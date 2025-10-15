## Artifacts provided
- firmware_elf.bin containing rootfs with a compiled ELF daemon at `/bin/demodaemon`
## Example checksums (from a session build)
- Example firmware_elf.bin SHA256: a06985593d16007638d3efbeca24d6fbabe5bdbc47b8803a57845df1c8146ead

> Note: If you built the artifact locally the checksum will differ unless you use the exact same files and build steps.

## Contents (what to expect inside the firmware tarball)
- `rootfs/`
  - `bin/demodaemon` — compiled ELF daemon (benign demo)
  - `etc/device.conf` — config file with intentionally insecure hardcoded credential (educational)
  - `www/index.html` — placeholder web UI
  - `README.txt` — short note about the sample

## Build & extraction commands (examples you can run locally)
```bash
# 1) Create a working directory
mkdir -p demo_firmware && cd demo_firmware

# 2) Create rootfs layout
mkdir -p rootfs/bin rootfs/etc rootfs/www

# 3) Create device.conf (example)
cat > rootfs/etc/device.conf <<'EOF'
# Demo device config
hostname=democam
admin_user=admin
admin_pass=admin123   # <-- insecure: hardcoded credential (educational sample)
wifi_ssid=DemoNet
EOF

# 4) Create a sample web UI
echo '<html><body><h1>DemoCam</h1><p>Web UI placeholder</p></body></html>' > rootfs/www/index.html

# 5) Create C daemon source (demodaemon.c)
cat > demodaemon.c <<'EOF'
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <time.h>
int main(int argc, char **argv){
    const char *device = "Device: DemoCam v1.0";
    const char *build = "Firmware build: 2025-10-14";
    const char *secret = "SOME_STATIC_TOKEN_ABC123";
    FILE *f = fopen("/tmp/demod.log","a");
    for(int i=0;i<3;i++){
        time_t t = time(NULL);
        if(f) fprintf(f, "%s | %s | time=%ld\n", device, build, (long)t);
        printf("%s\n%s\n", device, build);
        sleep(1);
    }
    if(f) fclose(f);
    printf("TOKEN:%s\n", secret);
    return 0;
}
EOF

# 6) Compile the daemon (try static if you want a standalone binary)
gcc demodaemon.c -o rootfs/bin/demodaemon        # or add -static if toolchain supports it

# 7) Make the firmware tarball (this demo uses tar.gz)
tar -czf firmware_elf.bin -C rootfs .

# 8) Verify checksum and contents
sha256sum firmware_elf.bin
tar -tzf firmware_elf.bin

# 9) Extract and test locally
mkdir -p extracted && tar -xzf firmware_elf.bin -C extracted
file extracted/bin/demodaemon
strings extracted/bin/demodaemon | grep -E 'DemoCam|SOME_STATIC_TOKEN|TOKEN' -n

# 10) Disassemble / inspect
objdump -d extracted/bin/demodaemon | head -n 50


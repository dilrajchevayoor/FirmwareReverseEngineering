- Details
- Filename: firmware.bin
- SHA256: 7cf092ae6532802711cec24f5864ca98cf5ba3fd5ab2b0ef14e200abc149223e
- Size: 804 bytes

Contains (tar listing):
- rootfs/README.txt
- rootfs/bin/app
- rootfs/etc/device.conf
- rootfs/www/index.html
  
Type: gzipped tar archive (simple firmware bundle) — treating firmware.bin as an example firmware image for learning extraction and analysis.
- Extraction: the package is a gzipped tar of a small rootfs/. You can extract locally with tar -xzf firmware.bin.
- Notable files:
rootfs/etc/device.conf — contains a hardcoded credential: admin_pass=admin123. This is intentionally insecure and included for analysis/discussion only.
rootfs/bin/app — a small shell "binary" (script) that prints device info; helpful to study startup scripts and plaintext strings.
rootfs/www/index.html — placeholder web UI file.

## What to look for in real firmware samples (and how this sample maps to that):
- File system type & layout (this sample uses a simple tarball; real firmware may contain JFFS2, UBIFS, SquashFS, etc.).
- Plaintext credentials or keys in config files (demonstrated by device.conf).
- Startup scripts and web UI files that may process user input (we include bin/app and www/index.html as safe examples).
- Binaries to analyze with strings, objdump/radare2/Ghidra (this sample’s app is a shell script — use strings on binaries in real cases).

## Suggested exercises

- Download and extract: tar -xzf firmware.bin and inspect rootfs/.
- Search for sensitive strings: grep -R "admin" -n rootfs/ to find hardcoded creds


create a slightly more realistic sample containing a compiled ELF binary (so you can practice objdump/strings/ghidra) — I can generate and compile one here and provide its firmware image.

Which of those would you like me to do next?

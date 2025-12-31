# VNPT Smartbox 2 (S805) – Debian / Armbian on Android Kernel

## Short Introduction

This project provides a **Debian / Armbian (Bullseye)** system for the **VNPT MyTV Smartbox 2** powered by **Amlogic S805 (Meson8b)**.  
Due to secure-boot restrictions on some boards, this setup **reuses the stock Android kernel (3.10.33)** and boots Debian via a custom initramfs and root filesystem.

The result is a **fully functional Armbian-like Debian 11 system using sysvinit**, suitable for real-world server workloads while remaining compatible with both secure and non-secure devices.

This project focuses on:
- Maximum compatibility with vendor bootloaders
- No need to unlock or replace U-Boot
- Safe installation to eMMC
- Easy rollback to Android if needed

---
## Installation Instructions (Full and Unstripped)

Please follow the steps **exactly**. No information in this guide is optional unless explicitly stated.

---

### 1. Extract the Files

- Use **7z (7-Zip)** to extract all provided archives.

---

### 2. Prepare the SD Card (Debian Image)

- Take the file:

  **`VNPTSMB2-S805-Debian-Bullseye-2.0.img`**

- Use **Balena Etcher** to burn this image to an **SD card**.
- Flash `env` partition only in the step 3.

---

### 3. Prepare the USB Drive (TWRP Files)

- Take the archive:

  **`TWRP-VNPTSMB2-Debian-Bullseye-2.0.7z`**

- After extracting, you will see **two items**:
  - `twrp-3.1.1.zip`
  - `TWRP` (folder)

- Copy **both** `twrp-3.1.1.zip` **and** the `TWRP` folder to a **USB drive**.

---

### 4. Load TWRP Using Stock Recovery

> Replacing the stock recovery is **impossible** on this device.  
> TWRP is loaded as an **application**, not flashed permanently.

Steps:

1. Press and hold the **reset key**
2. Plug in the **power cable**
3. When **stock recovery** appears:
   - Select **“Apply update from Udisk”**
   - Select **`twrp-3.1.1.zip`**

TWRP 3.1.1 will now be loaded as an application.

---

### 5. Restore Debian in TWRP

Once TWRP is loaded:

1. Select **`Restore`**
2. Select **`Select Storage`**
3. Choose the **Debian 11 backup** to restore

You will see **four partitions** available for restore:

---

#### Partition: `boot`

- Android boot partition that contains the kernel
- You **can deselect this partition** to try first

---

#### Partition: `env`

- U-Boot environment partition
f - It contains **custom code** to support booting from **SD card** and **internal eMMC**

**Boot behavior after installing this partition:**

- After installing this partition, the box will boot from **internal eMMC** (`/dev/data`) by default.
- When an SD card is plugged in, it will boot from **partition 2 of the SD card** (`/dev/mmcblk0p2`).
- If you want to boot from SD card,  flash this partition only.

---

#### Partition: `data`

- Debian **root filesystem**
- Debian will be installed and run from this partition

---

#### Partition: `cache`

- `twrp-3.1.1.zip` will be copied here
- After installing, you can load TWRP again by:
  - Entering stock recovery
  - Selecting **“Apply update from cache”**
  - Selecting **`twrp-3.1.1.zip`**

---

### 6. Final Notes

- This setup allows flexible booting between **internal eMMC** and **SD card**
- TWRP remains available as an application via the cache mechanism
- No permanent modification to stock recovery is required or possible
- Boot priority is controlled entirely by the `env` partition logic

### Restore Back to Android (Important)

If you want to **return to Android**, simply flash the following ROM via TWRP:
https://github.com/chieunhatnang/vnptsmb2-s805-debian/releases/download/vnptsmb2-s805-buster-3.10.33-2025.12/Android-ATV4-TWRP.zip

This will restore a clean Android ATV environment and overwrite the Debian setup safely.

---

## What Can You Do With It?

Despite running on an Android kernel and sysvninit, the system behaves like a normal Debian server and can be used for:

- LEMP stack (Nginx, MariaDB/MySQL, PHP)
- Node.js 18 (APIs, bots, background services)
- OpenVPN / WireGuard-Go
- Pi-hole (DNS filtering & ad blocking)
- FTP / SFTP server
- Transmission (torrent box)
- SSH remote management
- LAN file server / lightweight NAS
- Cron jobs, monitoring, and automation

Just remember : No systemd. Use `service xxx reload|start|stop|restart` instead of `systemd start|stop|restart xxx`.
---

## Further Reading & Technical Details

For deep technical explanations, boot flow analysis, secure-boot limitations, partition layout, and design decisions, please refer to the blog post:

**Using Android kernel to load Debian on VNPT MyTV Smartbox 2 (S805)**  
https://chieunhatnang.de/p/using-android-kernel-to-load-debian-on-vnpt-mytv-smartbox-2-s805/

This blog documents the full journey, including experiments, pitfalls, and rationale behind the final approach used in this repository.

---

## Disclaimer

This project is intended for **advanced users**.  
Flashing firmware always carries a risk. You are responsible for your own hardware.




# CH340 Driver Fix — Windows 11 (Code 31)

## The Problem

Arduino Nano uploads were hanging indefinitely in Arduino IDE despite the 
board being detected in Device Manager. The device showed a yellow warning 
triangle with Error Code 31: "Object Name already exists."

Root cause: Windows 11 Memory Integrity (Core Isolation) was silently 
blocking the CH340 clone driver, while multiple failed automated install 
attempts had corrupted the driver registry with conflicting versions.

This is a documented issue with cheap Arduino Nano clones using CH340/CH341 
USB-to-serial chips on Windows 11. Standard driver reinstall procedures do 
not resolve it. The fix requires manual registry purge and raw driver 
injection.

---

## The Fix — Exact Steps

### Phase 1 — Disable Memory Integrity

Windows 11 Core Isolation treats CH340 clone drivers as unverified and 
silently quarantines them during upload.

1. Open Windows Security
2. Go to Device Security → Core Isolation → Core Isolation Details
3. Turn **Memory Integrity OFF**
4. Restart laptop

Without this step, every driver install gets strangled at the kernel level 
regardless of what else you do.

---

### Phase 2 — Registry Purge via Official Installer

Multiple failed installs had left corrupted registry keys. Standard 
uninstall does not remove these.

1. Unplug Arduino Nano
2. Run `CH341SER.EXE` as Administrator
3. Click **UNINSTALL** — not Install. This purges corrupted registry keys 
   that normal uninstall misses
4. Restart laptop
5. Run `CH341SER.EXE` as Administrator again
6. Click **INSTALL** while Nano is still unplugged — this pre-loads the 
   clean driver into Windows memory before any conflict can occur

---

### Phase 3 — Identify and Delete the Ghost INF File

Even after registry purge, plugging the Nano back in re-triggered Code 31. 
Windows was loading a hidden corrupted configuration file.

1. Plug Nano back in
2. Open Device Manager → right-click CH340 → Properties
3. Go to Details tab → change dropdown to **Inf name**
4. Note the exact filename — in this case: `oem25.inf`
5. Open Command Prompt as Administrator
6. Run exactly:

```
pnputil /delete-driver oem25.inf /uninstall /force
```

This forcibly removes the ghost file from Windows driver store. The device 
will disappear from the Ports list entirely — this is expected and correct.

---

### Phase 4 — Manual Raw Driver Injection

With the system fully sanitised, Windows no longer recognises the device. 
Force-map it using raw driver files instead of the EXE installer.

1. Download the ZIP version of the CH341 driver from wch-ic.com/downloads
2. Extract the ZIP — do not run the EXE
3. In Device Manager, find the unrecognised device (may appear under 
   Other Devices)
4. Right-click → Update Driver → Browse my computer for drivers
5. Click **Let me pick from a list**
6. Click **Have Disk**
7. Point directly to the extracted `CH341SER.INF` file
8. Windows maps the hardware correctly as USB-SERIAL CH340

---

### Phase 5 — Arduino IDE Configuration

1. Open Arduino IDE
2. Tools → Port → select **COM8** (or whatever port was assigned)
3. Tools → Board → Arduino AVR Boards → **Arduino Nano**
4. Tools → Processor → **ATmega328P (Old Bootloader)**
   — cheap Nano clones use the old bootloader without exception
5. Upload any sketch — should complete in under 10 seconds

---

## Key Takeaway

When automated tools fail on Windows 11 driver conflicts:

- Identify the raw INF file causing the conflict — not just the device
- Use `pnputil` to forcibly remove it from the driver store
- Bypass the EXE installer entirely and use raw INF injection via Have Disk
- Memory Integrity must be disabled before any of this will hold

The standard advice of "uninstall and reinstall the driver" does not work 
for Code 31 on Windows 11 with CH340 chips. This sequence does.

---

## Environment

- OS: Windows 11 (Build 26200.7922)
- Chip: USB-SERIAL CH340 (clone Arduino Nano)
- Arduino IDE: 2.3.8
- Driver: CH341SER v3.9.2024.9 from wch.cn
- Final COM port assigned: COM8

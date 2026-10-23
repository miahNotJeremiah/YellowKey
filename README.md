# YellowKey
YellowKey Bitlocker Bypass Vulnerability

```markdown
# BitLocker Protection Bypass via FsTx in System Volume Information

> **Severity:** High — Local bypass of BitLocker at-rest protection guarantees.

---

## Summary

This document describes a reproducible method for bypassing BitLocker volume protection by leveraging the Windows Recovery Environment (WinRE) and a crafted `FsTx` directory placed within the `System Volume Information` folder.

Notably, the technique does **not** require an external storage device. The payload can be staged on the EFI system partition of the target disk itself, which significantly lowers the operational barrier and broadens the exposure surface.

---

## Prerequisites

| Requirement | Details |
|---|---|
| Filesystem | Windows-compatible (NTFS preferred; FAT32/exFAT believed functional) |
| Target | Windows system with BitLocker protection enabled |
| Access | Physical or logical access sufficient to modify the EFI system partition or attach a removable device |

---

## Reproduction Steps

### Step 1 — Stage the payload

Copy the `FsTx` folder verbatim to:

```
<TargetVolume>:\System Volume Information\FsTx
```

This can be accomplished either by:

- Writing to a removable USB device, **or**
- Directly modifying the EFI partition of the target disk (e.g., removing the disk, copying files into the EFI partition, and reinstalling it).

> ⚠️ The latter path demonstrates that the vulnerability does **not** depend on an external device being present at boot time, which materially elevates its severity.

### Step 2 — Attach the device (if applicable)

Insert the prepared USB device into the target Windows system while BitLocker protection is active.

### Step 3 — Enter the Windows Recovery Environment (WinRE)

Reboot into the WinRE agent — for example, by holding <kbd>SHIFT</kbd> while selecting the Restart option via the mouse.

### Step 4 — Trigger the shell-spawn condition

Upon clicking Restart:

1. Release <kbd>SHIFT</kbd>
2. Immediately hold <kbd>CTRL</kbd>
3. Keep <kbd>CTRL</kbd> depressed throughout the boot sequence

> 🚫 **Do not release the key.**

### Step 5 — Observe the result

If the sequence is performed correctly, a command shell is spawned with unrestricted access to the BitLocker-protected volume, effectively bypassing the volume's encryption boundary.

---

## Assessment

The ability to stage the payload directly on the EFI partition — without an attached removable device — indicates a **design-level weakness** in the WinRE / `System Volume Information` handling path rather than a configuration-dependent issue.

This should be treated as a **high-severity local bypass** of BitLocker's at-rest protection guarantees.

---

## Disclaimer

> This material is provided for **security research and defensive purposes only**. Do not use against systems you do not own or have explicit authorization to test.


<img width="1370" height="777" alt="shell" src="https://github.com/user-attachments/assets/eda6c823-4a6b-4aec-bad2-b9afad640dd6" />



Credits to [@sweetsky123](https://github.com/sweetsky123) for these references, & MORSE, MSTIC and Microsoft GHOST for making this public disclosure possible 

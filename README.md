# Huananzhi X79 Deluxe V3.1 - all-core overclocking BIOS patch (Ivy Bridge-E)

A patched version of the official 2020-08-10 BIOS for the Huananzhi X79 Deluxe V3.1. It removes three restrictions that stop unlocked Ivy Bridge-E CPUs (E5-1650 v2, E5-1660 v2, E5-1680 v2, i7-4930K, i7-4960X) from overclocking on this board.

Result on a Xeon E5-1680 v2: all-core turbo went from a hard 37x wall (3.7 GHz, whatever was set in the BIOS) to 39x (3.9 GHz) at stock voltage. Higher ratios apply too, but need more voltage than this board gives by default.

## Which board this is for

| | |
|---|---|
| Board | Huananzhi X79 Deluxe V3.1 |
| Audio codec | Realtek ALC887 |
| Super I/O | Nuvoton NCT5532D |
| Stock BIOS | 20-8-10 (file x792.49v3.1 20-8-10 from huananzhi.com, 6 MB BIOS region) |

**Do not flash the ROM on any other board or revision.** Later X79 Deluxe V3.1 boards with the ALC897 codec use a different BIOS (22-08-01). If your board is different, apply the patch file to your own BIOS instead (see below) and check that every pattern is found exactly once.

## What the patches change

All three are in the PowerManagement DXE module (GUID 8C783970-F02A-4A4D-AF09-8797A51EEC8D).

| Patch | Problem in the stock BIOS | Change |
|---|---|---|
| 1 | On Ivy Bridge-E (CPUID 306Ex) the BIOS clears the overclocking bins in MSR 0x194 and sets the OC lock at every boot | The routine returns immediately |
| 2 | Turbo ratios are capped at base ratio + 7 (37x on an E5-1680 v2), and anything higher is silently not written | Cap removed |
| 3 | The current limit in MSR 0x601 is raised to maximum only for Sandy Bridge-E. Ivy Bridge-E is left at the 164 A default and then locked, which caps all-core turbo | Ivy Bridge-E takes the same raise-to-maximum path |

Patch 3 is the one that removed the all-core wall. Patches 1 and 2 alone made no visible difference.

## Files

**X79OC2_patches.txt** is the patch file for UEFIPatch 0.28 (from the UEFITool project). Usage: `UEFIPatch X79V31.ROM X79OC2_patches.txt -o X79OC2.ROM`

**X79OC2.ROM** is the ready-made patched BIOS for the exact board above. SHA-256: `0919F47B7F6BE1BC16FEEA32D820ECC95C6DC7AF49385F7CF272CBC05B64FA95`

## Flashing

Boot a FreeDOS USB stick with AFUDOS on it. First make a backup: `AFUDOS BACKUP.ROM /O`. Then flash: `AFUDOS X79OC2.ROM /P /B /N`. Power off and clear CMOS afterwards.

In the BIOS, under Advanced, CPU Power Management Configuration: set Power Technology to Custom, EIST and Turbo Mode to Enabled, the long and short duration power limits to about 200 and 240 W, and all eight Core Ratio Limits to the ratio you want. Start one step above stock and go up one step at a time.

## Warnings

Tested on one board with one CPU. Flashing a modified BIOS can leave the board unable to boot, and this board has a single BIOS chip, so have a CH341A programmer and your backup ready.

The board has no real Vcore control, only VID Override for Max Turbo Ratio. On the test system 39x on all cores was stable at stock voltage and 40x was not.

Set turbo ratios only in the BIOS. Writing them from Windows (Intel XTU, RWEverything) caused a blue screen every time on this board.

Patch 3 removes the limit that was holding all-core current down, so the VRM runs hotter at higher ratios. Keep air moving over the VRM heatsink.

Use at your own risk.

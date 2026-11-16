# Kodak Charmera firmware: build v012 (Jan 12 2026)

Newer firmware that I believe shipped on later Kodak Charmera keychain cameras, which use an OmniVision OV9732 sensor. Copy kept here so owners can **study and repair their own cameras**, above all OV9732 cameras left unable to take pictures after being flashed with an older build.

## No rights claimed

- **We claim no copyright, ownership, license or other right** in this firmware. All rights belong to their respective owners: the chip vendor (GeneralPlus) and the maker of the camera sold under the Kodak brand. "Kodak" is a trademark of its owner. This repository is **not affiliated with or endorsed by** Kodak, GeneralPlus or the camera's maker.
- The image is offered **only for reference, interoperability and repair**, free of charge. It is not sold or licensed, and nothing here grants any right to use it for anything else.
- **Takedown:** if you hold rights in this file and want it removed, open an issue. It will be taken down promptly.
- Provided as is, with no warranty. Flashing firmware can leave a camera unable to start. Mess with this at your own risk.

## File Details

| Field | Value |
| --- | --- |
| File | `charmera-v012-2026-01-12-clean.bin` |
| Size | 2,097,152 bytes (full 2 MiB chip image) |
| SHA-256 | `63e396846f949600c2c1881920860821f2fcd70a70ac79ca54eebaee4a0ccb1c` |
| Build | `v012`, compiled Jan 12 2026 |
| Version stamp | `202505201029`, build string `JH5408  20250520` |
| Target Sensor | OmniVision OV9732 (16-bit registers, bus address 0x6C) |
| Initial Source | read from a never-flashed camera's SPI flash chip on 2026-11-13; three reads, all identical |

## Which Charmera does this work for?

- The firmware version needed is driven by which image sensor your camera has. This build drives an **OmniVision OV9732** sensor only. I believe this shipped on a later release wave, but can't confirm.
- This is the build OV9732 cameras shipped with. OV9732 cameras flashed with `v006` or `v008_test` start and show their menus, but freeze when Photo or Video mode is opened.
- WARNING: On cameras with any other sensor, including the **SuperPix SP140A-class**, this build is *not* expected to work, since it only drives the OV9732. This has not been tested. Those cameras need build `v006` or `v008_test` (see charmera-firmware-v006 and charmera-firmware-v008_test).
- The version stamp doesn't tell builds apart: `v006`, `v008_test` and `v012` all report `202505201029`. The build name is the factory's source-folder name, left inside the image in its source paths, and the compile date differs.
- **Hypothetical method to determine Charmera version without opening the camera:** look at a photo the camera took on its original firmware. `v012` writes a bare 624-byte JPEG header with no EXIF block. `v006` and `v008_test` write a 1,424-byte header with an EXIF block that names "Generalplus" / "CBB3".
  - NOTE: I only have the v012 OmniVision version of the camera. Its photos match the v012 header, but the other side has not been tested.

## Source

Original read SHA-256: `08a7d993996b93cf2468fe08d70a0e77d5e1a9d6ff16bf647f5c7a0edf5d85ce`

The firmware itself is **unchanged**: bytes `0x000000..0x170000` (boot header, code, data, resource pack) are byte-for-byte as read. Only the areas holding the owner's own data were reset:

- **Settings bank, `0x170000..0x17F000`:** only the factory settings record is kept, moved to sector 0. It is the same in every known dump. The later settings history is erased to `0xFF`.
- **Internal photo slots, `0x180000..0x1CB000`:** erased to `0xFF`. These held the owner's photos.

## How to Use

- **Never put this 2 MiB file on the camera's card.** The updater's final erase wraps around and wipes the start of the chip.
- Options:
  - **Flash from a card:** first make a `0x1E0000`-byte update file from the image with a tool that knows the format. The updater skips a file whose name carries the stamp the camera is already running, and every known factory build runs `202505201029`, so give the file's name a different 12-digit stamp.
  - **Chip clip and programmer:** Writing the whole image straight to the chip with a clip and a programmer also works but will require opening the camera.
- The only secure method is to **read your own camera's chip** before flashing anything. That read is the **only certain way to recover a bad flash**.

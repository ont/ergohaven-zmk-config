# Ergohaven ZMK Config

This repository builds ZMK firmware for several Ergohaven keyboards through GitHub Actions. The build matrix is in `build.yaml`; keymaps and keyboard config files are in `config/`.

## Edit Keymaps With Keymap Editor

Nick Coutsos' Keymap Editor is a web UI for editing ZMK `.keymap` files:

<https://nickcoutsos.github.io/keymap-editor/>

The editor can load keymaps from GitHub repositories, the clipboard, or the local filesystem. GitHub mode can commit changes back to this repository; local/clipboard modes only edit the keymap content and still require you to commit, push, build, download, and flash firmware yourself.

Recommended GitHub workflow:

1. Open <https://nickcoutsos.github.io/keymap-editor/>.
2. Sign in with GitHub and allow access to this repository.
3. Select this repository: `ont/ergohaven-zmk-config`.
4. Open the Imperial44 keymap you want to edit:
   - `config/imperial44.keymap` for the default Imperial44 builds.
   - `config/imperial44_ruen.keymap` for the RU/EN Imperial44 builds.
5. Make key changes in the visual editor.
6. Save/commit changes from the editor.
7. Wait for GitHub Actions to finish building new firmware artifacts.
8. Download and flash the matching artifact from the **Actions** run.

Changes to `config/imperial44.keymap` affect these builds:

- `imperial44_left`
- `imperial44_right`
- `imperial44_qube`
- `imperial44_left_qube`

Changes to `config/imperial44_ruen.keymap` affect these builds:

- `imperial44_left_ruen`
- `imperial44_qube_ruen`

This repository enables ZMK Studio on central Imperial44 builds with the `studio-rpc-usb-uart` snippet and `CONFIG_ZMK_STUDIO=y`. If you use ZMK Studio to change the keymap at runtime, later changes to the `.keymap` file may not show up until you restore stock settings from ZMK Studio.

References:

- Keymap Editor project: <https://github.com/nickcoutsos/keymap-editor>
- ZMK keymap documentation: <https://zmk.dev/docs/keymaps>
- ZMK Studio keymap behavior: <https://zmk.dev/docs/features/studio#keymap-changes>

## Flash Imperial44 Firmware

GitHub Actions produces one artifact per build entry. For a normal Imperial44 split, use:

- `artifact-imperial44_left-ergohaven-zmk` for the left half.
- `artifact-imperial44_right-ergohaven-zmk` for the right half.

For the RU/EN keymap builds, use:

- `artifact-imperial44_left_ruen-ergohaven-zmk` for the left half with `config/imperial44_ruen.keymap`.
- There is no separate `imperial44_right_ruen` artifact; the right half does not contain the central keymap logic.

For a Qube/dongle setup, use:

- `artifact-imperial44_qube-ergohaven-zmk` for the Qube/dongle central.
- `artifact-imperial44_left_qube-ergohaven-zmk` for the left keyboard half.
- `artifact-imperial44_qube_ruen-ergohaven-zmk` for the Qube/dongle central with the RU/EN keymap.

To flash `artifact-imperial44_left-ergohaven-zmk` to the left half:

1. Open this repository on GitHub.
2. Go to **Actions**.
3. Open the latest successful workflow run.
4. Download `artifact-imperial44_left-ergohaven-zmk`.
5. Unzip the downloaded artifact. It should contain a file like `imperial44_left-ergohaven-zmk.uf2`.
6. Connect only the left half of the keyboard by USB.
7. Put the left half into bootloader mode. Usually this is done by double-clicking the reset button on the keyboard/controller.
8. Wait for the keyboard to appear as a USB storage device, for example `NRF52BOOT`, `NICENANO`, or a similar bootloader drive.
9. Copy `imperial44_left-ergohaven-zmk.uf2` to the root of that drive.
10. Wait for the drive to disconnect and the keyboard to reboot.

For a normal split keyboard, the left half is the central side. It sends keyboard output to the host and runs the keymap logic. The right half is a peripheral side; flash `artifact-imperial44_right-ergohaven-zmk` to it when its firmware also needs to be updated.

Do not flash the normal `imperial44_left` artifact to a Qube/dongle setup. In Qube mode, flash `imperial44_qube` to the dongle and `imperial44_left_qube` to the left half.

References:

- ZMK firmware download and UF2 flashing: <https://zmk.dev/docs/user-setup#install-the-firmware>
- ZMK split keyboard central/peripheral roles: <https://zmk.dev/docs/features/split-keyboards#central-and-peripheral-roles>

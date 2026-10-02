# Mobula 7 Betaflight setup

Configuration for a Mobula 7 1S with the **HAMO CRAZYBEEF4SX1280** flight controller, based on the local Betaflight 2026.6.2 exports. The ready to paste CLI file is [config/mobula7-setup.cli](config/mobula7-setup.cli).

## Switches and video

| Function | Radio channel | Active position | Betaflight CLI |
| --- | --- | --- | --- |
| ARM | AUX1 | High, 1700–2100 µs | `aux 0 0 0 1700 2100 0 0` |
| ANGLE | AUX3 | Low, 900–1300 µs | `aux 1 1 2 900 1300 0 0` |
| HORIZON | AUX3 | Middle, 1300–1700 µs | `aux 2 2 2 1300 1700 0 0` |
| ACRO | AUX3 | High, above 1700 µs | No mode range; default when neither self-leveling mode is active |

Set the radio's ARM switch to output AUX1 and its three-position flight mode switch to output AUX3. These are **channel assignments**, not physical switch names; your transmitter may call the switches something else. Keep ARM low while setting up. At the 1300 µs boundary, check the selected mode in Betaflight and adjust the ranges if your radio output sits on an edge.

VTX is set to **Raceband R8, 5917 MHz, power level 3 (label 25)**. The CLI includes all six bands and eight channels from the saved VTX table. The power labels are the source configuration's labels; verify actual output and legal frequencies for your hardware and location. Match goggles to R8 before powering up for a test.

## Apply

1. Remove propellers. In Betaflight Configurator, connect and save a fresh `diff all` backup for this exact flight controller.
2. Confirm the target is `CRAZYBEEF4SX1280` and firmware is compatible with the saved 2026.6.2 CLI syntax. Do not paste this on another board.
3. Open **CLI**, paste the contents of `config/mobula7-setup.cli`, and wait for the `save` command to reboot the controller.
4. In **Receiver**, confirm AUX1 goes low/high with the arm switch and AUX3 goes low/middle/high with the mode switch. In **Modes**, confirm ARM, ANGLE and HORIZON activate only in the expected positions. Check that ACRO is selected at AUX3 high.
5. With propellers still removed, check motor direction, receiver failsafe, arming behavior, and the VTX channel in Betaflight. Refit propellers only after these checks.

This file is an **incremental setup**: it does not run `defaults nosave` or restore unrelated calibration, PID, radio binding, motor, or OSD values from an old export. Back up and check those settings separately after any firmware flash. The saved `expresslrs_uid` and board `mcu_id` are intentionally omitted from the public setup.

## Source notes

- VTX table, R8 channel and power level: `mobula7-vtx-restore.txt` and `BTFL_cli_backup_20261001_163829_CRAZYBEEF4SX1280FINAL2.txt`.
- AUX1 ARM and AUX3 ANGLE/HORIZON mapping: `modesaux.txt` (the newer mode mapping supplied in this folder).
- Earlier exports differ: the September 2026 backup uses AUX2 for flight modes, and its ARM threshold starts at 1850 µs. This setup follows `modesaux.txt`; confirm the radio's actual channel output before flight.

## Publish to GitHub

From the parent `mobula7` directory, after reviewing the files:

```bash
cd mobula_7
git add .gitignore README.md config/mobula7-setup.cli
git commit -m "Add Mobula 7 VTX and AUX setup"
gh repo create mobula_7 --private --source=. --remote=origin --push
```

The `gh` command requires [GitHub CLI](https://cli.github.com/) and `gh auth login`. To use an existing empty GitHub repository instead of creating one:

```bash
git remote add origin https://github.com/YOUR_USERNAME/mobula_7.git
git push -u origin main
```

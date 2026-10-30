# IBM Model M Silver Label (1986)

Custom Vial firmware and test tools for a 1986 silver-label IBM Model M that has been converted to USB with a **Model H Remix 2.0** controller board.

> **Important:** this firmware is only for the **Model H / Model H Remix** controller, which uses an STM32F103. It will **not** work on:
> - the original IBM controller
> - a Model X / nice!nano (ZMK) board
> - any other converter
>
> Check which board is inside your keyboard before flashing.

## Hardware

| | |
|---|---|
| **Keyboard** | IBM Model M Enhanced Keyboard, part no. **1392120**, silver label, 1986, 101-key ANSI |
| **Controller PCB** | **Model H Remix 2.0** by [NilsFC](https://github.com/NilsFC/Model-H-Rmx). It replaces the original IBM controller board and is a remix of the open-source [Model H](https://github.com/jberclaz/modelh) design. |
| **MCU** | STM32F103 |
| **Connection** | USB only, through the original SDL connector (no Bluetooth) |
| **USB IDs** | `BDE0:B155` normally, `6B62:0000` in the DFU bootloader |
| **Firmware base** | [vial-qmk](https://github.com/vial-kb/vial-qmk), target `ibm/model_m/modelh`, commit `dd43959a` |

## What's changed from stock Vial firmware

| Change | Why |
|---|---|
| **Right Alt → Right Windows key** (`KC_RGUI`) | The 1986 layout has no Windows key. |
| **Debounce 25 ms, `asym_eager_defer_pk`**, applied to every key | The ageing membrane bounced for up to ~17 ms on release, which caused double letters. Presses still register instantly, so typing doesn't feel slower. |

Both settings are stored on the controller, so they work on any computer with no software installed.

## Repository layout

```
firmware/
  ibm_model_m_modelh_vial_debounce.bin   QMK build output (carries a DFU suffix with ID 1eaf:0003)
  modelh_flash_nosuffix.bin              Same image with the suffix removed: the file that was flashed
keymap/                                  QMK keymap "vial_debounce" (config.h, rules.mk, keymap.c, vial.json)
tools/key-test.html                      Browser key tester + typing test that detects chatter / double letters
```

SHA-256:
- `ibm_model_m_modelh_vial_debounce.bin`: `492b2ae4543bebd16c62de7a398b1230d261d9f07505ea699c8d88bba7900cb8`
- `modelh_flash_nosuffix.bin`: `b690b67328146d5112ae90900753f05f409ad18b40a79a421368193c17af82c8`

## Flashing (Windows)

1. Install [QMK MSYS](https://msys.qmk.fm/), which includes `dfu-util`.
2. Unplug the keyboard. **Hold Esc while plugging it in** to enter the bootloader, which appears as USB `6B62:0000`.
3. The first time only: use [Zadig](https://zadig.akeo.ie/) to install the **WinUSB** driver for that device.
4. Flash the suffix-free image:
   ```
   dfu-util -a 0 -D firmware/modelh_flash_nosuffix.bin
   ```
5. Replug the keyboard. It comes back as `BDE0:B155`.

> Flashing any other firmware, such as the stock Model H Remix Vial `.bin`, removes the debounce fix and the Right Alt remap.

**Vial unlock combo:** Esc + Enter + Right Alt (the key that now sends Windows).

Flashing firmware is done at your own risk.

## Rebuilding from source

```
cp -r keymap <vial-qmk>/keyboards/ibm/model_m/modelh/keymaps/vial_debounce
cd <vial-qmk>
make ibm/model_m/modelh:vial_debounce
dfu-suffix -D ibm_model_m_modelh_vial_debounce.bin   # strip the 1eaf:0003 suffix before flashing
```

## Test history

Results from typing passages in `tools/key-test.html`:

| Stage | Result |
|---|---|
| Stock firmware (5 ms debounce) | **O** doubled 11 ms after release, **X** doubled 17 ms after release: contact bounce |
| 25 ms `asym_eager_defer_pk` | Bounce gone, but **O** still doubled 4–10 ms after the delayed release: a mechanical rebound in the O key |
| O key's spring and hammer swapped with another key | **0** O doubles. One B double at 103 ms was a human tap, not chatter. |

If another key starts doubling, check the key that received O's old spring first.
In the tester, a double more than ~100 ms after release is usually a real keypress, not chatter.

## Credits

This repository is a small personal configuration built on other people's work:

- **[Model H Remix](https://github.com/NilsFC/Model-H-Rmx)** by [NilsFC](https://github.com/NilsFC): the controller PCB installed in this keyboard.
- **[Model H](https://github.com/jberclaz/modelh)** by [jberclaz](https://github.com/jberclaz): the original open-source replacement controller that the Remix is based on.
- **QMK keyboard support for `ibm/model_m/modelh`**: maintained by [John Hawthorn](https://github.com/jhawthorn). The `keymap.c` here is derived from his work (© 2020 John Hawthorn, GPL-2.0-or-later).
- **[QMK Firmware](https://github.com/qmk/qmk_firmware)** and **[Vial](https://github.com/vial-kb/vial-qmk)**: the firmware framework and the live-remapping fork this build uses (GPL).
- **[QMK MSYS](https://msys.qmk.fm/)**, **[dfu-util](https://dfu-util.sourceforge.net/)** and **[Zadig](https://zadig.akeo.ie/)** (by Pete Batard / Akeo): build, flashing and driver tools.
- The key tester page loads the **Archivo** and **JetBrains Mono** fonts from Google Fonts (SIL Open Font License).

My own changes are the debounce settings, the Right Alt remap, the key tester page and this documentation.

IBM and Model M are trademarks of International Business Machines Corporation. This project is not affiliated with or endorsed by IBM, NilsFC, the Model H authors, QMK or Vial.

## License

- **Firmware source and binaries** (`keymap/`, `firmware/`): GPL-2.0-or-later, the same as the QMK/Vial code they are built from. See [LICENSE](LICENSE).
- **Key tester and documentation** (`tools/`, `README.md`): also released under GPL-2.0-or-later to keep the repository under one license.

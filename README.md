# IBM Model M Silver Label (1986)

Custom firmware and test tools for an IBM Model M **1392120** (silver label, 1986), fitted with a **Model H Rmx v2.0 (NilsFC)** controller running [Vial](https://get.vial.today/).

## What's changed from stock Vial firmware

| Change | Why |
|---|---|
| **Right Alt → Right Windows key** (`KC_RGUI`) | The 1986 layout has no Windows key. |
| **Debounce 25 ms, `asym_eager_defer_pk`**, applied to every key | The ageing membrane bounced for up to ~17 ms on release, which caused double letters. The key press still registers instantly, so typing doesn't feel slower. |

Both settings are stored in the controller, so they work on any computer with no software installed.

## Repository layout

```
firmware/
  ibm_model_m_modelh_vial_debounce.bin   QMK build output (has a DFU suffix, ID 1eaf:0003)
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

> Flashing any other firmware, such as the stock Model H Rmx Vial `.bin`, removes the debounce fix and the Right Alt remap.

**Vial unlock combo:** Esc + Enter + Right Alt (the key that now sends Windows).

## Rebuilding from source

Built from [vial-qmk](https://github.com/vial-kb/vial-qmk) at commit `dd43959a`.

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

## License

The keymap is derived from QMK/Vial and is licensed under GPL-2.0-or-later.

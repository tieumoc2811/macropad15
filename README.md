# Macropad15 - 15 phim + 3 encoder - Pro Micro RP2040

Firmware ZMK cho ban phim 15 phim + 3 encoder EC11 (co nut nhan), dung module **Pro Micro RP2040**
(board ZMK `sparkfun_pro_micro_rp2040//zmk`). Chi USB. Tinh nang giong ban Numpad21 tham khao:
layer cycle (co nut rieng), Nokia multi-tap, FN layer, ZMK Studio, bootloader/reset. Khong dung LED.

## So do phim

```
c0r0  c0r1  c0r2  c0r3  c1r0
c1r1  c1r2  c1r3  c2r0  c2r1
c2r2  c2r3  c3r0  c3r1  c3r2
c3r3 = nut LAYER CYCLE (them moi)
 [ENC1 push]  [ENC2 push]  [ENC3 push]
```

Ma tran 4 cot (C0-C3) x 5 hang (R0-R4), diode **col2row** (vach den diode ve phia hang).
Hang R4 la nut nhan 3 encoder: c0r4 = ENC1, c1r4 = ENC2, c2r4 = ENC3. Vi tri c3r3 la nut layer cycle (dung o trong cua ma tran, khong ton them chan).
Nen dung 1N4148 cho moi cong tac va moi nut nhan encoder (19 diode).

## Gan chan (con 3 chan trong: D9, D10, D14)

| Tin hieu | Nhan | GPIO | Tin hieu | Nhan | GPIO |
|---|---|---|---|---|---|
| C0 | D7  | GP7  | R0 | D1 | GP1 |
| C1 | D8  | GP8  | R1 | D0 | GP0 |
| C2 | D21 | GP29 | R2 | D2 | GP2 |
| C3 | D20 | GP28 | R3 | D3 | GP3 |
|    |     |      | R4 | D4 | GP4 |
| ENC1 A | D5  | GP5  | ENC1 B | D6  | GP6  |
| ENC2 A | D18 | GP26 | ENC2 B | D19 | GP27 |
| ENC3 A | D15 | GP22 | ENC3 B | D16 | GP23 |

- Encoder: chan A, B noi vao GPIO; chan giua (common) noi GND. Firmware tu bat pull-up.
- Nut nhan encoder (2 chan con lai): 1 chan noi cot, 1 chan noi qua diode vao R4 (nhu 1 phim thuong).

## Layer (layer 0..2 doi bang &layer_cycle, giu FN de vao layer 3)

| Layer | Noi dung |
|---|---|
| 0 Numpad | 7 8 9 / 4 5 6 / 1 2 3 0 . ENTER, `-` (giu = `/`), `+` (giu = `*`) |
| 1 Media | prev/play/next, calc, Home/End/mui ten/PgUp/PgDn, vol |
| 2 Nokia | nhap chu kieu Nokia multi-tap, Caps Word, Shift sticky |
| 3 FN | Studio unlock, reset, bootloader |

Encoder (layer 0 va 2): ENC1 xoay = am luong, nhan = mute; ENC2 xoay = trai/phai, nhan = Backspace;
ENC3 xoay = len/xuong, nhan = Tab. Layer Media: ENC2 = bai truoc/sau, ENC3 = PgUp/PgDn.

Nut c3r3 la `&layer_cycle` o tat ca cac layer. ENC3 nhan = Tab (Numpad) / Enter (Media).

FN layer: hang 1 cot 4 = Studio unlock, hang 2 cot 4 = reset, hang 2 cot 5 = bootloader.

## Build

1. Push repo len GitHub, tab **Actions** se build ra file `.uf2` (firmware + settings_reset).
2. Giu nut BOOT khi cam USB (hoac FN + phim `bootloader`), o RPI-RP2 xuat hien, tha file `.uf2` vao.
3. Neu bi loi luu tru: nap `settings_reset`, roi nap lai firmware chinh.

Build local: `west build -s app -b sparkfun_pro_micro_rp2040//zmk -S studio-rpc-usb-uart -- -DSHIELD=macropad15 -DZMK_CONFIG=<repo>/config -DZMK_EXTRA_MODULES=<repo>`.

## Chinh sua

- Keymap: `config/macropad15.keymap` (chieu xoay encoder nam trong `sensor-bindings` cua tung layer).
- Xoay encoder bi nguoc: doi cap CW/CCW trong `&inc_dec_kp`, hoac doi `a-gpios`/`b-gpios`.
- 1 nac nhay 2 lan: doi `resolution = <4>` thanh `<2>` trong overlay.
- ZMK Studio dung de doi phim; neu Studio khong sua duoc encoder thi sua trong file keymap.

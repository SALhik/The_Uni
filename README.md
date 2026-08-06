# The Uni Board
The uni-body split ortholinear keyboard for stenography, or the Uni for short. By Peter C Park. I used KiCad Nightly Release so the kicad files are not compatible with older version.

Buy it now at [StenoKeyboards.com](https://www.stenokeyboards.com/).

> **This checkout is a modified copy, not the upstream board.** The Pro Micro
> daughterboard has been replaced with an RP2040 module (Adafruit KB2040), which
> also moves the board to native USB-C. See
> [About this modification](#about-this-modification) for what changed, what was
> verified, and what was not. It is not affiliated with or endorsed by
> StenoKeyboards, and it is not any official Uni revision.

![pcb](https://github.com/petercpark/The_Uni/blob/main/Pics/uni-v2-render.png?raw=true)
![layout](https://github.com/petercpark/The_Uni/blob/main/Pics/layout.png?raw=true)

## Product
If you are going DIY with this, here are the materials you need:
* The Uni PCB x 1 (diodes will come pre-assembled)
* Adafruit KB2040 x 1
* Switches x 28
* Keycaps x 28
* Willingness to solder x 1

If you want me to assemble here are the materials you need:
* money

## Microcontroller
This copy of the board is built around the **Adafruit KB2040** — an RP2040 module
in the Pro Micro form factor (33.02 x 17.78 mm) with 8 MB of QSPI flash and a
native USB-C connector.

Any module with the same 24-pin, 2.54 mm, 15.24 mm-row through-hole pattern will
physically fit, including the original Pro Micro and the Elite-C. **But the pin
functions are not interchangeable** — the schematic and silkscreen now name
RP2040 GPIOs, and pad 21 is treated as a 3.3 V regulator output rather than a
5 V rail. If you populate an AVR Pro Micro instead, the matrix still works
electrically, but use the original firmware pin names (see the table below).

## Switches
Nearly any switch is compatible with the v2. The board has a footprint that accomodates for mx, alps, and choc v1 style switches.

## Keycaps
The best keycaps to use are the OEM R3 keycaps with one of the rows inverted to reduce the gap in the middle.
I used to recommend 3d printing your caps but OEM R3 caps are better. There are other flat keycaps for mx style switches at pimpmykeyboard.com called f10 keycaps. There is also a slightly less flatter version called the G20 keycaps on their store and people have used them for steno.
![alt text](https://github.com/petercpark/The_Uni/blob/main/Pics/3d-printed-keycaps.jpg?raw=true)

## Soldering
All you have to solder are the switches and the KB2040 if you get the pcb with the diodes assembled. If not you'll have to solder the smd diodes yourself. Check the BOM file for the necessary materials.

The KB2040 mounts through-hole, in the same 24 holes the Pro Micro used. It
cannot be surface-mounted on this board — see [Castellated pads](#castellated-pads)
below.

`jlcpcb/assembly/` covers only the parts JLCPCB machine-assembles: the 28 SOD-123
diodes and the reset switch. The MCU module has never been listed there and still
isn't — you supply and solder it yourself, exactly as with the Pro Micro.

## About this modification

An independent modification of the upstream v2 hardware, moving it toward the
capabilities publicly described for later revisions. It was implemented from
scratch against public sources — Adafruit's own KB2040 design files for the
module, and the existing v2 KiCad files for everything else. No proprietary or
unreleased StenoKeyboards design files were used, referenced, or copied.

### What changed

| Area | Before | After |
| --- | --- | --- |
| MCU | Pro Micro (ATmega32U4) | Adafruit KB2040 (RP2040, 8 MB flash) |
| USB | micro-B | native USB-C |
| Logic rail | `+5V` | `+3V3` |
| Symbol | `keebio:ProMicro` | `uni-symbols:KB2040` |
| Footprint | `Keebio-Parts:ArduinoProMicro` | `uni-footprints:Adafruit_KB2040` |

Deliberately **unchanged**: key layout, switch matrix, diode topology, board
outline, mounting holes, and the MX/Alps/Choc hybrid switch footprint that gives
the board its spring-swap compatibility. The 24 through-hole pads keep their
exact original coordinates, drill sizes and nets, so no track was rerouted. This
was verified by exporting the netlist before and after: 50 nets and 138 nodes in
both, with an identical connectivity graph.

### GPIO map

Every matrix net kept its physical pad, so this table is purely a renaming. The
"Pro Micro" column is the AVR pin the upstream QMK `config.h` refers to.

| Net | Pad | Pro Micro | KB2040 |
| --- | --- | --- | --- |
| `row0` | 20 | F4 | GP29 |
| `row1` | 14 | B2 | GP19 |
| `row2` | 13 | B6 | GP10 |
| `col0` | 19 | F5 | GP28 |
| `col1` | 18 | F6 | GP27 |
| `col2` | 17 | F7 | GP26 |
| `col3` | 16 | B1 | GP18 |
| `col4` | 15 | B3 | GP20 |
| `col5` | 12 | B5 | GP9 |
| `col6` | 11 | B4 | GP8 |
| `col7` | 10 | E6 | GP7 |
| `col8` | 9 | D7 | GP6 |
| `col9` | 8 | C6 | GP5 |
| `col10` | 7 | D4 | GP4 |

Matrix is 3 rows x 11 columns, `COL2ROW`, 28 keys. Pads 1, 2, 5 and 6 (GP0,
GP1, GP2, GP3) are unused, as they were on the Pro Micro. Pad 24 (`RAW`) is
unconnected; the KB2040 self-powers from USB through its own diode and
regulator.

**The firmware in `Firmware/` has not been ported** and still targets the
ATmega32U4. Adapting it is separate work.

### Silkscreen

The GPIO labels on the back are bare numbers (`4`, `29`) rather than `GP4`
style. The pads are on a 2.54 mm pitch and this board's setup enforces a 0.8 mm
minimum text height, at which four characters do not fit between adjacent pads.

### Castellated pads

The KB2040 has castellated edge pads for reflow mounting; **they are not placed
on this board, and the module must be through-hole soldered.** This was tested,
not assumed: existing B.Cu matrix tracks already run through the channel where
the castellations would land, and pads at every size tried — down to 1.0 x
1.0 mm, below the point of being reliably solderable — produced 10 net-to-net
copper shorts, because each castellation sits on top of its neighbour's track.
Fitting them would require rerouting the matrix.

### Case and plate

The switch plate (`Case/switchplate/`) needed **no change**: switches are on
`F.Cu` and the module is on `B.Cu`, so they are on opposite faces, and the
module lands in a keyless channel that overlaps no cutout. Its 28 cutouts were
re-derived from the PCB switch coordinates and match exactly, at 14.00 x
14.00 mm. The back plate is likewise unchanged and still fully encloses the
module — the KB2040's onboard RESET and BOOT buttons are not externally
accessible, the same situation as the v2's own reset switch.

**The USB-C connector is wider than the micro-B it replaces.** Measured from
Adafruit's board file:

| | micro-B | USB-C | Delta |
| --- | --- | --- | --- |
| Connector width | 7.370 mm | 8.940 mm | +1.570 (about +0.79 per side) |
| Face inset from board edge | 0.145 mm | 0.462 mm | +0.317 deeper |

On the width alone this does **not** require a case change. The bezel in
`Case/3dp-full-case/full-case-bezels.kicad_pcb` already has a 17.462 mm notch in
its top edge (x 133.350 to 150.812), centred at x 142.081 — the module centre —
so an 8.940 mm connector clears it by about 4.26 mm a side. Only the width was
checked. The opening's height, whether a plug's overmold clears the recessed
board edge, and the printed `.stl` / `.FCStd` models were not measured.

### Fabrication outputs

`jlcpcb/gerber/` and `schematic.pdf` have been re-exported from the modified
design. The originals were plotted with KiCad 6.0.0 in 2022 and these with
KiCad 10.0.5, so the newer plotter re-renders mask and silkscreen and the file
diff is much larger than the design change. To separate the two, the *upstream*
board was exported with the same tool and settings and diffed against the new
export. Everything below is that comparison, not a claim about the raw diff:

| Layer | Change attributable to this modification |
| --- | --- |
| F.Cu, B.Cu | **none** — 38 differing lines per layer, every one an X2 `%TO.P%` pad-name attribute; zero geometry ops |
| PTH, NPTH drill | **none** — 212 and 168 holes, byte-identical |
| Edge.Cuts | **none** — 24 ops, identical |
| F.Mask, B.Mask | **none** |
| F.Silkscreen, B.Silkscreen | changed, and confined to a 17.780 x 33.020 mm window — exactly the module outline |

`jlcpcb/assembly/` was deliberately **not** regenerated. No footprint moved (59
before, 59 after, zero position or rotation changes), and neither the BOM nor
the position file ever listed the MCU — they cover only the parts JLCPCB
machine-assembles.

The plate and back plate exports (`Case/switchplate/*/*.dxf`,
`Case/backplate/Gerbers/`) are also unchanged, because neither of those boards
was modified.

### Not verified

Stated so you know where to double-check rather than trust this document:

* **No physical build has been made.** Everything here is from file analysis.
* **No component heights were confirmed.** KB2040 PCB thickness, USB-C
  receptacle height, and whether a USB-C plug's overmold clears the recessed
  board edge are all unknown here. Later revisions are *reported* to use 5 mm
  screws and 9 mm standoffs against v2/v3's 4 mm and 4 mm; that figure is
  repeated here as hearsay and was **not** derived from these files. Confirm
  against the real parts before ordering hardware.
* **Nothing has been test-fabricated.** The gerbers are regenerated and were
  checked against a same-tool export of the upstream board, but no board house
  has run them and no panel has been built from them.
* **The exports come from a newer KiCad than the design file.** The board is
  still in KiCad 6 format and was plotted with KiCad 10.0.5. That is normal and
  the geometry was verified, but if you have KiCad 6 to hand, re-plotting there
  would keep the toolchain consistent.

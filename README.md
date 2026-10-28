# SY22 Control

A browser editor for the **Yamaha SY22** vector synthesizer. It makes the inside of the machine visible: all 64 internal voices and 16 multis at a glance, every voice parameter on a knob (including the ones the SY22's own screens never show), and the level and detune vectors drawn out so you can see, and record, how a voice moves.

**Use it:** open the page in Chrome or Edge (Safari has no Web MIDI). Everything runs in your browser; nothing is uploaded.

## Status
Personal project, built for my own studio and shared as-is under the MIT licence. Free to use, copy and change.
It is **not actively maintained**: I update it only when I run into something while using it myself. No support, no feature requests, no update schedule.

## What it does
**Memory**
- Receive all voices and multis from the SY22 (or open a `.syx` file), save them as `.syx`, send them back
- 64 voices in an 8 × 8 grid like the SY22's banks, 16 multis below; drag a voice onto another to swap them (multis that use them are updated too)
- Follow: clicking a voice selects it on the SY22, and choosing one on the SY22 selects it here

**Voice editor**
- Every parameter of all four elements: the two AWM sample elements (A, C) and the two FM elements (B, D) with separate modulator and carrier
- Hidden parameters, marked in orange: temperament, operator waves, frequency ratios, fixed frequency, operator envelopes, envelope peak, LFO to carrier or modulator only
- Envelopes drawn as you edit them
- **Live**: every change is sent to the SY22's edit buffer, a few times per second
- Knobs can learn a knob or fader from a MIDI controller (tested with a Korg nanoKONTROL2), each with its own range and pickup
- Snapshot A/B with a morph slider, copy element A → C or B → D, revert, save a single voice as `.syx`
- Get from SY22: pulls in the voice the SY22 is playing

**Vectors**
- Level and detune vectors drawn on a pad with the SY22's corners (A top, B bottom, C right, D left)
- Record a new vector with the mouse, at the voice's own vector speed, like recording on the synth; loop or end, clear, or edit the steps in a table

**Multis**
- Name, effect, and per part: MIDI channel, voice, volume, detune, note range and note shift

**Lab tools**: compare two dumps byte by byte (with labels), raw bytes of the selected voice or multi, MIDI log.

## Setting up the SY22
- UTILITY → MIDI: **EXCLUSIVE on**. To receive voices or a bank: **internal memory protect off**.
- Stay in **VOICE play mode** (not EDIT) while editing live.
- Nothing becomes permanent until you store it on the SY22 yourself. After a live edit, the SY22 asks whether to store the voice when you change voices. That's by design: the machine keeps the final word.

Good to know:
- The SY22 can't take single-parameter changes, so every edit sends the whole voice (592 bytes, about 0.2 seconds of MIDI). Live editing is close, not perfectly smooth.
- After receiving a voice the SY22 jumps to its pitch bend screen. Nothing in the SysEx controls that, as far as I found.
- The effect is one byte: 16 types and a depth of 0–7. There are no delay times or reverb lengths to edit.

## How it works (short version)
- One voice: `F0 43 0n 7E 04 48 "PK  2203AE" [574 bytes] [checksum] F7` · request: `F0 43 2n 7E "PK  2203AE" F7`
- All voices + multis: `F0 43 0n 7E` + 17 blocks (`[count] [data] [checksum]`, 4 voices each, then 16 multis of 94 bytes) + `F7` · request: `F0 43 2n 7E "PK  2203VM" F7`
- Each voice and multi also has its own 8-bit checksum: the sum of its bytes, where the high-bit bytes count × 128 (for voices, byte 0 is one of them)
- Voice: name at bytes 3–10, effect at 2, elements A `15`, B `30`, C `5F`, D `7A`, vectors from `AB` (50 steps of time, X, Y)
- Multi: name 3–10, then per part channel, note limits, voice, volume, detune, note shift; effect at 91

## Not possible (as far as I found)
- Changing a single parameter without resending the whole voice
- Effect settings beyond type and depth
- Choosing which screen the SY22 shows

## Thanks
- **Edisyn** by Sean Luke (TG33/SY22/SY35 editor), for the voice layout and the wave name lists
- **Rich Hanson** and the old SY-Edit / *sy35form* documentation, and **everilae/sy22** on GitHub for the byte-by-byte voice table
- Yamaha's SY22 owner's manual for the bulk dump formats and parameter ranges

## Credits
Made by **Maurice Hissink** (harmonicworks): idea, design and testing on my own SY22, with **Claude** (Anthropic's AI) doing most of the reverse-engineering and coding. Built in one morning, on the foundations of my other MIDI tools ([SU10-Kit](https://github.com/harmonicworks/SU10-kit), [M5 Editor](https://github.com/harmonicworks/M5-editor), [VS-880 Control Room](https://github.com/harmonicworks/VS880-Control-Room)).

MIT licence.

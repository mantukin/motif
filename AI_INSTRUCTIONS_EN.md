# Motif - Instructions for AI Assistants

You are an AI assistant for music composition in a MIDI sequencer. Your task is to generate or modify project JSON files according to user requests.

## Project Structure

```typescript
interface Project {
  name: string;          // Project name
  bpm: number;           // Tempo (60-200)
  measures: number;      // Number of bars (measures)
  masterVolume: number;  // Master volume (0-1)
  tracks: Track[];       // Array of tracks
}

interface Track {
  id: string;            // Unique ID (e.g., "track-1")
  name: string;          // Name ("Drums", "Bass", etc.)
  trackType?: 'instrument' | 'drums' | 'voice'; // Optional editor mode
  instrument: string;    // Supported instrument ID; use the core list below by default
  volume: number;        // Track volume (0-1)
  clips: MidiClip[];     // Array of clips
}

interface MidiClip {
  id: string;            // Unique ID (e.g., "clip-1")
  name: string;          // Clip name
  startTime: number;     // Position on timeline (in beats)
  duration: number;      // Duration on timeline (in beats)
  patternDuration?: number; // Optional internal loop length for the clip pattern (in beats)
  notes: Note[];         // Array of notes
}

interface Note {
  id: string;            // Unique ID (e.g., "note-1")
  midi: number;          // MIDI note number (0-127, C4 = 60)
  time: number;          // Start time RELATIVE TO CLIP START (in beats)
  duration: number;      // Duration (in beats)
  velocity: number;      // Volume/Impact strength (0-1)
  lyric?: string;        // Optional word/syllable token for voice melodies
}
```

## Composition Strategy

When creating or modifying music, use a pattern-oriented workflow (like building with Lego blocks):

1. **Create Reusable Patterns:** For any musical idea (melody, beat, chord progression), create a separate clip with notes. Keep patterns short (1, 2, or 4 bars).

2. **Build with Copies:** Create the song structure by copying patterns. Place them on the timeline to create verses, choruses, bridges. If a section repeats, use the same patterns.

3. **Stretching and Looping:** You can change the `duration` of a clip on the timeline — the notes inside will automatically loop. For example: create a 1-bar drum loop and set `duration: 8` to play it twice.

4. **Set Pattern Boundaries Explicitly:** If a clip loops a shorter musical cell inside a longer clip, add `patternDuration`. Usually this should be a bar or phrase boundary such as `4`, `8`, or `16` beats.

## Important Rules

- **Time in Beats:** 1 measure = 4 beats in 4/4 time signature.
- **Note.time is relative to clip:** If a clip starts at beat 4, and a note should sound at beat 5, then `note.time = 1`.
- **Pattern Loop Boundary:** Do not place the loop boundary at the literal end of the last note unless that is the true musical boundary. Prefer the end of the bar or phrase via `patternDuration`.
- **Unique IDs:** All `id` fields must be unique within the project.
- **Volume/Velocity:** Value from 0 (silence) to 1 (maximum).
- **Voice Tracks:** Use `trackType: "voice"` for vocal melodies. The playback instrument can still be any melodic timbre, but `voice_oohs` is the default starting point.
- **Lyrics on Notes:** For vocal phrases, store each word or syllable directly on the matching note with `note.lyric`.

## General MIDI Instruments

Use the IDs below as the safest defaults for new tracks. The app also supports additional MuseScore preset IDs and expanded drum-kit presets. If you are editing an existing project, preserve its exact `instrument` IDs. If the user asks for a more specific preset and you already see its exact ID in the project JSON, reuse that ID instead of inventing a new name.

### Instrument ID Guidance

- Prefer the GM IDs below for simple requests like "piano", "bass", "strings", or "basic drums".
- Use extra MuseScore preset IDs when the user clearly wants a more specific color or articulation. These IDs usually start with `ms_`, for example `ms_b8_l0_p0_mellow_grand_piano`, `ms_b20_l0_p81_detuned_saw`, or `ms_b17_l0_p40_violin_expr`.
- `Expr.` presets are valid melodic instruments and are useful for more expressive sustained lines.
- Drum-kit presets usually start with `drum_kit_`, for example `drum_kit_p8_room`, `drum_kit_p25_tr_808`, or `drum_kit_p48_orchestra_kit`.
- Do not rewrite an existing instrument to a simpler GM name unless the user explicitly asks for that change.

### Full Core GM Instrument IDs

```text
acoustic_grand_piano
bright_acoustic_piano
electric_grand_piano
honky_tonk_piano
electric_piano_1
electric_piano_2
harpsichord
clavinet
celesta
glockenspiel
music_box
vibraphone
marimba
xylophone
tubular_bells
dulcimer
drawbar_organ
percussive_organ
rock_organ
church_organ
reed_organ
accordion
harmonica
tango_accordion
acoustic_guitar_nylon
acoustic_guitar_steel
electric_guitar_jazz
electric_guitar_clean
electric_guitar_muted
overdriven_guitar
distortion_guitar
guitar_harmonics
acoustic_bass
electric_bass_finger
electric_bass_pick
fretless_bass
slap_bass_1
slap_bass_2
synth_bass_1
synth_bass_2
violin
viola
cello
contrabass
tremolo_strings
pizzicato_strings
orchestral_harp
timpani
string_ensemble_1
string_ensemble_2
synth_strings_1
synth_strings_2
choir_aahs
voice_oohs
synth_choir
orchestra_hit
trumpet
trombone
tuba
muted_trumpet
french_horn
brass_section
synth_brass_1
synth_brass_2
soprano_sax
alto_sax
tenor_sax
baritone_sax
oboe
english_horn
bassoon
clarinet
piccolo
flute
recorder
pan_flute
blown_bottle
shakuhachi
whistle
ocarina
lead_1_square
lead_2_sawtooth
lead_3_calliope
lead_4_chiff
lead_5_charang
lead_6_voice
lead_7_fifths
lead_8_bass_lead
pad_1_new_age
pad_2_warm
pad_3_polysynth
pad_4_choir
pad_5_bowed
pad_6_metallic
pad_7_halo
pad_8_sweep
fx_1_rain
fx_2_soundtrack
fx_3_crystal
fx_4_atmosphere
fx_5_brightness
fx_6_goblins
fx_7_echoes
fx_8_sci_fi
sitar
banjo
shamisen
koto
kalimba
bagpipe
fiddle
shanai
tinkle_bell
agogo
steel_drums
woodblock
taiko_drum
melodic_tom
synth_drum
reverse_cymbal
guitar_fret_noise
breath_noise
seashore
bird_tweet
telephone_ring
helicopter
applause
gunshot
```

### Full MuseScore Extra Preset IDs

```text
ms_b8_l0_p0_mellow_grand_piano
ms_b8_l0_p4_detuned_tine_ep
ms_b8_l0_p5_detuned_fm_ep
ms_b8_l0_p6_coupled_harpsichord
ms_b8_l0_p14_church_bell
ms_b8_l0_p16_detuned_organ_1
ms_b17_l0_p16_drawbar_organ_expr
ms_b18_l0_p16_detuned_org_1_expr
ms_b8_l0_p17_detuned_organ_2
ms_b17_l0_p17_perc_organ_expr
ms_b18_l0_p17_detuned_org_2_expr
ms_b17_l0_p18_rock_organ_expr
ms_b8_l0_p19_church_organ_2
ms_b17_l0_p19_church_organ_expr
ms_b18_l0_p19_church_organ_2_expr
ms_b17_l0_p20_reed_organ_expr
ms_b8_l0_p21_italian_accordion
ms_b17_l0_p21_accordion_expr
ms_b18_l0_p21_it_accordion_expr
ms_b17_l0_p22_harmonica_expr
ms_b17_l0_p23_bandoneon_expr
ms_b8_l0_p24_ukulele
ms_b8_l0_p25_12_string_guitar
ms_b16_l0_p25_mandolin
ms_b8_l0_p26_hawaiian_guitar
ms_b8_l0_p28_funk_guitar
ms_b8_l0_p30_feedback_guitar
ms_b8_l0_p31_guitar_feedback
ms_b8_l0_p38_synth_bass_3
ms_b8_l0_p39_synth_bass_4
ms_b8_l0_p40_slow_violin
ms_b17_l0_p40_violin_expr
ms_b18_l0_p40_slow_violin_expr
ms_b17_l0_p41_viola_expr
ms_b17_l0_p42_cello_expr
ms_b17_l0_p43_contrabass_expr
ms_b17_l0_p44_strings_trem_expr
ms_b20_l0_p44_violins_tremolo
ms_b21_l0_p44_violins_trem_expr
ms_b25_l0_p44_violins2_tremolo
ms_b26_l0_p44_violins2_trem_expr
ms_b30_l0_p44_violas_tremolo
ms_b31_l0_p44_violas_trem_expr
ms_b40_l0_p44_celli_tremolo
ms_b41_l0_p44_celli_trem_expr
ms_b50_l0_p44_basses_tremolo
ms_b51_l0_p44_basses_trem_expr
ms_b20_l0_p45_violins_pizzicato
ms_b25_l0_p45_violins2_pizzicato
ms_b30_l0_p45_violas_pizzicato
ms_b40_l0_p45_celli_pizzicato
ms_b50_l0_p45_basses_pizzicato
ms_b8_l0_p48_orchestral_pad
ms_b17_l0_p48_strings_fast_expr
ms_b20_l0_p48_violins_fast
ms_b21_l0_p48_violins_fast_expr
ms_b25_l0_p48_violins2_fast
ms_b26_l0_p48_violins2_fast_expr
ms_b30_l0_p48_violas_fast
ms_b31_l0_p48_violas_fast_expr
ms_b40_l0_p48_celli_fast
ms_b41_l0_p48_celli_fast_expr
ms_b50_l0_p48_basses_fast
ms_b51_l0_p48_basses_fast_expr
ms_b17_l0_p49_strings_slow_expr
ms_b20_l0_p49_violins_slow
ms_b21_l0_p49_violins_slow_expr
ms_b25_l0_p49_violins2_slow
ms_b26_l0_p49_violins2_slow_expr
ms_b30_l0_p49_violas_slow
ms_b31_l0_p49_violas_slow_expr
ms_b40_l0_p49_celli_slow
ms_b41_l0_p49_celli_slow_expr
ms_b50_l0_p49_basses_slow
ms_b51_l0_p49_basses_slow_expr
ms_b8_l0_p50_synth_strings_3
ms_b17_l0_p50_syn_strings_1_expr
ms_b18_l0_p50_synth_strings3_expr
ms_b17_l0_p51_syn_strings_2_expr
ms_b17_l0_p52_choir_aahs_expr
ms_b17_l0_p53_voice_oohs_expr
ms_b17_l0_p54_synth_voice_expr
ms_b17_l0_p56_trumpet_expr
ms_b17_l0_p57_trombone_expr
ms_b17_l0_p58_tuba_expr
ms_b17_l0_p59_hmn_mute_tpt_expr
ms_b17_l0_p60_french_horns_expr
ms_b8_l0_p61_brass_2
ms_b17_l0_p61_brass_section_expr
ms_b18_l0_p61_brass_2_expr
ms_b8_l0_p62_synth_brass_3
ms_b17_l0_p62_synth_brass_1_expr
ms_b18_l0_p62_synth_brass_3_expr
ms_b8_l0_p63_synth_brass_4
ms_b17_l0_p63_synth_brass_2_expr
ms_b18_l0_p63_synth_brass_4_expr
ms_b17_l0_p64_soprano_sax_expr
ms_b17_l0_p65_alto_sax_expr
ms_b17_l0_p66_tenor_sax_expr
ms_b17_l0_p67_baritone_sax_expr
ms_b17_l0_p68_oboe_expr
ms_b17_l0_p69_english_horn_expr
ms_b17_l0_p70_bassoon_expr
ms_b17_l0_p71_clarinet_expr
ms_b17_l0_p72_piccolo_expr
ms_b17_l0_p73_flute_expr
ms_b17_l0_p74_recorder_expr
ms_b17_l0_p75_pan_flute_expr
ms_b17_l0_p76_bottle_chiff_expr
ms_b17_l0_p77_shakuhachi_expr
ms_b17_l0_p78_whistle_expr
ms_b17_l0_p79_ocarina_expr
ms_b8_l0_p80_sine_wave
ms_b17_l0_p80_square_lead_expr
ms_b18_l0_p80_sine_wave_expr
ms_b17_l0_p81_saw_lead_expr
ms_b20_l0_p81_detuned_saw
ms_b21_l0_p81_detuned_saw_expr
ms_b17_l0_p82_calliope_lead_expr
ms_b17_l0_p83_chiffer_lead_expr
ms_b17_l0_p84_charang_expr
ms_b17_l0_p85_solo_vox_expr
ms_b17_l0_p86_5th_saw_wave_expr
ms_b17_l0_p87_bass_lead_expr
ms_b17_l0_p89_warm_pad_expr
ms_b17_l0_p90_polysynth_expr
ms_b17_l0_p91_space_voice_expr
ms_b17_l0_p92_bowed_glass_expr
ms_b17_l0_p93_metal_pad_expr
ms_b17_l0_p94_halo_pad_expr
ms_b17_l0_p95_sweep_pad_expr
ms_b17_l0_p97_soundtrack_expr
ms_b17_l0_p99_atmosphere_expr
ms_b17_l0_p101_goblin_expr
ms_b17_l0_p102_echo_drops_expr
ms_b17_l0_p103_star_theme_expr
ms_b8_l0_p107_taisho_koto
ms_b17_l0_p110_fiddle_expr
ms_b17_l0_p111_shenai_expr
ms_b1_l0_p115_temple_blocks
ms_b8_l0_p115_castanets
ms_b8_l0_p116_concert_bass_drum
ms_b8_l0_p117_melo_tom_2
ms_b8_l0_p118_808_tom
```

### Full Drum Kit Preset IDs

```text
percussion
drum_kit_p1_standard_1
drum_kit_p2_standard_2
drum_kit_p3_standard_3
drum_kit_p4_standard_4
drum_kit_p5_standard_5
drum_kit_p6_standard_6
drum_kit_p7_standard_7
drum_kit_p8_room
drum_kit_p9_room_1
drum_kit_p10_room_2
drum_kit_p11_room_3
drum_kit_p12_room_4
drum_kit_p13_room_5
drum_kit_p14_room_6
drum_kit_p15_room_7
drum_kit_p16_power
drum_kit_p17_power_1
drum_kit_p18_power_2
drum_kit_p19_power_3
drum_kit_p24_electronic
drum_kit_p25_tr_808
drum_kit_p32_jazz
drum_kit_p33_jazz_1
drum_kit_p34_jazz_2
drum_kit_p35_jazz_3
drum_kit_p36_jazz_4
drum_kit_p40_brush
drum_kit_p41_brush_1
drum_kit_p42_brush_2
drum_kit_p48_orchestra_kit
drum_kit_p56_marching_snare
drum_kit_p57_oldmarchingbass
drum_kit_p58_marching_cymbals
drum_kit_p59_marching_bass
drum_kit_p95_oldmarchingtenor
drum_kit_p96_marching_tenor
```

### Drums (Special)
For drums, use `trackType: "drums"` and the GM Drum Map below. `instrument: "percussion"` is the safe default, but the app also supports multiple drum-kit preset IDs such as room, power, electronic, TR-808, jazz, brush, orchestra, and marching variants.

When using alternate drum kits, keep the same note-writing workflow unless the project already demonstrates a different mapping: kick and snare stay in the usual GM range, and the kit choice mainly changes the sampled character.

| MIDI | Sound |
|------|------|
| 35 | Acoustic Bass Drum |
| 36 | Bass Drum 1 |
| 38 | Acoustic Snare |
| 40 | Electric Snare |
| 42 | Closed Hi-Hat |
| 44 | Pedal Hi-Hat |
| 46 | Open Hi-Hat |
| 49 | Crash Cymbal 1 |
| 51 | Ride Cymbal 1 |
| 45 | Low Tom |
| 47 | Low-Mid Tom |
| 48 | Hi-Mid Tom |
| 50 | High Tom |

## MIDI Note Numbers (Reference)

| Octave | C  | C# | D  | D# | E  | F  | F# | G  | G# | A  | A# | B  |
|--------|----|----|----|----|----|----|----|----|----|----|----|----|
| 1      | 24 | 25 | 26 | 27 | 28 | 29 | 30 | 31 | 32 | 33 | 34 | 35 |
| 2      | 36 | 37 | 38 | 39 | 40 | 41 | 42 | 43 | 44 | 45 | 46 | 47 |
| 3      | 48 | 49 | 50 | 51 | 52 | 53 | 54 | 55 | 56 | 57 | 58 | 59 |
| 4      | 60 | 61 | 62 | 63 | 64 | 65 | 66 | 67 | 68 | 69 | 70 | 71 |
| 5      | 72 | 73 | 74 | 75 | 76 | 77 | 78 | 79 | 80 | 81 | 82 | 83 |
| 6      | 84 | 85 | 86 | 87 | 88 | 89 | 90 | 91 | 92 | 93 | 94 | 95 |

---

## UJAM Virtual Instrument Instructions (Guitar & Bass)

If the user asks for a rock/metal guitar part or a modern electric bass part, assume the use of a UJAM plugin and follow these special instructions. This workflow differs from standard MIDI programming.

### Key Concept: Two-Note System (Player Mode)

Your MIDI programming for these instruments must follow a simple two-note system: one note defines **WHAT** to play (chord/root note), the other defines **HOW** to play it rhythmically.

- **Play Range:** Upper MIDI range for selecting chords (guitar) or root notes (bass). These notes must be held for the entire duration of the active harmony.
- **Phrase Range:** Lower MIDI range — keyswitches that trigger pre-made rhythmic patterns. A single note here triggers a complex performance.
- **CRITICAL RULE: MIDI DEAD ZONE (C3 - B3):** An entire octave from C3 to B3 is **completely unused**. You **MUST NOT** place notes in this range!

### Naming Convention
**CRITICALLY IMPORTANT:** Name the MIDI clip to indicate the plugin mode. For example: "Chorus Rhythm (Chord Mode)" for guitar or "Verse Groove (Player Mode)" for bass.

---

### UJAM IRON 2 — Guitar Plugin (Player Mode)

Use for tracks with a guitar instrument (e.g., `overdriven_guitar`, `distortion_guitar`).

- **Play Range (C4-F6):** For chords or single notes. For a G5 power chord — place G4 and D5. Hold these notes for the entire duration of the active chord.
- **Phrase Range (C1-B2):** For rhythmic patterns. The pattern will loop while the note is held.

#### IRON 2 — Phrase Map (C1-B2)

| Key | Description |
| :--- | :--- |
| C1 | Silence |
| C#1 / Db1 | Long Chord With Fill |
| D1 | Open 1/1 |
| D#1 / Eb1 | Generic Rhythm Sustain |
| E1 | Long Chord 2 & 4 |
| F1 | Off-Beat 2 & 4 |
| F#1 / Gb1 | Single Note Rhythm |
| G1 | Open Stops 1/4 |
| G#1 / Ab1 | Generic Chord Rhythm |
| A1 | Open 1/4 |
| A#1 / Bb1 | Chord Rhythm 1/16 |
| B1 | Half-Mute 1/4 |
| C2 | Muted 1/4 |
| C#2 / Db2 | Muted 1/8 Open |
| D2 | Open 1/8 |
| D#2 / Eb2 | Build-Up Open 1/8 |
| E2 | Half-Muted 1/8 |
| F2 | Muted 1/8 |
| F#2 / Gb2 | Muted 1/16 Riding |
| G2 | Muted 1/16 |
| G#2 / Ab2 | Build-Up Muted 1/8 |
| A2 | Off-Beat 1/8 |
| A#2 / Bb2 | Slide Down |
| B2 | Pick Slide |

---

### UJAM ROWDY 2 — Bass Plugin (Player Mode)

Use for tracks with an electric bass instrument (e.g., `electric_bass_finger`, `electric_bass_pick`). Follows the same concept as the guitar plugin.

- **Play Range (C3-B4):** For root notes of the bass line. These notes must be held for the full duration.
- **Phrase Range (C#1-B2):** For rhythmic patterns.
- **CRITICAL RULE: MIDI DEAD ZONE:** A dead zone exists between the phrase and play ranges. Do not place notes there!

#### ROWDY 2 — Phrase Map (C#1-B2)

| MIDI Note | Description | Bars |
| :--- | :--- | :--- |
| C♯1 | Long 1/2 Notes | 2 |
| D1 | Long 1/4 Notes | 2 |
| D♯1 | Short 1/4 Notes | 2 |
| E1 | Long 1/8 Notes | 2 |
| F1 | Short 1/8 Notes | 2 |
| F♯1 | Short 1/8+1/16 Notes | 2 |
| G1 | Basic 1/8 Note 1 | 2 |
| G♯1 | Basic 1/8 Note 2 | 2 |
| A1 | Basic Syncopated 1 | 2 |
| A♯1 | Basic Syncopated 2 | 2 |
| B1 | Basic 1/16 Note 1 | 2 |
| C2 | Basic 1/16 Note 2 | 2 |
| C♯2 | Fill 1/8 Note | 1 |
| D2 | Basic 1/16 Note Short | 2 |
| D♯2 | Fill 1/16 Note 1 | 1 |
| E2 | Samba | 2 |
| F2 | Samba 1+5 | 2 |
| F♯2 | Fill 1/16 Note 2 | 1 |
| G2 | Disco 1/8 Notes | 2 |
| G♯2 | Fill 1/16 Note 3 | 1 |
| A2 | Disco Samba | 2 |
| A♯2 | Fill 1/16 Note 4 | 1 |
| B2 | Salsa | 2 |

---

## Example Project

```json
{
  "name": "Simple Beat",
  "bpm": 120,
  "measures": 4,
  "masterVolume": 0.8,
  "tracks": [
    {
      "id": "track-drums",
      "name": "Drums",
      "instrument": "percussion",
      "volume": 0.8,
      "clips": [
        {
          "id": "clip-drums-1",
          "name": "Basic Beat",
          "startTime": 0,
          "duration": 4,
          "notes": [
            {"id": "n1", "midi": 36, "time": 0, "duration": 0.5, "velocity": 0.9},
            {"id": "n2", "midi": 42, "time": 0, "duration": 0.25, "velocity": 0.6},
            {"id": "n3", "midi": 42, "time": 0.5, "duration": 0.25, "velocity": 0.6},
            {"id": "n4", "midi": 38, "time": 1, "duration": 0.5, "velocity": 0.85},
            {"id": "n5", "midi": 42, "time": 1, "duration": 0.25, "velocity": 0.6},
            {"id": "n6", "midi": 42, "time": 1.5, "duration": 0.25, "velocity": 0.6},
            {"id": "n7", "midi": 36, "time": 2, "duration": 0.5, "velocity": 0.9},
            {"id": "n8", "midi": 42, "time": 2, "duration": 0.25, "velocity": 0.6},
            {"id": "n9", "midi": 42, "time": 2.5, "duration": 0.25, "velocity": 0.6},
            {"id": "n10", "midi": 38, "time": 3, "duration": 0.5, "velocity": 0.85},
            {"id": "n11", "midi": 42, "time": 3, "duration": 0.25, "velocity": 0.6},
            {"id": "n12", "midi": 42, "time": 3.5, "duration": 0.25, "velocity": 0.6}
          ]
        }
      ]
    },
    {
      "id": "track-bass",
      "name": "Bass",
      "trackType": "instrument",
      "instrument": "electric_bass_finger",
      "volume": 0.7,
      "clips": [
        {
          "id": "clip-bass-1",
          "name": "Bass Line",
          "startTime": 0,
          "duration": 4,
          "notes": [
            {"id": "b1", "midi": 40, "time": 0, "duration": 1, "velocity": 0.8},
            {"id": "b2", "midi": 40, "time": 2, "duration": 0.5, "velocity": 0.8},
            {"id": "b3", "midi": 43, "time": 2.5, "duration": 0.5, "velocity": 0.7},
            {"id": "b4", "midi": 45, "time": 3, "duration": 1, "velocity": 0.8}
          ]
        }
      ]
    },
    {
      "id": "track-voice",
      "name": "Lead Voice",
      "trackType": "voice",
      "instrument": "voice_oohs",
      "volume": 0.8,
      "clips": [
        {
          "id": "clip-voice-1",
          "name": "Hook",
          "startTime": 0,
          "duration": 4,
          "notes": [
            {"id": "v1", "midi": 67, "time": 0, "duration": 1, "velocity": 0.85, "lyric": "Shine"},
            {"id": "v2", "midi": 69, "time": 1, "duration": 1, "velocity": 0.82, "lyric": "on"},
            {"id": "v3", "midi": 71, "time": 2, "duration": 2, "velocity": 0.88, "lyric": "me"}
          ]
        }
      ]
    }
  ]
}
```

---

## How to Use

1. The user exports their current project from the sequencer (JSON).
2. The user sends you this JSON + this instruction file + their request.
3. You generate an updated JSON with changes.
4. The user imports the JSON back into the sequencer.

**Response Format**:

- In chat-based workflows, you may answer in a normal, user-friendly way.
- When returning updated project data, prefer putting the JSON in a fenced `json` code block so the user can copy it easily.
- A short explanation of what changed before or after the JSON is welcome when it helps the user.
- Only return raw JSON without markdown if the user explicitly asks for raw JSON only.

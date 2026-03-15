# Market Study: iOS App for AI Melody Extraction to Piano Sheet Music

**Date:** March 2026  
**Scope:** Competitive landscape for an app that imports any song, uses AI to extract the main melody (主旋律), and displays it for piano learning

---

## Executive Summary

The market for audio-to-sheet-music transcription is crowded with partial solutions, but no product cleanly serves the specific workflow of: **import any song → AI isolates the main melody → display it as piano-playable notation for a learner.** Existing tools either stop at stem separation, focus on manual practice aids without transcription, or require the input audio to already be a solo instrument. This gap represents a genuine opportunity.

---

## Competitive Product Profiles

### 1. Moises (moises.ai)

| Attribute | Detail |
|---|---|
| **Platform** | iOS, Android, Web |
| **Pricing** | Free (limited); Premium ~$3.99–$5.99/mo; Pro ~$18.33–$24.99/mo |
| **Core tech** | AI stem separation (vocals, drums, bass, guitar, keys, "other") |

**What it does relevant to melody extraction:** Moises splits a mixed song into isolated stems. A user can mute drums/bass/chords and listen to the isolated vocal or lead instrument, which approximates melody isolation. It does not transcribe or produce notation.

**Strengths:**
- Best-in-class stem separation quality for a consumer mobile app
- Widely used by musicians; strong brand recognition
- Supports up to 20-minute files on Premium
- Also detects chords, tempo, and key

**Weaknesses / Limitations:**
- No notation output — the isolated melody stem is audio only
- No piano display or sheet music
- The "melody" is an audio track; pitch correction and formatting for piano is left entirely to the user
- Requires the user to then manually transcribe or use a separate tool

**Target audience:** Working musicians, cover artists, vocalists, producers — not piano learners.

---

### 2. ScoreCloud

| Attribute | Detail |
|---|---|
| **Platform** | macOS, Windows, iOS (ScoreCloud Songwriter) |
| **Pricing** | Free (watermarked, no save/export); Plus ~$4.99/mo (MIDI export); Pro (MusicXML export) |
| **Core tech** | Audio-to-notation transcription (monophonic and polyphonic); vocal/chord separation for mixed songs |

**What it does relevant to melody extraction:** ScoreCloud Songwriter handles mixed audio — it separates the vocal melody from accompaniment and transcribes both melody and chord symbols into a lead sheet. ScoreCloud Studio handles single-instrument recordings.

**Strengths:**
- One of the few tools that attempts full-mix → melody + chords lead sheet conversion
- Handles polyphonic instruments; outputs to industry-standard MusicXML
- Produces actual notation, not just audio
- iOS app exists (Songwriter variant)

**Weaknesses / Limitations:**
- Transcription accuracy degrades significantly on dense pop/rock mixes; works best with clean vocal-forward recordings
- Free tier is impractical (watermark, no saving)
- Lead sheet output assumes a vocalist performing the melody; formatting for piano solo isn't the priority
- Limited catalogue integration; user must supply the audio file

**Target audience:** Singer-songwriters, composers wanting to notate their own performances.

---

### 3. AnthemScore (Lunaverus)

| Attribute | Detail |
|---|---|
| **Platform** | Windows, macOS, Linux — **no mobile app** |
| **Pricing** | Lite: $19.97 one-time; Professional: ~$39.99 one-time; 30-day free trial |
| **Core tech** | ML-based audio-to-sheet-music (detects notes in MP3/WAV, arranges into measures) |

**What it does relevant to melody extraction:** AnthemScore converts any audio file to sheet music using machine learning. It produces piano-roll and standard notation. Users can adjust note detection thresholds to focus on prominent notes (i.e., melody).

**Strengths:**
- One-time purchase — no subscription
- Cross-platform desktop support
- Decent results on solo instruments (piano, guitar)
- Exports to MusicXML, MIDI, PDF

**Weaknesses / Limitations:**
- Desktop only — no iOS app
- Accuracy on full-mix audio is poor; produces ghost notes and rhythmically misplaced notes
- No built-in melody isolation; all instruments in the mix are transcribed together
- Requires significant manual cleanup

**Target audience:** Desktop musicians wanting a low-cost, offline transcription starting point.

---

### 4. Capo (SuperMegaUltraGroovy) — iOS

| Attribute | Detail |
|---|---|
| **Platform** | iOS (iPhone, iPad), macOS |
| **Pricing** | ~$9.99 one-time purchase (App Store) |
| **Core tech** | Chord detection, key/tempo analysis, speed and pitch control |

**What it does relevant to melody extraction:** Capo detects chords and key from any song in your music library and displays them in a scrolling view synchronized with playback. Latest version (4.5, June 2025) improved detection speed by up to 10x.

**Strengths:**
- Best chord detection in its class for iOS
- Piano chord diagram view available
- One-time purchase; no subscription
- Excellent for learning songs by ear at reduced speed

**Weaknesses / Limitations:**
- Detects **chords**, not the **melody line** — cannot produce a monophonic melody for piano
- No notation output; no sheet music export
- Primarily designed for guitarists

**Target audience:** Guitarists and ear-training musicians; not suited for piano melody learners.

---

### 5. Transcribe+ (iOS)

| Attribute | Detail |
|---|---|
| **Platform** | iOS |
| **Pricing** | Free (first quarter of audio only); full version ~$14.99 one-time (in-app purchase) |
| **Core tech** | Speed/pitch control, loop regions, spectrum analysis |

**What it does relevant to melody extraction:** A manual transcription aid. It slows audio without changing pitch, lets users loop difficult passages, and displays a frequency spectrum to help identify notes by ear. No AI — the user does all the work.

**Strengths:**
- Industry-standard tool for musicians transcribing by ear
- Frequency spectrum helps identify pitches without AI
- One-time purchase; offline

**Weaknesses / Limitations:**
- No AI; no automation; pure manual tool
- Requires significant musical knowledge
- No notation output

**Target audience:** Advanced musicians and transcribers who prefer manual ear-training workflows.

---

### 6. Amazing Slow Downer (Roni Music) — iOS

| Attribute | Detail |
|---|---|
| **Platform** | iOS, Android |
| **Pricing** | Full version ~$14.99; free Lite version (first 25% of audio only) |
| **Core tech** | Time-stretching and pitch-shifting audio playback |

**Strengths:** Simple, reliable, offline, long track record.

**Weaknesses / Limitations:** Purely a playback speed tool — no AI, no analysis, no notation. Less featured than Transcribe+ (no spectrum analyzer).

**Target audience:** Musicians who want to slow down recordings to learn by ear.

---

### 7. Melodyne (Celemony)

| Attribute | Detail |
|---|---|
| **Platform** | macOS, Windows (plugin and standalone) — **no mobile app** |
| **Pricing** | Essential: ~$99; Assistant: ~$249; Editor: ~$499; Studio: ~$699 — all one-time |
| **Core tech** | Industry-leading pitch/time editing; DNA (Direct Note Access) for polyphonic audio |

**What it does relevant to melody extraction:** Melodyne's DNA technology (Editor and Studio editions) can detect and separate individual notes in a polyphonic chord. A skilled user can isolate the melody from a mix by muting non-melody notes.

**Strengths:**
- Unmatched precision for audio note manipulation
- Can technically isolate a melody from a full mix with expert use
- Outputs to MIDI

**Weaknesses / Limitations:**
- Desktop/DAW plugin only — no iOS app
- Extremely high skill ceiling; not usable by a casual learner
- Expensive; multi-step workflow requires additional notation software

**Target audience:** Professional audio engineers, producers, and session musicians.

---

### 8. Simply Piano (JoyTunes)

| Attribute | Detail |
|---|---|
| **Platform** | iOS, Android |
| **Pricing** | Freemium; Individual ~$17.90/mo or ~$169.90/yr; 14-day free trial |
| **Core tech** | Real-time note recognition via microphone; structured lesson curriculum |

**Strengths:** Polished beginner-friendly UX, large licensed song library, real-time note feedback.

**Weaknesses / Limitations:** Zero melody extraction — confined to its own catalogue. If the song a user wants isn't in the library, the user has no options within the app.

**Target audience:** Beginner piano learners following a structured curriculum.

---

### 9. Piano Marvel

| Attribute | Detail |
|---|---|
| **Platform** | iOS, Android, Web, macOS, Windows |
| **Pricing** | ~$10.84–$17.99/mo; ~$129.99/yr |
| **Core tech** | MIDI note recognition, sight-reading assessment, 26,000+ song library |

**Strengths:** Large library, granular accuracy metrics, better suited for intermediate/advanced learners than Simply Piano.

**Weaknesses / Limitations:** Catalogue-bound — cannot import a song from audio. Subscription is relatively expensive.

**Target audience:** Intermediate to advanced piano students.

---

### 10. MuseScore

| Attribute | Detail |
|---|---|
| **Platform** | iOS, Android, Web, macOS, Windows |
| **Pricing** | Desktop app free (open-source); MuseScore.com Pro ~$6.99/mo or ~$49/yr |
| **Core tech** | Music notation editor; MIDI import/export; community sheet music library |

**Strengths:** Largest free sheet music library in the world, best free notation editor, MusicXML/MIDI import/export.

**Weaknesses / Limitations:** Not a transcription tool — cannot accept audio input. Community arrangement quality varies. iOS app is a viewer/player only.

**Target audience:** Composers, arrangers, students needing a notation editor or sheet music browser.

---

### 11. Melody Scanner (Klangio)

| Attribute | Detail |
|---|---|
| **Platform** | iOS, Android, Web |
| **Pricing** | Free (up to 1 minute, microphone/YouTube only); Premium ~€6.99/mo or ~€47.99/yr |
| **Core tech** | AI transcription for piano, flute, violin (beta), guitar (beta); audio file and YouTube URL input |

**What it does relevant to melody extraction:** Accepts audio files or YouTube URLs and transcribes them to sheet music. Piano is the primary supported instrument. Outputs PDF, MIDI, MusicXML.

**Strengths:**
- One of the closest existing products to the target concept
- iOS native app exists
- Accepts external audio files and YouTube URLs
- Piano-specific transcription model

**Weaknesses / Limitations:**
- Works best when input audio **is already a solo piano recording** — degrades significantly on full-band mixes
- Does not separate/isolate a melody from a mixed song before transcribing
- On a pop song with vocals, drums, and bass, output is chaotic

**Target audience:** Musicians transcribing solo piano recordings or single-instrument performances.

---

### 12. Klangio (Full Suite)

| Attribute | Detail |
|---|---|
| **Platform** | iOS, Android, Web, MuseHub desktop |
| **Pricing** | Single app ~$24.99–$49.99/yr; bundle ~$49.99/yr; ~50 transcription tickets/month |
| **Core tech** | Separate AI models per instrument: Piano2Notes, Guitar2Tabs, Sing2Notes, Drum2Notes |

**What it does:** Instrument-specific transcription. Piano2Notes transcribes piano audio. Sing2Notes transcribes a sung or hummed melody. Neither processes a full-mix recording to extract a melody automatically.

**Strengths:** Instrument-specific models are more accurate, full suite covers multiple instruments, reasonable pricing, iOS native.

**Weaknesses / Limitations:** No full-mix melody isolation — models expect clean single-instrument audio. Sing2Notes requires user to sing it themselves.

---

### 13. Ivory

| Attribute | Detail |
|---|---|
| **Platform** | Web (browser-based); no native iOS app |
| **Pricing** | Free (first 45 seconds); Premium subscription (exact price not public) |
| **Core tech** | AI polyphonic piano transcription; MIDI piano roll editor; accepts YouTube URLs and audio uploads |

**Strengths:** Highest accuracy among transcription tools specifically for piano content, web-based with no install, MIDI piano roll editor for cleanup.

**Weaknesses / Limitations:** Web-only, optimized for piano recordings (not full-mix), does not isolate melody first, opaque pricing.

**Target audience:** Classical pianists transcribing piano recordings.

---

### 14. Songscription AI ⚡ (most relevant new entrant)

| Attribute | Detail |
|---|---|
| **Platform** | Web only; launched June 2025 |
| **Pricing** | Free (10 transcriptions/mo, up to 3 min each); Plus $9.99/mo; Pro $29.99/mo |
| **Core tech** | AI full-mix transcription; single-instrument output; supports piano, guitar, voice, violin, flute, drums, sax, trumpet, clarinet, trombone |

**What it does relevant to melody extraction:** Positions itself as "Shazam for sheet music" — upload a song, receive single-instrument sheet music. Raised $5M in seed funding and reached 150K users within months of launch.

**Strengths:**
- Explicitly targets the "import any song → get sheet music" workflow
- Supports piano output from full-mix audio
- Accepts YouTube URLs and audio uploads
- Proven demand: $5M raised, 150K users

**Weaknesses / Limitations:**
- **Web-only — no iOS app as of March 2026**
- Accuracy on full commercial recordings varies; MusicRadar noted "humans will be doing all the serious transcription for the foreseeable future" in their review
- No melody isolation step before transcription
- Free tier is very limited (3-minute clips, 10/month)

**Target audience:** Hobbyist musicians wanting sheet music for any song without paying an arranger.

---

## Feature Comparison Table

| Product | Platform | Price Model | Accepts Mixed Song | Isolates Melody | Outputs Notation | Piano-Specific | iOS Native |
|---|---|---|---|---|---|---|---|
| **Moises** | iOS/Web | Freemium ~$4–$25/mo | Yes | Audio stem only | No | No | Yes |
| **ScoreCloud** | iOS/Mac/Win | Freemium ~$5/mo+ | Partially | Vocal melody only | Yes | Partial | Yes |
| **AnthemScore** | Desktop only | One-time $20–$40 | Yes | No | Yes | Partial | No |
| **Capo** | iOS/Mac | One-time ~$10 | Yes | No (chords only) | No | Chord diagrams | Yes |
| **Transcribe+** | iOS | One-time ~$15 | Yes | Manual only | No | No | Yes |
| **Amazing Slow Downer** | iOS/Android | One-time ~$15 | Yes | Manual only | No | No | Yes |
| **Melodyne** | Desktop/Plugin | One-time $99–$699 | Yes (with skill) | Yes (with skill) | Via MIDI export | No | No |
| **Simply Piano** | iOS/Android | ~$18/mo | No | No | Catalogue only | Yes | Yes |
| **Piano Marvel** | iOS/Web | ~$18/mo | No | No | Catalogue only | Yes | Yes |
| **MuseScore** | iOS/Web/Desktop | Free / $7/mo | No (editor) | No | Yes (editor) | Partial | Yes |
| **Melody Scanner** | iOS/Web | Freemium ~€7/mo | Only if solo | No | Yes | Yes | Yes |
| **Klangio** | iOS/Web | ~$25–$50/yr | Only if solo | No | Yes | Yes | Yes |
| **Ivory** | Web only | Freemium (opaque) | Only if piano | No | Yes | Yes | No |
| **Songscription AI** | Web only | Freemium $0–$30/mo | Yes | Partial (attempts) | Yes | Yes | No |

---

## Market Gaps

### Gap 1: No iOS-Native End-to-End Pipeline

The specific workflow — **import any song → AI isolates main melody → display as piano notation → practice** — does not exist as a single native iOS app. Every product in the market either:

- Stops at audio (Moises, Capo, Transcribe+, Amazing Slow Downer), or
- Requires clean single-instrument input (Melody Scanner, Klangio, Ivory), or
- Is catalogue-bound with no external song input (Simply Piano, Piano Marvel), or
- Is desktop-only (AnthemScore, Melodyne), or
- Is web-only with no iOS app (Songscription, Ivory)

### Gap 2: No Melody Isolation Step Before Transcription

Transcription tools that accept full-mix audio (Songscription, ScoreCloud) attempt to go directly from full mix to notation. No mobile product applies a **stem separation pass first** (isolate the lead melody) and **then** runs a monophonic transcription model on the cleaner isolated audio. Combining Moises-quality stem separation with Melody Scanner-quality piano transcription in a single pipeline would meaningfully improve accuracy.

### Gap 3: No Piano-Learner UX Wrapping Transcription

Transcription tools output notation files. They do not wrap the output in a practice interface — scrolling notation, play-along, left/right hand separation, tempo control, or difficulty reduction. Simply Piano has the UX; it just cannot import arbitrary songs. No product combines both.

### Gap 4: Monophonic Melody Simplification for Learners

Even when a melody is correctly extracted, it may contain ornaments, fast runs, or rhythmic complexity that a beginner cannot play. No existing tool simplifies an extracted melody to a learner-appropriate version while preserving its recognizability.

### Gap 5: Licensing-Safe Full-Song Import UX

Most tools require users to upload their own audio files to sidestep licensing. An app with a clean file picker + YouTube URL flow (requiring user to supply the audio) is the legally defensible approach — and none of the iOS apps have done this well in the context of piano learning.

### Gap 6: Accuracy Transparency and Correction UX

Current melody transcription from polyphonic audio has meaningful error rates. No existing consumer product provides a simple mobile interface for quick note correction. A product that produces a usable first draft **and** offers an intuitive piano-roll correction UI on mobile would close a real gap.

---

## Opportunity Assessment

**Rating: High**

### Justification

**Demand is proven, not hypothetical.** Songscription launched June 2025, raised $5M, and reached 150K users on web alone — purely on the "import any song → get sheet music" proposition. This directly validates consumer demand for the workflow. The iOS-native version does not exist.

**No dominant iOS incumbent.** Moises owns stem separation on mobile. Simply Piano owns piano learning. Nobody owns the intersection. The gap is structural, not defended.

**AI model maturity now supports it.** Monophonic melody transcription from a clean audio stem achieves usable accuracy (recognizable output even with rhythm quantization needing cleanup). A two-step pipeline (stem separation → monophonic transcription) is more tractable than direct full-mix transcription — and it's what nobody has built on iOS.

**iOS is the right platform.** All transcription tools with traction (Melody Scanner, Klangio, Moises) have native iOS apps. Web-only tools (Songscription, Ivory) are leaving mobile acquisition on the table. Piano learning is a couch-and-iPad activity.

**Subscription revenue model fits.** Simply Piano, Piano Marvel, and Yousician have demonstrated $10–$20/month willingness-to-pay from piano learners. A product at $9.99–$14.99/month is consistent with established pricing.

**Key risks:**

| Risk | Severity | Mitigation |
|---|---|---|
| Transcription accuracy disappoints users | High | Correction UX; set expectations as "starting draft, not final score" |
| Songscription ships an iOS app | Medium | First-mover advantage in learning UX; their model is transcription-only, not practice-integrated |
| Moises adds notation export | Medium | Moises is infrastructure/API-focused; building piano learning UX is not their roadmap |
| Copyright exposure from processing commercial songs | Medium | Require user-supplied audio files; process on-device; MIDI/notation output belongs to the user |
| Apple enters natively via GarageBand | Low | Apple historically does not build deep practice/learning UX |

**Conclusion:** The opportunity is high-conviction. The core workflow is technically achievable today using established open-source models and on-device ML. The market is large and clearly underserved. No well-funded incumbent is defending this iOS-native territory. The primary execution risk is transcription accuracy — an engineering challenge, not a market hypothesis.

---

*Research based on web sources and training knowledge through March 2026. Verify all pricing independently before business decisions.*

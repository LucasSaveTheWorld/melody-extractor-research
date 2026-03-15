# Melody Extraction on iOS: Technical Feasibility Study

> Knowledge base: training data through August 2025. For the most current benchmark numbers and Core ML conversion reports, cross-reference the Spotify Basic Pitch GitHub repo, `coremltools` GitHub issues, and Papers With Code AMT leaderboards.

---

## 0. Target Use Case: Chinese Vocal Melody → Piano Sheet Music

### Use Case Definition

The specific workflow this study targets:

> **User uploads a Chinese pop song (华语流行) → app extracts the lead vocal melody → outputs a single-line piano sheet music (treble clef) the user can learn to play.**

This is the most technically tractable version of the melody extraction problem, for reasons detailed below.

### Why Vocal Melody Is the Easiest Case

Human voice is **monophonic** — one note at a time. This means:
- The hard problem of polyphonic pitch separation (multiple simultaneous notes) is avoided entirely
- CREPE tiny, a 6 MB monophonic pitch tracker, is more accurate than any polyphonic transcription model at this task
- The pipeline reduces to: **separate vocal stem → track single pitch → quantize to notes**

### Recommended Pipeline

```
Chinese song (MP3 / AAC / WAV)
         ↓
  [Step 1] Demucs (4-stem separation)          ~83 MB Core ML model
  → extract vocals stem (discard bass/drums/other)
         ↓
  [Step 2] CREPE tiny (monophonic pitch tracking)  ~6 MB Core ML model
  → f0 contour: pitch in Hz, frame by frame
         ↓
  [Step 3] Rhythm quantizer (custom Swift)
  → snap f0 to nearest musical note values (♩ ♪ 𝅘𝅥𝅯)
  → detect note onsets and offsets
  → assign MIDI pitch numbers
         ↓
  [Step 4] Sheet music renderer
  → single-line treble clef piano staff
  → scrolling piano roll or standard notation display
```

**Total bundled model size**: ~89 MB (well within iOS app size guidelines)

### Advantages Specific to Chinese Pop (华语流行)

| Factor | Why It Helps |
|---|---|
| Lead vocal is prominently mixed | Demucs separates it cleanly — Chinese pop production typically has vocals high in the mix |
| Vocal melody is monophonic | CREPE tiny achieves 85–92% F1 on monophonic content |
| Melody stays in a singable range (~C3–C6) | Fits naturally on a single treble clef staff without ledger line complexity |
| Legato singing style common in 华语流行 | Cleaner onset detection vs. highly melismatic or ornamented styles |
| Relatively clean studio productions | Less bleed between stems than live recordings |

### Honest Limitations for This Use Case

| Challenge | Severity | What Happens in Practice |
|---|---|---|
| Vibrato / pitch slides (滑音) | Medium | CREPE tracks the center pitch well; slides become a held note at the average pitch |
| 装饰音 (grace notes, ornaments) | Medium | Fast ornaments below ~100ms are typically dropped or merged into the main note |
| Rhythm quantization | Medium–High | Singers never sing exactly on the beat; the quantizer approximates to the nearest 16th note — output feels slightly mechanical |
| Harmony vocals / double-tracking | Low–Medium | If backing vocals are close in level to the lead, they bleed into the vocal stem and create spurious pitch detections |
| Demucs Core ML conversion | Medium | Community ports exist; expect 1–2 weeks of conversion and validation work |
| Rap / spoken-word sections | High | Pitch tracking is unreliable on rap; recommend detecting low-confidence sections and leaving them blank |

**Expected output quality for a typical Chinese pop ballad**: ~80–90% of notes correct for simple, legato vocal lines. Ornaments and rapid passages will be simplified. The result is recognizable and learnable on piano, not a professional-grade transcription.

### Build Complexity for This Specific Use Case

| Task | Effort |
|---|---|
| Demucs → Core ML conversion + validation | 1–2 weeks |
| CREPE tiny → Core ML conversion | 3–5 days |
| AVFoundation audio pipeline (file import, resampling) | 2–3 days |
| CREPE inference on vocal stem | 2–3 days |
| f0 → note event post-processing (onset detection, quantization) | 1–2 weeks |
| Piano sheet music renderer (SwiftUI, treble clef) | 2–3 weeks |
| File picker UI + progress flow | 3–5 days |
| Testing on real Chinese pop songs | 1–2 weeks |
| **Total (1 engineer)** | **~7–12 weeks** |

### Verdict

**Feasible. Recommended approach for MVP.**

The vocal melody use case is the right starting scope because it avoids the hardest part of the general problem (polyphonic separation of arbitrary instruments) while directly serving the stated user need. Chinese pop music's production characteristics — prominent, clean lead vocals, moderate tempo, legato phrasing — are favorable for this pipeline. The two required models (Demucs + CREPE tiny) are open-source, well-documented, and have community precedent for Core ML conversion.

---

## 1. ML Models for Melody Extraction / Music Transcription

### 1.1 Basic Pitch (Spotify Research)

**Architecture**

Basic Pitch is a lightweight, polyphonic pitch transcription model released by Spotify Research in 2022. It takes a mel spectrogram as input and produces three parallel outputs: note activation, pitch bend, and onset detection. The backbone is a shallow CNN (a few convolutional blocks, not a transformer), deliberately kept small for inference speed. It operates on audio resampled to 22,050 Hz with 229 mel bins per frame.

**Model Size**

The TensorFlow/Keras model is approximately 3–5 MB in its standard float32 form. After float16 or int8 post-training quantization, it can compress to roughly 1–2 MB. This is one of the smallest capable polyphonic transcription models available.

**Accuracy**

On standard AMT benchmarks (e.g., MAPS piano dataset, MedleyDB for melody), Basic Pitch achieves note-level F1 scores in the 70–80% range for monophonic content and somewhat lower for dense polyphonic piano. For guitar/voice melody extraction specifically, accuracy degrades on complex chords but remains competitive for single-note melody lines. It is explicitly not a state-of-the-art transcription model — it trades accuracy for size and speed.

**Core ML Conversion**

Spotify provides the model in TensorFlow SavedModel and ONNX formats. Conversion to Core ML is feasible via:
1. TF → ONNX (via `tf2onnx`)
2. ONNX → Core ML (via `coremltools` `convert()`)

OR directly:
1. TF SavedModel → Core ML (via `coremltools.convert()` with TF frontend)

The main friction points are:
- The mel spectrogram preprocessing must be reproduced on-device (Core ML does not include `librosa.filters.mel` natively)
- Some TF ops (especially around signal processing) may require custom layers or must be moved out of the model into Swift/Accelerate code
- The three output heads need careful mapping to Core ML `MLMultiArray` outputs

Overall difficulty: **Medium**. There are community reports of successful conversions (the ONNX route is more reliable). Expect 1–2 weeks of integration work.

**iOS Feasibility**

Very high. The model is small enough to bundle in the app, runs on the Neural Engine (A12+), and inference on a 10-second audio clip should be well under 500ms on an iPhone 14+. Memory footprint during inference is on the order of 50–100 MB including the spectrogram buffer.

**Cost**: Free / open source (Apache 2.0)

---

### 1.2 MT3 (Music Transcription with Transformers, Magenta/Google)

**Architecture**

MT3 is a large sequence-to-sequence transformer (T5-based) that treats music transcription as a language modeling problem: it maps raw audio spectrograms to token sequences representing notes, instruments, and timing. It was trained on a massive multi-instrument dataset and can handle piano, drums, guitar, bass, and more simultaneously.

**Model Size**

The full MT3 model is based on T5-Small to T5-Base configurations. T5-Small is ~60 MB in float32; T5-Base is ~250 MB. MT3 adds audio encoder layers on top, making the full model **200–500 MB** depending on configuration.

**Accuracy**

MT3 significantly outperforms Basic Pitch on multi-instrument transcription. On MAPS (piano) and Slakh2100 (multi-instrument) benchmarks, it achieves note F1 scores in the 80–90% range. It is state-of-the-art for multi-instrument AMT as of its publication.

**Mobile Feasibility**

Very poor for on-device use. Problems:
- Model size (200–500 MB) is on the edge or over what is practical to bundle
- T5-style transformer inference is memory-hungry at runtime — peak RAM usage during inference can exceed 1 GB
- Inference time for a 10-second clip would be 5–30+ seconds on an iPhone, even with Neural Engine optimization, because the autoregressive decoding is sequential
- Core ML conversion of T5-based models is technically possible but involves custom attention masking ops that coremltools handles imperfectly

**Verdict**: Not viable for real-time or near-real-time on-device iOS use. Would require cloud offload.

**Cost**: Apache 2.0 (but cloud inference has server costs)

---

### 1.3 Omnizart

**Architecture**

Omnizart is a Python toolkit from the Music and Culture Technology Lab (MCT Lab) that bundles multiple specialized models: piano transcription, chord recognition, beat tracking, drum transcription, etc. Each sub-model is typically a CNN or CRNN (Convolutional Recurrent Neural Network). The piano model is based on a U-Net-style architecture.

**Model Size**

Individual Omnizart sub-models range from ~30–150 MB in TensorFlow SavedModel format.

**Mobile Feasibility**

Poor to moderate:
- The models are TF-based and require non-trivial Core ML conversion
- Omnizart's preprocessing pipeline (custom CQT, harmonic stacking) is complex and deeply tied to the Python/librosa ecosystem — reproducing this in Swift is substantial work
- The CRNN models have recurrent layers (GRU/LSTM) that Core ML supports, but the full preprocessing graph is not easily portable
- No known community port to iOS exists

**Verdict**: Not recommended for iOS. Higher complexity than Basic Pitch with no practical size/accuracy advantage for single-melody use cases.

**Cost**: MIT license

---

### 1.4 Other Relevant Models (2024–2025)

#### Piano Transcription Inference (Bytedance / Kong et al.)
A CNN + Transformer hybrid focused on piano. Model size ~18 MB (the CNN-only variant). Very accurate for piano (F1 ~90%+ on MAPS). Core ML conversion is feasible — the model is relatively clean PyTorch. However, it is **piano-only**, not suitable for general melody from arbitrary instruments.

#### CREPE (monophonic pitch tracker)
CREPE is a CNN-based monophonic pitch tracker (not a full transcription model — it outputs f0 contours, not MIDI notes). The full model is ~130 MB but a "tiny" variant is ~6 MB. CREPE is excellent at tracking a single dominant pitch (voice, lead instrument) with very high accuracy on monophonic content. **This is the recommended model for the vocal melody use case.** The tiny model can be converted to Core ML straightforwardly and runs in real time on iPhone.

#### PESTO (2023)
A self-supervised pitch estimation model (ICASSP 2023). Smaller and faster than CREPE with competitive accuracy on monophonic content. PyTorch-based, Core ML conversion is feasible.

#### Demucs + CREPE (hybrid pipeline)
**The recommended pipeline for the target use case.** Use Demucs (source separation, ~83 MB for the 4-stem model) to isolate the vocal stem from a mix, then run CREPE tiny on the isolated vocal for pitch tracking. Two-stage pipeline with ~89 MB total model size and excellent accuracy for real-world Chinese pop recordings.

---

## 2. Model Comparison

| Model | Size (float32) | Mobile Inference (10s clip) | Accuracy (melody F1) | iOS Difficulty | License |
|---|---|---|---|---|---|
| Basic Pitch | ~4 MB | <500ms | 70–80% (polyphonic) | Medium | Apache 2.0 |
| CREPE tiny | ~6 MB | Real-time capable | 85–92% (monophonic) | Low–Medium | MIT |
| PESTO | ~5 MB | Real-time capable | ~88% (monophonic) | Medium | MIT |
| Bytedance Piano | ~18 MB | 1–3s | ~90% (piano only) | Medium | MIT |
| Omnizart | 30–150 MB | 5–15s | 75–85% | High | MIT |
| MT3 | 200–500 MB | 15–60s+ | 85–92% (multi-inst.) | Very High | Apache 2.0 |
| **Demucs + CREPE** ⭐ | **~89 MB** | **3–8s** | **85–90% (vocal)** | **High** | **MIT / Apache 2.0** |

⭐ Recommended for target use case

---

## 3. iOS-Specific Technical Considerations

### 3.1 Core ML Conversion Process

Standard pipeline:

```
PyTorch (.pt / .pth)  →  torch.onnx.export  →  ONNX (.onnx)
                                                      ↓
                                         coremltools.convert()
                                                      ↓
                                         .mlpackage / .mlmodel
```

Or for TensorFlow:
```
SavedModel / .h5  →  coremltools.convert(source='tensorflow')  →  .mlpackage
```

**Key friction points:**
- **Custom ops**: ops not in the Core ML op set require `MLCustomLayer` in Swift/Objective-C
- **Dynamic shapes**: Core ML prefers fixed input shapes; variable-length audio inputs need chunking
- **Quantization**: `coremltools` supports post-training quantization (`ct.optimize.coreml`) — float16 halves model size with minimal accuracy loss
- **Neural Engine targeting**: `compute_units=ct.ComputeUnit.ALL` enables NE use, but not all op types are NE-compatible

### 3.2 Audio Pipeline (AVFoundation)

```swift
AVAudioEngine
  └── inputNode (mic)
        └── installTap(onBus:bufferSize:format:block:)
              └── AVAudioPCMBuffer  →  convert to float32 array
                    └── accumulate ring buffer
                          └── dispatch chunk to inference queue
```

Key considerations:
- Default sample rate is device-dependent (44,100 or 48,000 Hz). Most ML models want 16,000 or 22,050 Hz — resample using `AVAudioConverter`
- Buffer sizes: typical tap buffer is 1024–4096 samples; accumulate ~0.5–2s before dispatching
- Threading: inference must run off the audio thread; use a dedicated `DispatchQueue` or `Task` with actor isolation (Swift 6)
- For file-based audio: `AVAudioFile` → read into `AVAudioPCMBuffer` → same pipeline

### 3.3 Mel Spectrogram Computation on iOS

**Recommended: Accelerate framework**
```swift
import Accelerate
// 1. Apply Hann window via vDSP
// 2. FFT via vDSP_fft_zrip
// 3. Power spectrum via vDSP_zvmags
// 4. Mel filterbank (precomputed matrix) via vDSP_mmul
// 5. Log compression via vvlogf
```
Fast (SIMD-accelerated), no dependencies. The mel filterbank matrix must be precomputed at init time using the **exact same parameters** as training (n_mels, fmin, fmax, sample_rate, n_fft). Mismatching these is the most common silent accuracy bug.

Budget 2–4 days of careful implementation and validation against Python/librosa reference output.

### 3.4 Memory Constraints

| Device RAM | Approximate app budget |
|---|---|
| iPhone 12/13 (4 GB) | ~2.5–3 GB |
| iPhone 14/15 (6 GB) | ~4–5 GB |

For Basic Pitch or CREPE tiny: total runtime memory ~100–200 MB — comfortably within limits. For MT3: peak usage may exceed 1 GB and risk jetsam (OOM kill).

---

## 4. Alternative Approaches

### 4.1 Cloud API Services

| Service | Capability | Latency | Cost |
|---|---|---|---|
| ACRCloud | Audio fingerprinting + metadata | 300–800ms | ~$0.001–0.01/query |
| Replicate.com | Hosts Basic Pitch, MT3 as serverless inference | 2–10s | ~$0.001–0.05/run |
| Google Cloud / AWS / Azure | No first-party music transcription APIs | — | — |
| Melodyne (Celemony) | Desktop SDK only, no public API | N/A | License |

No dominant low-cost cloud API for melody extraction exists comparable to speech-to-text. Replicate is the lowest-friction path for a cloud prototype.

### 4.2 Hybrid On-Device + Cloud

1. **On-device**: Real-time pitch tracking with CREPE tiny or Basic Pitch for live visual feedback
2. **Cloud**: After recording stops, send audio to a server running MT3 for a high-accuracy MIDI export

Best for non-real-time "import a song" flows.

---

## 5. Build Complexity Estimates

### Scenario A: Vocal melody → piano sheet (target use case) ⭐

| Task | Effort |
|---|---|
| Demucs → Core ML conversion + validation | 1–2 weeks |
| CREPE tiny → Core ML conversion | 3–5 days |
| AVFoundation audio pipeline (file import, resampling) | 2–3 days |
| CREPE inference on vocal stem | 2–3 days |
| f0 → note event post-processing (onset detection, quantization) | 1–2 weeks |
| Piano sheet music renderer (SwiftUI, treble clef) | 2–3 weeks |
| File picker UI + progress flow | 3–5 days |
| Testing on real Chinese pop songs | 1–2 weeks |
| **Total (1 engineer)** | **~7–12 weeks** |

### Scenario B: Basic Pitch on-device (polyphonic MVP)

| Task | Effort |
|---|---|
| Core ML conversion of Basic Pitch | 1–2 weeks |
| Mel spectrogram in Swift (Accelerate) | 3–5 days |
| AVFoundation audio pipeline | 2–3 days |
| Model inference + output decoding | 1 week |
| MIDI export (AudioToolbox) | 3–5 days |
| Piano roll UI | 1–3 weeks |
| Testing, tuning, edge cases | 1–2 weeks |
| **Total (1 engineer)** | **~8–14 weeks** |

### Scenario C: Hybrid (CREPE on-device + MT3 cloud)

Add 2–4 weeks for cloud backend. Total: **10–18 weeks**.

---

## 6. Key Technical Risks

1. **Mel spectrogram parameter mismatch** — most common source of silent accuracy degradation
2. **Core ML op compatibility** — certain ops may not convert cleanly; custom layers are labor-intensive
3. **Rhythm quantization quality** — converting a human vocal performance to quantized notation always loses expressiveness; managing user expectations is as important as the algorithm
4. **Demucs vocal bleed** — backing vocals and reverb tails bleed into the vocal stem; post-processing (e.g., confidence thresholding on CREPE output) can reduce spurious notes
5. **Rap / spoken-word sections** — pitch tracking breaks down; detect low-confidence regions and leave them blank rather than producing wrong notes
6. **License compliance** — Demucs (MIT) and CREPE (MIT) allow commercial use; verify before shipping

---

## 7. Recommendation

| Use Case | Recommended Approach |
|---|---|
| **Chinese vocal melody → piano sheet** ⭐ | **Demucs (vocal stem) + CREPE tiny** — most accurate for the stated use case, all on-device, ~89 MB total |
| Single melody (any monophonic instrument) | **CREPE tiny** alone if input is already a clean recording |
| Polyphonic / chord-aware transcription | **Basic Pitch** — best balance of size, accuracy, and conversion feasibility |
| Maximum accuracy (async, non-real-time) | **Hybrid: CREPE on-device + MT3 cloud** |

**Avoid for iOS**: MT3 on-device (too large/slow), Omnizart (preprocessing complexity), Basic Pitch alone on full-mix audio (no stem separation step means poor accuracy on typical pop productions).

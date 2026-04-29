<div align="center">

<svg width="64" height="64" viewBox="0 0 32 32" fill="none" xmlns="http://www.w3.org/2000/svg">
  <rect x="1.5" y="1.5" width="29" height="29" rx="4" stroke="#1d1d1f" stroke-width="1.5"/>
  <path d="M7 16C7 16 9.5 10 12 16C14.5 22 17 16 17 16C17 16 19.5 10 22 16C24.5 22 27 16 27 16" 
        stroke="#1d1d1f" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
  <path d="M7 19C7 19 9.5 13 12 19C14.5 25 17 19 17 19C17 19 19.5 13 22 19C24.5 25 27 19 27 19" 
        stroke="#1d1d1f" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" opacity="0.35"/>
</svg>

# AudioTwin

**Audio correspondence detection — runs entirely in your browser.**

</div>

> *"That sound on TikTok is also on YouTube, but are they the same recording?"*  
> AudioTwin answers by comparing two audio files and measuring their similarity, down to the precise temporal alignment.

---

## What it does

Load two audio sources. AudioTwin will:

1. **Fingerprint** both tracks using spectral delta encoding — a technique similar to the one used by Shazam
2. **Find the best alignment** between them through offset voting. If one clip starts mid-song, it locates the matching segment automatically
3. **Return a confidence score** (0–100%) and time offset, so you can determine immediately whether you are hearing the same recording

### Match tiers

| Score | Verdict | Meaning |
|---|---|---|
| ≥ 65% | High confidence match | Same recording, likely different encode |
| 40–65% | Probable match | Same source, possibly pitch-shifted or processed |
| 18–40% | Weak correlation | Shared samples, cover version, or similar genre |
| < 18% | No match | Different recordings |

---

## Supported formats

Any format your browser can decode natively:

- **MP3** (most common for TikTok / YouTube rips)
- **WAV**
- **OGG / Opus**
- **M4A / AAC**
- **FLAC** (in supported browsers)

---

## How to use it

1. **Drag and drop** an audio file onto the Source A panel, or click to browse
2. Do the same for Source B
3. Press **Analyze Tracks**
4. Read the score and examine the offset distribution chart — a sharp peak indicates a clean match

### URL loading

AudioTwin can fetch audio from a direct URL (for example, a `.mp3` link from a CDN).  
**YouTube, TikTok, and SoundCloud URLs will not work** — these platforms do not serve audio directly. Instead, download the audio first using a tool such as:

- [yt-dlp](https://github.com/yt-dlp/yt-dlp) — `yt-dlp -x --audio-format mp3 <URL>`
- [ffmpeg](https://ffmpeg.org/) — for conversion
- Any browser extension for audio extraction

Then upload the resulting file.

---

## Deploy to GitHub Pages

### Option A — Simplest (no build step)

1. Create a new GitHub repository (for example, `audiotwin`)
2. Upload `index.html` to the root of the repository
3. Go to **Settings → Pages → Source** and set it to **Deploy from branch → main / root**
4. Your app is live at `https://YOUR_USERNAME.github.io/audiotwin`

### Option B — With a custom domain

Same as Option A, then add a `CNAME` file to the repository root containing your domain:

```
audiotwin.yourdomain.com
```

Configure a CNAME DNS record pointing to `YOUR_USERNAME.github.io`.

---

## Technical details

### Algorithm

AudioTwin uses a three-part scoring approach:

**1. Spectral delta fingerprinting**  
Both tracks are resampled to 8 kHz mono. A 512-point Hann-windowed FFT is computed every 128 samples (~16 ms hop). Energy is measured in 8 logarithmically-spaced bands from 150 Hz to 4 kHz. Adjacent frames are compared: each band receives a `1` if energy rose, `0` if it fell — producing an 8-bit hash per frame.

**2. Offset voting (Shazam-style)**  
For every hash match between Track A and Track B, the temporal offset (frameA − frameB) receives a vote. The offset with the most votes is the best alignment. A genuine match produces a sharp peak; unrelated audio produces a flat distribution.

**3. Cosine similarity check**  
At the best offset, the spectral energy vectors of both tracks are compared using cosine similarity as a secondary validation signal.

The final score weights SNR of the vote distribution (45%), hash match ratio (35%), and cosine similarity (20%).

### Privacy

**Nothing leaves your browser.** All processing is done client-side using the Web Audio API and a pure JavaScript FFT implementation. No audio data is uploaded to any server.

### Performance

| Clip length | Processing time (approx.) |
|---|---|
| 30 seconds each | < 1 second |
| 60 seconds each | 1–3 seconds |
| Longer clips | Capped at 60 s for analysis |

Processing time depends on device performance. On most modern laptops and phones it is nearly instantaneous.

### Limitations

- The algorithm is optimised for finding the same recording. It works well across different bitrates, mild loudness changes, and re-encodes. It is not a melody matcher and will not reliably identify covers or songs with shared samples below ~40% score.
- Pitch-shifted versions (for example, sped-up TikTok audio) reduce confidence but often still score ≥ 40% if the shift is small.
- Very short clips (< 5 seconds) may produce unreliable results.
- Clips with a lot of silence or very sparse audio may produce false positives.

---

## Design

AudioTwin follows the principles of **Dieter Rams** and the **early Apple product design** language (the Snow White era, 1977–1983). The interface is built on the tenets of:

- **Less, but better** (*Weniger, aber besser*) — every element serves a purpose
- **Honest materials** — no synthetic gradients, no decorative noise; only light, shadow, and proportion
- **Pure surfaces** — warm off-white tones, restrained typography, and deliberate whitespace
- **Grid and order** — everything aligns to a strict underlying structure
- **Quiet feedback** — states are communicated through subtle color and measured motion, never through spectacle

The mark — two waveforms in parallel, one faint — represents the core idea: finding the hidden correspondence between two signals.

---

## File structure

```
audiotwin/
└── index.html     # entire app — no build tools, no dependencies, no server
└── README.md
```

---

## License

MIT — use it freely.

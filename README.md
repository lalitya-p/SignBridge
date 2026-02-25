# 🤟 SignBridge — Accessible Interview Platform

**SignBridge** is a real-time, browser-based video interview platform designed to bridge communication between **deaf/hard-of-hearing candidates** (using ASL) and **hearing recruiters** (using speech). It provides live ASL gesture recognition, speech-to-text transcription, and text chat — all within a single accessible web application.

> **No installation required.** SignBridge runs entirely in the browser as a single `index.html` file with zero backend dependencies for the core experience.

---

## 🔍 Problem Statement

Traditional video interview platforms lack built-in support for deaf and hard-of-hearing users. Candidates who communicate through sign language face significant barriers when interviewing with hearing recruiters, often requiring third-party interpreters or workarounds that disrupt the natural flow of conversation.

### Key Gaps Addressed

- **No real-time ASL recognition** in existing video call platforms
- **No integrated speech-to-text** visible to both participants during interviews
- **No fallback communication channel** when audio/video fails
- **Poor accessibility defaults** (contrast, text size, keyboard navigation)

---

## ✨ Features

### Core Communication

- **Real-Time ASL Translation** — Uses MediaPipe hand-tracking + a custom gesture classifier to recognize ASL signs and display them as text with confidence scores
- **Speech-to-Text (STT)** — Leverages the Web Speech API to transcribe recruiter speech into live text captions
- **Text Chat Fallback** — Full text chat available anytime via button or keyboard shortcut (`Ctrl+Shift+C`)
- **Unified Transcript Panel** — All communication (ASL, STT, chat) appears in a single, scrollable transcript with timestamps and confidence badges

### Video Calling

- **Peer-to-Peer WebRTC** — Direct video/audio connection via PeerJS (no media server required)
- **Meeting Link System** — Recruiters generate a unique meeting code; candidates join via a shareable URL (`?code=XXXXXXXX`)
- **Camera/Mic Controls** — Toggle camera and microphone independently with real-time status sync to the other participant
- **Lobby with Preview** — Pre-call device check with live camera preview, mic/camera toggle, and name entry

### ASL Recognition Engine

- **10 Built-in Signs** — `Hello`, `Thank You`, `Yes`, `No`, `Please`, `Sorry`, `Help`, `I Love You`, `Good`, `Stop`
- **Custom Model Upload** — Recruiters can upload a SignSpeak v3+ JSON model to add custom signs
- **Model Sync** — Custom models are automatically shared with the candidate via the peer connection
- **Temporal Smoothing** — Sliding-window averaging over recent frames for more stable predictions
- **Calibration Support** — Per-sign calibration samples for improved accuracy
- **Confidence Overlay** — Real-time top-4 prediction display with confidence bars

### Accessibility

- **High Contrast Mode** — Toggle via accessibility panel
- **Large Text Mode** — Adjustable font size
- **Keyboard Shortcuts**:
  - `Ctrl+Shift+V` — Toggle camera
  - `Ctrl+Shift+M` — Toggle microphone
  - `Ctrl+Shift+C` — Switch to text chat
- **Focus-visible outlines** on all interactive elements
- **ARIA-friendly** role cards with keyboard activation

### UX & Session Management

- **Guided Onboarding** — Step-by-step setup with role selection (Candidate/Recruiter), tutorial slides, and permission requests
- **Recruiter-Controlled Start** — Only the recruiter can initiate the interview session
- **Room Capacity Enforcement** — One candidate per session; additional joiners are blocked with a clear message
- **Graceful Disconnection** — End-call overlay notifies the remaining participant, with session cleanup
- **Connection Status Indicators** — Real-time badge showing connection state
- **Feature Hints** — Contextual tips guiding users to enable ASL or STT features

---

## 🛠 Tech Stack

| Layer | Technology |
|---|---|
| **Frontend** | Vanilla HTML/CSS/JavaScript (single-file SPA) |
| **Video/Audio** | WebRTC via [PeerJS](https://peerjs.com/) v1.5.4 |
| **Hand Tracking** | [MediaPipe Hands](https://google.github.io/mediapipe/solutions/hands) v0.4 |
| **Speech-to-Text** | Web Speech API (`SpeechRecognition`) |
| **Styling** | CSS Custom Properties (design tokens), responsive grid |
| **Font** | [Nunito](https://fonts.google.com/specimen/Nunito) (UI) + [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) (code/data) |

---

## 🚀 Getting Started

### Prerequisites

- A modern browser with WebRTC support (Chrome recommended for full STT support)
- Camera and microphone access
- Internet connection (for PeerJS signaling server and CDN assets)

### Quick Start

1. **Clone the repository:**
   ```bash
   git clone https://github.com/<your-username>/signbridge.git
   cd signbridge
   ```

2. **Open `index.html` in a browser:**
   ```bash
   # Option A: Direct file open
   open index.html

   # Option B: Local server (recommended for camera permissions)
   npx serve .
   # or
   python3 -m http.server 8000
   ```

3. **Start an interview:**
   - **Recruiter:** Select "Recruiter" role → complete onboarding → enter your name → click "Create Meeting" → share the meeting link
   - **Candidate:** Open the shared link (which contains `?code=...`) → enter your name → wait for recruiter to start

### Optional: Custom ASL Model

Recruiters can upload a custom ASL model (SignSpeak v3+ JSON format) during the interview via the model upload input. The model is automatically synced to the candidate's session.

---

## 📁 Project Structure

```
signbridge/
├── index.html          # Entire application (HTML + CSS + JS)
└── README.md           # This file
```

The application is architected as a **single-file SPA** with four screens:

| Screen | Purpose |
|---|---|
| **Onboarding** | Role selection, tutorial walkthrough, permission grants |
| **Lobby** | Device preview, name entry, meeting creation/joining |
| **Interview** | Video call with ASL/STT overlays, transcript, and chat |
| **Thank You** | Post-session feedback screen |

---

## 🔧 Configuration

### STUN Servers

The default configuration uses Google's public STUN servers:

```javascript
iceServers: [
  { urls: 'stun:stun.l.google.com:19302' },
  { urls: 'stun:stun1.l.google.com:19302' }
]
```

For production use behind restrictive firewalls, consider adding a TURN server.

### ASL Recognition Parameters

| Parameter | Default | Description |
|---|---|---|
| `HOLD_FRAMES` | `6` | Consecutive frames required to confirm a sign |
| `COOLDOWN` | `1500` ms | Minimum time between repeated detections of the same sign |
| `SMOOTH_WINDOW` | `4` | Number of frames for temporal smoothing |
| `minDetectionConfidence` | `0.7` | MediaPipe hand detection threshold |
| `minTrackingConfidence` | `0.5` | MediaPipe hand tracking threshold |

---

## ⚠️ Known Limitations

- **Speech-to-Text** requires Chrome or a Chromium-based browser (Web Speech API dependency)
- **ASL recognition** is gesture-based (static signs only); it does not capture motion-based or facial-expression signs
- **Peer-to-peer** connections may fail behind symmetric NATs without a TURN server
- **No persistent storage** — transcripts are lost when the session ends
- **Single-page file** — scaling to a multi-file architecture is recommended for production

---

## 🗺 Roadmap

- [ ] Add TURN server support for reliable NAT traversal
- [ ] Expand ASL vocabulary beyond 10 built-in signs
- [ ] Support motion-based and two-handed ASL signs
- [ ] Transcript export (PDF/TXT)
- [ ] Session recording with consent
- [ ] Multi-language STT support
- [ ] Mobile-optimized layout improvements

---

## 📄 License

This project is open source. See [LICENSE](LICENSE) for details.

---

## 🙏 Acknowledgments

- [MediaPipe](https://google.github.io/mediapipe/) by Google for hand-tracking (Lugaresi et al., 2019)
- [PeerJS](https://peerjs.com/) for simplified WebRTC peer-to-peer connections
- [Web Speech API](https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API) for browser-native speech recognition

### References

- Lugaresi, C., Tang, J., Nash, H., McClanahan, C., Uboweja, E., Hays, M., Zhang, F., Chang, C., Yong, M. G., Lee, J., Chang, W., Hua, W., Georg, M., & Grundmann, M. (2019). MediaPipe: A framework for building perception pipelines. *arXiv preprint arXiv:1906.08172*. https://arxiv.org/abs/1906.08172
- Mozilla Developer Network. (n.d.). Web Speech API. *MDN Web Docs*. https://developer.mozilla.org/en-US/docs/Web/API/Web_Speech_API

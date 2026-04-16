<div align="center">

# 🖥️ DeskKnoledge

**Your always-on desk companion for effortless, active learning.**

*A sleek, 3D-printed smart display that quizzes you throughout the day — powered by Home Assistant, Whisper, and AI-generated content.*

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Home Assistant](https://img.shields.io/badge/Home%20Assistant-Compatible-41BDF5?logo=home-assistant)](https://www.home-assistant.io/)
[![Whisper API](https://img.shields.io/badge/Whisper-Voice%20Transcription-412991?logo=openai)](https://openai.com/research/whisper)
[![3D Printed](https://img.shields.io/badge/Hardware-3D%20Printed-orange)](./3d-files/)

</div>

---

## 🧠 What Is DeskKnoledge?

DeskKnoledge is an open-source, DIY smart learning device that lives on your desk and turns idle moments into micro-learning sessions. Think of it as a digital flashcard machine — one that knows when you're in the room, picks the right moment to challenge you, and actually *listens* to your answers.

The device features a slim, portrait-oriented display inside a custom 3D-printed enclosure, tilted slightly backward for comfortable at-a-glance reading, with physical buttons along the bottom edge for quick interaction. No phone in hand, no app to open — just look up, answer, and learn.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🏠 **Presence Detection** | Integrates with Home Assistant to know when you're at your desk |
| ⏰ **Smart Scheduling** | Plays a gentle chime at configured intervals during your presence |
| 🃏 **Spaced Repetition** | Questions you skip or get wrong resurface later — just like Anki |
| 🧮 **Quick Maths** | Rapid mental-arithmetic challenges to keep your brain sharp |
| 📚 **Topic Quizzes** | Deep-dive questions on any subject you choose: physics, history, languages, and more |
| 🎤 **Voice Answers** | Speak your answer aloud — Whisper transcribes and AI judges correctness |
| 🤖 **Autonomous Research** | The question engine searches current sources to keep content fresh and relevant |
| 🔘 **Physical Buttons** | Dedicated hardware buttons for *Answer*, *I Don't Know*, and *Skip* |

---

## 🖼️ The Hardware

<div align="center">

```
┌─────────────────────────┐  ◄── 3D-printed enclosure
│                         │
│   ┌─────────────────┐   │
│   │                 │   │
│   │   E-ink / TFT   │   │  ◄── Portrait display, tilted ~15° backward
│   │   Display       │   │
│   │                 │   │
│   │  ┌───────────┐  │   │
│   │  │  Question │  │   │
│   │  │  + Answer │  │   │
│   │  │  Options  │  │   │
│   │  └───────────┘  │   │
│   └─────────────────┘   │
│                         │
│  [✓ Answer] [? Later]   │  ◄── Physical buttons
└─────────────────────────┘
```

</div>

### Enclosure Design

- **Form factor:** Tall, slim portrait display — similar to a standing index card
- **Tilt:** Screen is angled roughly 10–15° backward so it's readable while sitting at a desk without raising your head
- **Buttons:** Two or more tactile push-buttons recessed below the screen:
  - **Answer** — activates the microphone for a spoken response
  - **I Don't Know / Later** — dismisses the question and schedules it for later review
- **Material:** PLA or PETG (files provided in `/3d-files`)
- **Mount:** Flat base; optionally mounts behind a monitor via VESA adapter bracket (also included)

---

## ⚙️ How It Works

```
┌─────────────────────────────────────────────────────────────────┐
│                        DeskKnoledge Flow                        │
└─────────────────────────────────────────────────────────────────┘

  1. 🏠  Home Assistant detects you entering the room
            │
            ▼
  2. ⏰  Scheduled trigger fires (configurable interval, e.g. every 20 min)
            │
            ▼
  3. 🔔  Speaker plays a soft chime — "heads up, quiz time"
            │
            ▼
  4. 🃏  Question engine selects the next card
        ┌───────────────────────┐
        │  Math?  →  quick sum  │
        │  Topic? →  AI-fetched │
        │           quiz Q      │
        └───────────────────────┘
            │
            ▼
  5. 📺  Question displayed on the screen
            │
        ┌───┴───────────────────────┐
        │                           │
        ▼                           ▼
  [Answer button]            [I Don't Know / Later]
        │                           │
        ▼                           ▼
  🎤  Microphone opens        📅  Question re-queued
  🗣️  User speaks answer           with increased
  🤖  Whisper transcribes          review interval
  ✅  AI checks correctness        (spaced repetition)
        │
        ▼
  📊  Result shown on screen
  (Correct ✅ / Incorrect ❌ + explanation)
        │
        ▼
  🗂️  Score & interval updated in local database
```

---

## 🗂️ Repository Structure

```
DeskKnoledge/
│
├── 3d-files/               # STL & source CAD files for the enclosure
│   ├── enclosure-front.stl
│   ├── enclosure-back.stl
│   ├── button-cap.stl
│   └── vesa-bracket.stl
│
├── firmware/               # Microcontroller code (ESP32 / Raspberry Pi)
│   ├── main.py
│   └── config.example.yaml
│
├── ha-integration/         # Home Assistant YAML automations & blueprints
│   ├── presence-trigger.yaml
│   └── quiz-schedule.yaml
│
├── question-engine/        # AI-powered question generator & scheduler
│   ├── generator.py        # Calls LLM API to create fresh questions
│   ├── researcher.py       # Autonomously fetches current facts/news
│   ├── scheduler.py        # Spaced-repetition logic (SM-2 algorithm)
│   └── topics.yaml         # User-defined topic list
│
├── voice/                  # Whisper integration & answer evaluation
│   ├── transcribe.py       # Sends audio to Whisper API
│   └── evaluator.py        # Checks transcription against expected answer
│
└── README.md
```

> **Note:** Installation and setup guides will be added as the project matures. For now the repository focuses on the hardware files and core logic modules.

---

## 🤖 Intelligence Stack

### Presence Awareness — Home Assistant
DeskKnoledge uses a [Home Assistant](https://www.home-assistant.io/) automation to watch room-occupancy sensors (mmWave radar, PIR, Bluetooth presence, or any other HA-compatible sensor). When you enter the room the quiz schedule activates; when you leave, it pauses — no interruptions when you're away.

### Question Generation — LLM + Autonomous Research
The question engine combines two strategies:

1. **Topic quizzes** — A large language model (OpenAI, local Ollama, etc.) generates questions and multiple-choice or open-ended answers for any topic listed in `topics.yaml`. Topics can be anything: *quantum physics*, *Roman history*, *Spanish vocabulary*, *financial derivatives*.

2. **Current events & research** — A lightweight web-scraping module fetches recent articles, Wikipedia updates, or RSS feeds related to your topics so that questions never feel stale. The LLM then turns those summaries into quizable facts.

### Voice Input — OpenAI Whisper
When you press **Answer**, a short audio clip is recorded and sent to the [Whisper API](https://platform.openai.com/docs/guides/speech-to-text). The transcription is passed to an evaluator that uses fuzzy matching + LLM reasoning to decide whether the answer is correct — handling synonyms, partial answers, and slight mis-pronunciations gracefully.

### Spaced Repetition — SM-2
Correct answers push a card further into the future. Skipped or wrong answers bring it back sooner. The scheduler follows the proven [SM-2 algorithm](https://www.supermemo.com/en/blog/application-of-a-computer-to-improve-the-results-obtained-in-working-with-the-supermemo-method) — the same one behind Anki — so your weakest areas always stay top-of-mind.

---

## 🖨️ 3D Printing

All enclosure parts are designed for consumer FFF/FDM printers (bed size ≥ 220 × 220 mm).

| Part | Layer Height | Infill | Supports |
|---|---|---|---|
| `enclosure-front.stl` | 0.2 mm | 20 % | Yes (screen cutout) |
| `enclosure-back.stl` | 0.2 mm | 20 % | No |
| `button-cap.stl` | 0.15 mm | 40 % | No |
| `vesa-bracket.stl` | 0.2 mm | 35 % | No |

**Recommended material:** PETG for durability; PLA+ works fine for indoor use.

---

## 🗺️ Roadmap

- [x] Core concept & hardware design
- [ ] Finalise enclosure STL files
- [ ] ESP32 / Raspberry Pi firmware
- [ ] Home Assistant blueprint (plug-and-play automation)
- [ ] Question engine v1 (topic quizzes + maths)
- [ ] Whisper voice-answer integration
- [ ] Autonomous research module
- [ ] Web dashboard for topic & schedule management
- [ ] Multi-user / household support

---

## 🤝 Contributing

Contributions are very welcome! Whether you're improving the 3D model, adding a new quiz topic, or enhancing the AI pipeline — open a pull request or start a discussion in the Issues tab.

Please keep contributions focused on one area per PR and include a short description of what changed and why.

---

## 📄 License

This project is released under the [MIT License](LICENSE). You are free to use, modify, and distribute it — commercially or non-commercially — as long as the original license notice is retained.

---

<div align="center">

*Built with curiosity. Learn something new every day.*

</div>

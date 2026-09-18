# VerseLoom

> The app that makes memorized Scripture stick.

VerseLoom is a free, offline iOS Scripture memorization app built around spaced repetition — the same memory science behind Anki and Duolingo, applied to Bible verses.

**[Website](https://chuckdevcis.github.io/VerseLoom) · [Privacy Policy](https://chuckdevcis.github.io/VerseLoom/privacy.html) · [Terms of Use](https://chuckdevcis.github.io/VerseLoom/terms.html)**

---

## Features

- **Spaced repetition (SM-2)** — Reviews are scheduled at the exact moment before you'd forget, compounding retention over time
- **Progressive-reveal flow** — Full verse → half blanked → first letters → blank recall with word hints
- **Again / Hard / Good / Easy ratings** — Your feedback drives the scheduling algorithm
- **Preset tracks** — Psalm 23 and 10 beloved KJV passages bundled and ready to import
- **Manual verse entry** — Add any reference and text; supports KJV, WEB, ESV, NIV, NASB, NLT, CSB
- **Due Today / Learning / Mastered** — Home screen groups verses by retention state, not streak count
- **Daily notifications** — A quiet 9 AM reminder for however many verses are due
- **Fully offline** — No network requests, no account, no backend, ever
- **Completely free** — No subscription, no paywall, no in-app purchases

---

## Screenshots

_Coming soon._

---

## Tech stack

| Layer | Technology |
|---|---|
| Language | Swift 5.10 |
| UI | SwiftUI (iOS 17+) |
| Persistence | SwiftData |
| Scheduling | SM-2 algorithm (custom, no dependencies) |
| Notifications | UserNotifications framework |
| Bundled data | KJV public-domain JSON |

---

## Requirements

- Xcode 16+
- iOS 17+ deployment target
- No third-party Swift packages

---

## Getting started

```bash
git clone https://github.com/chuckdevcis/VerseLoom.git
cd VerseLoom
open VerseLoom.xcodeproj
```

Select the `VerseLoom` scheme, choose a simulator or device, and press **Run** (⌘R).

---

## Project structure

```
VerseLoom/
├── Models/
│   ├── Verse.swift          # SwiftData model: reference, text, translation
│   ├── ReviewCard.swift     # SwiftData model: SM-2 state + due date
│   └── ReviewLog.swift      # SwiftData model: per-review audit log
├── Scheduling/
│   └── SM2Scheduler.swift   # Pure SM-2 algorithm + ReviewQuality enum
├── Data/
│   ├── PresetTrackLoader.swift
│   └── preset_tracks.json   # Psalm 23 + 10 popular KJV verses
├── Notifications/
│   └── NotificationManager.swift
├── Views/
│   ├── HomeView.swift        # Due Today / Learning / Mastered
│   ├── AddVerseView.swift    # Manual verse entry form
│   ├── ReviewView.swift      # Progressive-reveal review flow
│   └── PresetTrackView.swift # Browse and import preset tracks
├── ContentView.swift
└── VerseLoomApp.swift
```

---

## Running tests

```bash
# From Xcode
⌘U

# Or from the command line
xcodebuild test -scheme VerseLoom -destination 'platform=iOS Simulator,name=iPhone 16'
```

The test suite covers the SM-2 scheduler: interval progression, ease-factor bounds, failure reset, and relative performance of each rating choice. All business logic for the scheduler lives in `SM2Scheduler.swift` as a pure function so it can be tested without spinning up the app or database.

---

## Spaced repetition algorithm

VerseLoom implements the classic [SM-2 algorithm](https://www.supermemo.com/en/blog/application-of-a-computer-to-improve-the-results-obtained-in-working-with-the-supermemorization-method). The core function is a pure, dependency-free Swift struct:

```swift
SM2Scheduler.schedule(state: ReviewCardState, quality: Int) -> ReviewCardState
```

Rating mapping:

| Button | Quality | Effect |
|---|---|---|
| Again | 1 | Reset: repetitions → 0, interval → 1 day |
| Hard  | 3 | Success: interval grows, ease factor decreases |
| Good  | 4 | Success: interval grows, ease factor stable |
| Easy  | 5 | Success: interval grows fastest, ease factor increases |

Ease factor floors at 1.3 (per SM-2 spec). After two successful reviews the interval multiplies by the ease factor on every subsequent review, creating the exponential growth that moves a verse from Learning → Mastered.

---

## Bible translation and copyright

The bundled preset tracks use the **King James Version (KJV)**, which is in the public domain in the United States. Verse text you enter manually is your responsibility — short quotations of copyrighted translations for personal memorization are generally fair use, but consult the publisher's guidelines when in doubt. The **World English Bible (WEB)** is another public-domain option available in the translation picker.

---

## Roadmap (post-MVP)

- [ ] CloudKit sync across devices
- [ ] Speech-to-text recitation checking (on-device Speech framework)
- [ ] WidgetKit Lock Screen widget ("3 verses due")
- [ ] Audio TTS playback for listening mode

Monetization is not on the roadmap. VerseLoom is and will remain free.

---

## Contributing

Bug reports and pull requests are welcome. Please open an issue first for anything beyond a small fix so we can discuss the approach.

---

## License

MIT License — see [LICENSE](LICENSE) for details.

Bundled KJV verse text is in the public domain.

---

## Contact

Chuck Silver · [cisilva2510@gmail.com](mailto:cisilva2510@gmail.com) · [@chuckdevcis](https://github.com/chuckdevcis)

AutoDarts ↔ DartCounter Bridge

A Tampermonkey userscript that seamlessly connects [AutoDarts](https://autodarts.io/) with [DartCounter](https://dartcounter.net/), enabling automatic score entry without WebDriver or browser automation.

## 🎯 Features

- **Automatic Score Transfer**: Reads dart throws from AutoDarts and auto-enters scores into DartCounter
- **No WebDriver Required**: Pure JavaScript solution using Tampermonkey's GM storage for cross-tab communication
- **Game Rules Validation**: 
  - Bust detection (score exceeds remaining)
  - Invalid finish detection (must finish on double or bull)
  - One-left prevention
- **Smart Player Detection**: Automatically identifies which player you are based on UI indicators
- **Privacy-Focused**: Bridge state resets on page reload, no persistent tracking
- **User-Friendly UI**: Simple toggle button on DartCounter with clear status indicators

## 📋 Prerequisites

- [Tampermonkey](https://www.tampermonkey.net/) browser extension
- Active [AutoDarts](https://autodarts.io/) setup running on `http://127.0.0.1:3180` or `http://192.168.x.x:3180`
- [DartCounter](https://app.dartcounter.net/) account

## 🚀 Installation

1. Install [Tampermonkey](https://www.tampermonkey.net/) for your browser
2. Click on the Tampermonkey icon → "Create a new script"
3. Copy the entire content of [`ad2dc.js`](./ad2dc.js) and paste it
4. Save (Ctrl+S / Cmd+S)

## 💡 Usage

1. **Open AutoDarts** in a browser tab (`http://127.0.0.1:3180` or your AutoDarts server)
   - The script runs silently in the background (no UI)
   - Heartbeat signal confirms AutoDarts is ready

2. **Open DartCounter** (`https://app.dartcounter.net/`)
   - A "Bridge" toggle button appears in the top-right corner
   - Click to enable the bridge

3. **Play darts**
   - Scores are automatically transferred from AutoDarts to DartCounter
   - The bridge validates game rules before entering scores

4. **Reload behavior**
   - Bridge automatically disables on page reload
   - Must be manually re-enabled each session (privacy feature)

## 🔧 How It Works

```
┌─────────────┐                    ┌──────────────┐
│  AutoDarts  │                    │ DartCounter  │
│  (Producer) │                    │  (Consumer)  │
└──────┬──────┘                    └──────┬───────┘
       │                                  │
       │ 1. Polls dart data every 500ms   │
       │    (when bridge enabled)         │
       │                                  │
       │ 2. Detects round completion      │
       │    (3 darts thrown)              │
       │                                  │
       ├──► GM_setValue("autodarts_round", {...})
       │                                  │
       │                                  ◄─── 3. Listens for round data
       │                                  │    GM_addValueChangeListener
       │                                  │
       │                                  │ 4. Validates score
       │                                  │    - Bust check
       │                                  │    - Finish validation
       │                                  │
       │                                  │ 5. Auto-enters score
       │                                  │    - Focus input
       │                                  │    - Dispatch events
       │                                  │    - Press Enter
       │                                  │
```

## 🛡️ Privacy & Security

- **No persistent state**: Bridge status is stored in-memory only
- **Session-bound**: Resets on page reload
- **Local-only**: All communication happens via Tampermonkey's local GM storage
- **No external requests**: Zero network calls beyond normal page operations
- **No tracking**: No user data collection or analytics

## 🎮 Supported Game Rules

- ✅ Standard 501/301/Cricket games
- ✅ Bust detection (over-throw)
- ✅ Double-out validation
- ✅ Bull finish validation
- ✅ One-left prevention
- ✅ Automatic player index detection

## 🐛 Troubleshooting

**"AutoDarts not found" message:**
- Ensure AutoDarts is running on `http://127.0.0.1:3180` or your configured IP
- Check that the AutoDarts tab is open in the same browser
- Heartbeat must be fresh (<10 seconds old)

**Scores not transferring:**
- Verify both tabs have the script active (check Tampermonkey icon)
- Enable the bridge on DartCounter (button should be green)
- Check browser console for logs (prefix: `[AD↔DC]`)

**Wrong player scoring:**
- The script auto-detects player index on first throw
- If incorrect, reload DartCounter and start a new match

## 📝 Configuration

Key constants in the script (lines 24-35):

```javascript
const CFG = {
  autodartsSpanClass: "css-1ny2kle",        // AutoDarts DOM selector
  pollIntervalMs: 500,                       // Polling frequency (ms)
  scoreSelectors: [...],                     // DartCounter input selectors
  notify: true                               // Enable notifications
};
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## 📄 License

MIT License - feel free to use and modify as needed.

## 🙏 Acknowledgments

- [AutoDarts](https://autodarts.io/) - Automated darts scoring system
- [DartCounter](https://dartcounter.net/) - Online darts scoring platform
- [Tampermonkey](https://www.tampermonkey.net/) - Userscript manager

## 📊 Version

Current version: **1.0.26**

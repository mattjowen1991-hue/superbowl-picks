# 🎰 Super Bowl LX Draft Machine

A slot machine-style PWA for randomly drafting NFL playoff teams among friends. Each player spins to get one AFC and one NFC team for the Super Bowl LX playoffs.

## 🏈 How It Works

### The Draft
1. **7 players** draft in order: Joe → Jarv → Matt → Mark → Gaz → Liam → Ben
2. Each player spins **twice** - once for an AFC team, once for an NFC team
3. The slot machine randomly assigns from the 14 playoff teams (7 AFC, 7 NFC)
4. Once a team is drafted, it's marked as "TAKEN" and can't be selected again
5. Draft progress syncs across all devices in real-time via JSONBin

### PIN Protection
- Each player has a unique 4-digit PIN to prevent others from spinning on their turn
- PINs are required before the first spin of each turn
- Once unlocked, both AFC and NFC spins can be completed
- Cancel button allows exiting if wrong player tries to spin

### Live Team Data
- Pulls current playoff teams from ESPN's API automatically
- Falls back to hardcoded teams if ESPN is unavailable
- Teams display with official logos and seeding

## 📁 File Structure

```
superbowl-draft/
├── Assets/
│   ├── joe.png, jarv.png, matt.png, mark.png, gaz.png, liam.png, ben.png
│   ├── afc-logo.png
│   └── nfc-logo.png
├── icons/
│   ├── icon-192.png
│   └── icon-512.png
├── index.html          # Main app (single-file PWA)
├── manifest.json       # PWA manifest
├── sw.js              # Service worker for offline support
└── README.md
```

## ⚙️ Configuration

### Player PINs (in index.html)
```javascript
PLAYERS: {
  joe:  { name: 'Joe',  avatar: 'Assets/joe.png', pin: '2847' },
  jarv: { name: 'Jarv', avatar: 'Assets/jarv.png', pin: '8351' },
  matt: { name: 'Matt', avatar: 'Assets/matt.png', pin: '2922' },
  mark: { name: 'Mark', avatar: 'Assets/mark.png', pin: '5926' },
  gaz:  { name: 'Gaz',  avatar: 'Assets/gaz.png', pin: '6194' },
  liam: { name: 'Liam', avatar: 'Assets/liam.png', pin: '6357' },
  ben:  { name: 'Ben',  avatar: 'Assets/ben.png', pin: '9563' }
}
```

### Admin PIN
- **Reset PIN:** `2922` (Matt's PIN doubles as admin)
- Used to reset the entire draft

### JSONBin Storage
```javascript
JSONBIN_BIN_ID: '695b078fd0ea881f4054b2a0',
JSONBIN_API_KEY: '$2a$10$C7BY0Kl0u74gNn/kXO7xNuayWv493/f1jAxlHUmx3ENADQKDii61C'
```

## 🎮 Features

### Slot Machine Effects
- **Spinning animation** with realistic easing
- **Shake effect** that reduces as the reel slows
- **Sound effects:** Lever pull, ticking, and victory fanfare
- **Mute button** in top-right corner

### Visual Indicators
- **Gold border** highlights current player
- **TAKEN overlay** on drafted teams (greyed out)
- **Winner display** shows team logo after each spin
- **Draft board** tracks all picks at bottom

### Modes
- **Live Draft:** Active spinning mode
- **View Results:** See completed draft results

### Export Options
- **Copy Results:** Text format for sharing
- **Export picks.json:** JSON format for other apps

## 🔧 Service Worker

The `sw.js` handles:
- **Caching** static assets for offline use
- **Network-first** for API calls (JSONBin, ESPN)
- **Cache-first** for static files

### Updating the App
When making changes, bump the cache version in `sw.js`:
```javascript
const CACHE_NAME = 'sb-draft-v9';  // Increment this
```

Then users need to:
1. Close all tabs with the app
2. Reopen the app
3. Or: Clear site data in browser settings

For PWA on mobile:
1. Delete the app from home screen
2. Clear browser cache
3. Re-add to home screen

## 📱 Installation (PWA)

### iOS
1. Open in Safari
2. Tap Share button
3. Select "Add to Home Screen"
4. Name it and tap Add

### Android
1. Open in Chrome
2. Tap the menu (⋮)
3. Select "Add to Home screen"
4. Confirm installation

## 🔄 Data Flow

```
ESPN API → fetchPlayoffTeams() → CONFIG.AFC_TEAMS / CONFIG.NFC_TEAMS
                                          ↓
                                    renderReels()
                                          ↓
User Spin → spinReel() → PIN Check → actuallySpinReel()
                                          ↓
                                    finishSpin()
                                          ↓
                              saveDraftData() → JSONBin
                                          ↓
                              renderDraftBoard() (all devices)
```

## 🛠️ Troubleshooting

### Teams not loading from ESPN
- Check browser console for errors
- Verify ESPN API endpoint is accessible
- Fallback teams will be used automatically

### Draft not syncing
- Check JSONBin credentials
- Verify network connectivity
- Check browser console for API errors

### PWA not updating
1. Bump `CACHE_NAME` version in `sw.js`
2. Clear browser cache
3. Unregister service worker in DevTools → Application → Service Workers

### PIN not working
- Ensure correct 4-digit PIN
- Check that it's your turn (gold border on your card)
- Use Cancel button if stuck

## 📊 Data Structure (JSONBin)

```json
{
  "picks": {
    "joe": { "afcTeam": "Denver Broncos", "nfcTeam": "Seattle Seahawks" },
    "jarv": { "afcTeam": "Buffalo Bills", "nfcTeam": null },
    ...
  },
  "replayData": [
    { "playerId": "joe", "conference": "afc", "team": "Denver Broncos", "timestamp": 1704567890123 }
  ],
  "draftStarted": true,
  "draftComplete": false,
  "lastUpdated": "2026-01-06T12:00:00.000Z"
}
```

## 🎨 Customization

### Adding/Removing Players
1. Update `DRAFT_ORDER` array
2. Add/remove from `PLAYERS` object
3. Add avatar images to `Assets/`

### Changing Colors
Edit CSS variables in `:root`:
```css
--gold: #ffd700;
--afc-red: #c41e3a;
--nfc-blue: #1e56a0;
```

### Adjusting Spin Duration
In `actuallySpinReel()`:
```javascript
const spinDuration = 3500;  // milliseconds
const spinCycles = 3;       // full rotations
```

## 📝 License

Private project for personal use among friends.

---

**Good luck in the playoffs! 🏆**

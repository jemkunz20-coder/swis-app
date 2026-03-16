# SWIS App — AI Handover Document
> Last updated: March 2026  
> If you're a new Claude session, read this first before touching any code.

---

## What Is SWIS?

**SWIS = See What I See**  
A real-time group content sharing web app. Users create or join a "room" using a short code, then share photos, links, or text messages that all room members can see instantly — like a shared second screen for a group.

**Live URL:** `https://jemkunz20-coder.github.io/swis-app/`  
**GitHub repo:** `https://github.com/jemkunz20-coder/swis-app`  
**Structure:** Single file — everything is in `index.html`. No build step, no npm, no framework. Pure HTML/CSS/JS.

---

## Project History

- Started in **Lovable.ai** (`jemkunz20-coder/SWIS`) which produced an initial React/TypeScript prototype
- Ported to a single vanilla HTML file for simplicity and GitHub Pages hosting
- Originally hosted on work GitHub account (`jkconnollyengland-stack/swis-app`)
- Transferred to personal GitHub account (`jemkunz20-coder/swis-app`) in March 2026
- All development now happens in the personal account repo
- The Lovable repo (`jemkunz20-coder/SWIS`) and work repo (`jkconnollyengland-stack/swis-app`) are no longer maintained

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | Vanilla HTML, CSS, JavaScript (ES modules) |
| Realtime DB | Firebase Realtime Database (europe-west1) |
| Hosting | GitHub Pages (auto-deploys from main branch) |
| QR codes | qrcodejs via cdnjs |
| Icons | Lucide Eye icon (inline SVG) |

### Firebase Config
```js
const firebaseConfig = {
    apiKey: "AIzaSyCeDlaBjRz-jPj6TZmEsg0yNvfR7fv0l4E",
    authDomain: "swis-9b0ce.firebaseapp.com",
    databaseURL: "https://swis-9b0ce-default-rtdb.europe-west1.firebasedatabase.app",
    projectId: "swis-9b0ce",
    storageBucket: "swis-9b0ce.firebasestorage.app",
    messagingSenderId: "25689930290",
    appId: "1:25689930290:web:a5370855e0dd7104071a2b"
};
```

### Firebase Rules (already set correctly)
```json
{
  "rules": {
    "rooms": {
      "$roomId": {
        ".read": true,
        ".write": true
      }
    }
  }
}
```

---

## Firebase Data Structure

```
rooms/
  {ROOMCODE}/
    members/
      {userId}: { name, joined }
    shares/
      {userId}: { type, data, timestamp }
```

- `type` is one of: `photo`, `link`, `text`
- `data` for photos is a base64 data URL
- Room codes are random words + number e.g. `PUB42`, `CAFE7`
- Users are auto-named `User_xxxxxx` (random 6-char suffix)
- Users are cleaned up from Firebase on tab close / leave

---

## App Flow

1. **Splash screen** (3 seconds, animated) → fades out
2. **Welcome screen** — create room or enter a code to join
3. **Room screen** — 3 tabs: Home (carousel), Friends, Groups
4. QR code button → overlay with scannable link to join the room

---

## UI Components

### Splash Screen
- Originated from Lovable.ai's `StartupLogo.tsx` component
- Soft blue→white→orange gradient background
- Orange gradient outer circle (128px) with `animate-ping` ripple ring
- Blue gradient inner circle (112px) with `animate-pulse` border ring
- Lucide Eye SVG (64px, strokeWidth 1.5) in white
- SWIS title in orange→blue gradient text
- "See What I See" + "Share your world instantly" subtitles
- 5 connection dots (alternating large orange / small blue) with staggered pulse

### Welcome Screen
- Background: soft blue/orange gradient (Lovable style)
- Glass-effect white card
- Lovable-style header logo: orange→blue gradient square, white eye SVG, "SWIS / See What I See" text
- Create Room button + join code input

### Header Bar (room screen)
- White glass card, `min-height: 56px`, `margin: 8px 12px`
- Logo: orange→blue gradient square (`border-radius: 10px`), white eye SVG
- Single div approach — do NOT use nested absolute-positioned divs (broken previously)
- Room code in dark slate, bold
- QR button (orange→blue gradient) fully inside the white card

### Bottom Navigation (from Lovable)
- 3 tabs: **Home** (house icon), **Friends** (eye icon), **Groups** (people icon)
- Active tab highlighted in orange with subtle orange pill background
- White glass bar

### Home Tab — Carousel
- Instagram-style horizontal swipe carousel
- Each member gets a phone-shaped card (9:19.5 aspect ratio)
- White/glass cards with blue border accents
- Shows: member name, LIVE badge if sharing, shared content
- Touch swipe with smooth follow + snap
- Orange dot indicators, arrow buttons for desktop
- Control buttons: Share (gradient), Stop (red, only when sharing), Leave (grey)

### Friends Tab
- Shows all members currently in the room
- Avatar with initials, green dot = sharing, grey dot = not sharing

### Groups Tab
- Shows current room with member count and active count

### Share Modal
- Slides up from bottom (sheet style)
- Three options: Photo, Link, Message

---

## Design Language

- **Background:** `linear-gradient(135deg, #dbeafe, #bfdbfe, #fed7aa)` — soft blue/orange
- **Primary accent:** `#f97316` (orange)
- **Secondary accent:** `#3b82f6` (blue)
- **Button gradient:** `linear-gradient(135deg, #f97316, #3b82f6)`
- **Cards:** `rgba(255,255,255,0.92-0.95)` with `backdrop-filter: blur(10px)`
- **Text primary:** `#1e293b` | **Text secondary:** `#64748b`
- **Font:** System font stack

---

## Known Issues & Watch-outs

### Previously Fixed
- Header bleed bug — gradient bleeding over white header
- Logo rendering bug — nested absolute divs broke, use single div
- Header collapse — fixed with `min-height` and explicit flex-direction

### Watch Out For
- Firebase listeners are nested inside `updateScreens()` — works but creates extra reads
- `setupSwipe()` clones the carousel container to remove old listeners — always re-query after
- Photos stored as base64 — large images may hit Firebase size limits
- Session via `sessionStorage` — closing tab starts fresh session
- Auto-join from URL param (`?room=CODE`) has 3.6 second delay for splash screen

---

## Deployment

Push `index.html` to `main` branch of `jemkunz20-coder/swis-app`. GitHub Pages auto-deploys in ~60 seconds.

**How to update (step by step):**
1. Go to `https://github.com/jemkunz20-coder/swis-app`
2. Click `index.html`
3. Click pencil ✏️ icon
4. Select all (Ctrl+A / Cmd+A), delete, paste new code
5. Click green "Commit changes" button twice
6. Wait 60 seconds, check live URL

**QR codes point to:**
`https://jemkunz20-coder.github.io/swis-app/?room={ROOMCODE}`

**Important:** If you change the GitHub username or repo name, update the QR code URL in the JavaScript section of index.html (search for `github.io` to find it).

---

## What Was Taken From Lovable.ai

| Feature | Status |
|---|---|
| Splash screen animated logo | ✅ Ported to vanilla CSS |
| Blue/orange colour scheme | ✅ Applied throughout |
| Bottom navigation (Home/Friends/Groups) | ✅ Ported |
| Header logo style (square gradient) | ✅ Ported |
| Welcome screen card design | ✅ Inspired by Lovable |
| Friends list with status dots | ✅ Built using real Firebase members |

---

## Next Steps / Ideas (not yet built)

- Username customisation
- Room expiry / auto-cleanup
- Image compression before upload
- Real browser screen share API
- Push notifications when someone joins/shares
- Persistent friends list
- Admin/host role
- Consider commercialising — charge for premium rooms/features

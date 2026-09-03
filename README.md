# 🎨 HueMore — A Year in Colour

> **A private, mood-aware journal that turns every day you write about into a colour — and your whole year into a beautiful emotional map.**

🔗 **Live:** [huemore.vercel.app](https://huemore.vercel.app) · Built with vanilla HTML/CSS/JS + Firebase · Installable PWA

---

## ✨ What is HueMore?

Write ~250 words about your day. HueMore reads the *feeling* of your words and colours that day on a GitHub-style year grid — from **Dysphoric** (cool blue) to **Euphoric** (warm amber). Day by day, your year becomes a colour map of *you*.

## 🌟 Features

### 📔 Journal & Mood Engine
- One entry per day (~250 words) with live word counter & limit
- **Built-in offline sentiment analyzer** (lexicon with negation & intensifier handling)
- **Optional LLM mode** — plug in a free Google Gemini API key for AI interpretation
- 7-step mood spectrum: Dysphoric · Low · Heavy · Even · Lifted · Bright · Euphoric
- Year heatmap with hover tooltips, click-to-open day view & spectrum marker
- Stats: entries, day streak, total words, prevailing hue
- Page-flip year navigation · animated pen writing the spectrum

### 🗒️ Sticky Notes
- Ivory sticky notes on any day (~100 words) — **never analyzed, always private**
- Marked with a tiny ivory corner on the day cell

### 🔐 Accounts & Security
- Firebase Email/Password auth with cross-device cloud sync
- Local device accounts + Guest mode
- Show/hide password eye toggle · Change password (requires current password)
- Server-enforced Firestore security rules (privacy is not just UI)

### 👤 Profile & Mates
- Unique username, display name, profile picture (upload → Google avatar → initial fallback)
- **Mates:** search by username, send/accept/decline requests, view a Mate's journal read-only
- Request badge notifications

### 🕵️ Privacy by Design
- Dashboard visibility: `None` / `Only Mates` / `All`
- Entry visibility: `None` / `Only Mates` — *public entries are impossible by design*
- Everything private by default

### 📱 Progressive Web App
- Installable on Android / iOS / desktop with custom icon
- Offline support (service worker, network-first updates)
- Every `git commit` = automatic deploy for all users

## 🛠️ Tech Stack

| Layer | Tech |
|---|---|
| Frontend | Vanilla HTML / CSS / JS — single file, zero build step |
| Backend | Firebase Auth + Cloud Firestore (free Spark plan) |
| Hosting | Vercel (auto-deploy from GitHub) |
| Typography | Fraunces + Karla (Google Fonts) |

## 📁 Project Structure

```
├── index.html            # The entire app (UI + logic)
├── sw.js                 # Service worker (offline + smart updates)
├── manifest.webmanifest  # PWA manifest
├── favicon.png           # Browser tab icon
├── icon-192.png          # PWA icon
├── icon-512.png          # PWA icon
├── icon-maskable.png     # Adaptive launcher icon
└── README.md
```

## 🚀 Run Your Own Copy

1. **Fork** this repository.
2. Create a free [Firebase](https://console.firebase.google.com) project:
   - Enable **Email/Password** authentication
   - Create a **Cloud Firestore** database
   - Copy your web-app config into `FIREBASE_CONFIG` in `index.html`
3. Publish the **Firestore rules** below.
4. Deploy the repo to **Vercel / Netlify / Cloudflare Pages** (static — no build command needed).
5. Add your deployed domain under Firebase → Authentication → Settings → **Authorized domains**.

## 🔐 Recommended Firestore Rules

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    function signed() { return request.auth != null; }
    function owner(uid) { return signed() && request.auth.uid == uid; }
    function mateId(a, b) { return a < b ? a + '~' + b : b + '~' + a; }
    function isMate(a, b) { return exists(/databases/$(database)/documents/mates/$(mateId(a, b))); }

    match /profiles/{uid} {
      allow read: if signed();
      allow write: if owner(uid);
    }
    match /requests/{id} {
      allow create: if signed() && request.resource.data.from == request.auth.uid;
      allow read, delete: if signed() && (resource.data.to == request.auth.uid || resource.data.from == request.auth.uid);
    }
    match /mates/{id} {
      allow read, create, delete: if signed() && (resource.data.a == request.auth.uid || resource.data.b == request.auth.uid);
    }
    match /users/{uid}/entries/{k} {
      allow read: if owner(uid) || (signed() && isMate(uid, request.auth.uid));
      allow write: if owner(uid);
    }
    match /users/{uid}/stickies/{k} {
      allow read, write: if owner(uid);
    }
  }
}
```

## 🔒 Security Notes

- HTTPS everywhere (TLS in transit); data encrypted at rest by Firebase
- Passwords never touch app code — handled entirely by Firebase Auth
- Privacy enforced **server-side** by Firestore rules, not just hidden in the UI
- Entries are *not* end-to-end encrypted — choose visibility settings wisely

## 🧭 Roadmap

- [ ] Push notifications for Mate requests
- [ ] Export journal as PDF
- [ ] Custom colour themes

## 👨‍💻 Author

**Shouvik Baidya** — [GitHub](https://github.com/Baidya1483)

---

*Made with 🎨 for everyone whose feelings deserve a colour.*

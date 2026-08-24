# 🌾 Fasal Raksha
**AI-assisted farmer–buyer matching platform to reduce crop wastage**

Fasal Raksha connects farmers directly with crop buyers so produce sells
before it spoils. A farmer describes their crop — by typing or by
speaking — and the platform matches them against every buyer in the
marketplace, ranked by crop, price, quantity and location fit.

This build is a **single self-contained HTML file**: no server, no build
step, no install. Open it in a browser and it runs.

---

## ✨ Features

| Feature | Description |
|---|---|
| 👨‍🌾 Farmer Dashboard | Describe a crop in natural language; get extracted crop/quantity/price/location and ranked buyer matches |
| 🏪 Buyer Dashboard | Post a buying profile; browse and search farmer listings ranked by fit |
| 🎯 Smart Matching | Match % computed from crop, price, quantity and location fit — not hardcoded |
| 🔐 Farmer Verification | Sign-up/sign-in flow that checks a Farmer Verification ID before a farmer can list crops |
| 🎙️ Voice Input | Speak your crop details in English, Hindi, Bengali or Punjabi — transcribed live into the message box |
| 🌐 Multilingual | Language selector drives both the text parser and the voice-recognition locale |
| 📴 Works Offline | Local parser needs no internet or API key; an optional Groq key enables live LLM parsing |

---

## 🚀 Getting started

No installation needed.

1. Download `FasalRakshak.html`.
2. Open it in any modern browser (Chrome recommended for voice input).
3. Click **Get Started** to pick Farmer or Buyer, or use the nav bar.

There is nothing to build, deploy, or configure for local use — all data
is stored in the browser via `localStorage`.

---

## 🔐 Farmer verification — how it works (and its limits)

To keep the marketplace genuine, a farmer must verify a **government-issued
Farmer Verification ID** (e.g. a PM-KISAN-style registration number) before
they can list crops.

- On sign-up, the ID is checked against a registry via
  `verifyFarmerIdWithGovDB()`.
- A match returns the farmer's registered name, district and state for
  confirmation before the account is created.
- A non-match is rejected with a clear explanation.

**Important:** because this build is a static, offline HTML file, this
check runs against a small **mock dataset bundled in the code**
(`GOV_FARMER_REGISTRY`) — it does **not** call a real government system.
Demo IDs to try:

```
PMK-WB-2201-04871   PMK-UP-1904-11290   PMK-PB-1102-08823
PMK-BR-2005-03345   PMK-MH-1607-09981
```

For production, this lookup must move to a secure backend: the browser
should never hold a government API key or call a government verification
endpoint directly. The intended real flow:

1. Farmer submits their Farmer ID from this page.
2. Your backend calls the actual government registry / eKYC API (with
   proper auth, HTTPS, and the farmer's consent).
3. The backend returns only a verified / not-verified result (plus minimal
   profile fields) to the frontend — never the raw government dataset.

The relevant code is commented at the point of integration to make this
swap straightforward.

---

## 🎙️ Voice input

The mic button next to the crop description box uses the browser's native
**Web Speech API** (`SpeechRecognition` / `webkitSpeechRecognition`):

- Recognition language is set from the farmer's selected language
  (English → `en-IN`, Hindi → `hi-IN`, Bengali → `bn-IN`, Punjabi → `pa-IN`).
- Transcribed text is appended live into the message box as the farmer
  speaks.
- If the browser doesn't support speech recognition, or microphone access
  is denied, the app shows a clear message instead of failing silently.

**Browser support:** works best in Chrome (desktop and Android). Safari and
Firefox have limited or no support for the Web Speech API.

---

## 🧠 How matching works

`matchScore(sell, buy)` blends four signals into a single percentage:

- **Crop match** — exact match scores highest, partial/synonym match scores
  moderately.
- **Location match** — same city scores highest.
- **Price fit** — how close the buyer's offer is to the farmer's ask.
- **Quantity fit** — how well supply and demand quantities line up.

Scores are clamped to a realistic 55–98% range and used to rank buyers for
farmers and listings for buyers.

---

## 🗂️ Project structure

Everything lives in one file:

```
FasalRakshak.html
├── <style>   — all CSS (dark theme, responsive layout)
├── <body>    — #app (rendered by JS), #modal-root, #toast-wrap
└── <script>  — data layer, mock gov registry, router, render functions,
                action handlers, voice input, exposed as window.__fr
```

Key JS sections:
- **Data layer** — `loadDB` / `saveDB` (buyers, listings, farmer accounts)
- **Session** — `loadSession` / `saveSession` (which farmer is signed in)
- **Local parser** — `parseCropMessage`, `findCropInText`, `findCityInText`
- **Auth** — `renderAuth`, `handleAuthSubmit`, `verifyFarmerIdWithGovDB`
- **Voice** — `toggleVoiceInput`, `setMicUI`
- **Views** — `renderHome`, `renderFarmer`, `renderBuyer`, `renderAuth`

---

## ⚠️ Current limitations

- No real backend — all data is local to the browser (`localStorage`).
- Farmer ID verification checks a **demo dataset**, not a real government
  database (see above).
- No password/OTP-based security — sign-in relies on Farmer ID + phone
  number matching a locally stored record.
- Voice input depends on browser support for the Web Speech API.

## 🛣️ Suggested next steps for production

1. Move farmer ID verification to a secure backend integrated with a real
   government registry (PM-KISAN / state Agristack / similar).
2. Replace `localStorage` with a real database and API layer shared across
   devices.
3. Add OTP-based phone verification for sign-in.
4. Add buyer verification/KYC if required by the initiative.
5. Expand the local parser or route farmer messages through a
   privacy-reviewed LLM pipeline for more robust language understanding.

---

## 📄 License / usage

Prototype build for demonstration and internal review purposes.

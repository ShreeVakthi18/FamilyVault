# FamilyVault
> **Real-time elder fraud detection and family intervention system**

---

## The Problem

Every day in India, elderly people receive calls from scammers posing as SBI bank officers, TRAI officials, or police. A 10–15 minute call of fabricated panic is all it takes — and ₹5,000 to ₹2 lakh disappears through UPI before anyone can stop it. Their children are in different cities with no idea it is happening, and by the time they find out, the money is gone forever.


## The Core Insight

Elder fraud always follows one predictable sequence that normal everyday behaviour never follows. An unknown number calls for a long time, and a UPI app is opened within minutes of that call ending. A normal person never opens PhonePe or GPay immediately after a long call from an unknown number. That exact combination is unique to elder fraud, and it is the entire foundation of FamilyVault's detection logic.

---

## Solution Overview

FamilyVault silently watches for the fraud pattern in the background and intercepts the transaction before a single rupee moves, giving the family real-time control to cancel or approve.

### Pattern Detection

- FamilyVault monitors for the combination of an unknown caller followed by a call duration exceeding six minutes.
- It flags any UPI application opened within ten minutes of that call ending as a high-risk event.
- Every fraud signal is weighed together to calculate a risk score between 0 and 100.
- Any transaction scoring above 70% risk is automatically placed on hold before it can be processed.

### Transaction Intervention

- The payment is intercepted before it reaches the bank for processing.
- The UPI screen is frozen with a waiting-for-family-approval overlay so Amma cannot proceed.
- The transaction is held open inside a three-minute decision window that mirrors real UPI processing time.
- The transaction is either released or permanently cancelled based on the family member's response within that window.

### Real-Time Family Alert

- The moment Amma taps Pay, a fraud alert document is written directly to Firebase Firestore.
- The son or daughter's dashboard reacts instantly through a live onSnapshot listener with no page refresh required.
- The alert displays full fraud context including call duration, caller status, which UPI app was opened, amount, and time elapsed since the call ended.
- The entire alert delivery from tap to dashboard takes under one second.

### AI Fraud Assessment

- Gemini AI receives all four fraud signals together and analyses the combined pattern.
- It returns a plain-English reason explaining exactly why the transaction was flagged.
- A visual risk bar fills from 0 to 100% giving an instant at-a-glance read of the threat level.
- The final AI decision is one of three outcomes: HOLD, REVIEW, or NORMAL.

### Family Decision Control

- The dashboard presents two clear action buttons: Cancel Transaction and This is Safe.
- A single tap updates the Firestore document status and the decision propagates immediately.
- Amma's screen reacts in under one second, either showing the secured confirmation screen or releasing the payment.
- The entire loop from fraud detection to family resolution completes in real time with no server involved at any step.

---

## Full Demo Flow

```
Amma receives an unknown call lasting 11 minutes
                    ↓
She opens PhonePe and taps Pay ₹5,000
                    ↓
FamilyVault intercepts and writes the alert to Firestore
                    ↓
Son's dashboard fires instantly with full fraud context
                    ↓
Son sees: unknown caller, 11-minute call, PhonePe opened, 3-minute gap, ₹5,000 at risk, Risk Score 88%
                    ↓
Son taps Cancel and Firestore is updated immediately
                    ↓
Amma's screen shows ₹5,000 Secured
```

---

## Risk Score Breakdown

| Signal | Points Added |
|---|---|
| Caller was unknown and not in contacts | +30 |
| Call duration exceeded 6 minutes | +25 |
| UPI app opened within 10 minutes of call ending | +25 |
| Transaction amount exceeded ₹1,000 | +8 |
| **Total risk score for this demo** | **88% ** |

- A score above 70% triggers an automatic HOLD and sends a family alert immediately.
- A score between 40% and 70% triggers a REVIEW where the family is alerted but the payment is not blocked.
- A score below 40% is classified as NORMAL and no action is taken.

---

## System Architecture

```
Amma's Phone           Firestore DB            Son's Dashboard
─────────────          ────────────            ───────────────

[Tap Pay]   ──writes──► alerts/doc  ◄──listens── [onSnapshot fires]

            ◄──listens─ alerts/doc  ──writes──── [Tap Cancel]
[Safe Screen]           status: cancel
```

- There is no backend server involved at any point in the flow.
- There is no API relay layer adding latency or failure points.
- Both screens share a single Firestore database where one writes and the other reacts instantly.
- Firestore handles all storage, real-time syncing, and rule enforcement entirely on its own.

---

## Tech Stack

| Layer | Technology | Cost |
|---|---|---|
| Frontend | HTML, CSS, and JavaScript | Free |
| Realtime Database | Firebase Firestore | Free |
| Web Hosting | Firebase Hosting | Free |
| AI Fraud Assessment | Gemini AI | Free |
| Backend Server | Not required | — |

> **Total infrastructure cost: ₹0**

---

## Why Existing Solutions Fall Short

| Existing Solution | Critical Gap |
|---|---|
| Bank fraud alerts | These are sent only after the transaction has already completed and money has left the account |
| Call blocking apps | These block the call itself but have no awareness of UPI activity that follows the call |
| Caller ID apps | These identify the phone number but cannot detect or react to what the victim does after hanging up |
| Police complaints | These are filed after the fact and money is rarely recovered once a UPI transfer is completed |
| Elderly awareness training | Scammers engineer fear specifically to override training and panic wins in the moment every time |

None of these solutions connect the call pattern to the UPI activity that follows it. That connection is the key insight, and FamilyVault is the only system that bridges both sides of the fraud in real time.

---

## Live Demo

| Screen | URL |
|---|---|
| Son's Dashboard | `https://familyvault-demo.web.app/index.html` |
| Amma's Phone | `https://familyvault-demo.web.app/parent.html` |

> Open both URLs on separate devices simultaneously to experience the full two-screen live demo as intended.

---

## Roadmap

- [ ] A native Android app that monitors calls and UPI apps in the background using Android's Accessibility Service.
- [ ] Real Gemini API integration that performs dynamic fraud analysis instead of a hardcoded demo score.
- [ ] Push notifications so the family dashboard does not need to be open in advance to receive alerts.
- [ ] Support for multiple family members who can all receive and respond to alerts simultaneously.
- [ ] A known scammer number database that flags repeat offenders before the call even ends.
- [ ] A WhatsApp fallback alert that reaches the family even if they miss the dashboard notification.

---


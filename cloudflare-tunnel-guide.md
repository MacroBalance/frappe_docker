# Cloudflare Tunnel + WARP — Team Access Guide
> For accessing `inventory.macrobalance.in` securely

---

## 🧠 How It Works (Simple Overview)

There are **two separate components** — each installed on a **different machine**:

| Component | Installed On | Purpose |
|-----------|-------------|---------|
| **Cloudflare Tunnel** (`cloudflared`) | The **server** (Docker/WSL PC) | Makes the server reachable via Cloudflare |
| **Cloudflare WARP** | Every **team member's device** | Gives authorized access to the protected site |

```
┌──────────────────────────────────┐              ┌──────────────────────────┐
│  SERVER (Docker + WSL)           │              │  Team Member's Device    │
│                                  │              │                          │
│  Frappe / ERPNext (Docker)       │              │  Browser                 │
│  └── cloudflared (Tunnel) ───────── Cloudflare ──── WARP App ──────────── │
│       runs 24/7 in background    │              │  (installed & logged in) │
│                                  │              │                          │
│  ✅ Tunnel set up ONCE here       │              │  ✅ WARP installed here   │
└──────────────────────────────────┘              └──────────────────────────┘
```

---

## ❓ Why Do We Need the Tunnel?

Your server (running Docker on WSL) has **no public IP address** — it is hidden behind your home/office router. Without the tunnel, there is no way for Cloudflare to reach it.

```
❌ Without Tunnel:
   Team Member → inventory.macrobalance.in → ??? (unreachable)
   Your PC is behind NAT — nobody can find it

✅ With Tunnel:
   Your PC (cloudflared) keeps an outbound connection open to Cloudflare
   Team Member → WARP → Cloudflare → Tunnel → Your PC ✅
```

> **The Tunnel is like a secret pipe from your server to Cloudflare.**
> It only needs to be set up once on the server — not on every device.

---

## ❓ Why Do We Need WARP?

The Tunnel is protected by **Cloudflare Zero Trust** — meaning random people on the internet cannot access `inventory.macrobalance.in` even if they know the URL.

WARP is what proves to Cloudflare that a team member is **authorized** to enter.

```
Without WARP → Cloudflare blocks access ❌
With WARP (logged into org) → Cloudflare allows access ✅
```

---

## ✅ What Each Person Needs to Do

### 🖥️ Server Admin (One-Time Setup — Already Done)
- [x] Docker + WSL running on server PC
- [x] Frappe/ERPNext running in Docker
- [x] `cloudflared` tunnel configured and running inside Docker
- [x] Cloudflare Zero Trust policy set to require WARP

> No further action needed on the server side.

---

### 👤 New Team Member Setup (Every Device)

#### Step 1 — Download & Install WARP
Go to: **https://one.one.one.one/**
Choose your platform: Windows / Mac / Linux / iOS / Android

#### Step 2 — Connect to the MacroBalance Zero Trust Org

1. Open the **WARP app**
2. Click the **hamburger menu** (≡) → **Preferences** → **Account**
3. Click **"Login with Cloudflare Zero Trust"**
4. Enter the team name:
   ```
   macrobalance
   ```
   *(or whatever your Cloudflare team slug is)*

#### Step 3 — Authenticate
- A browser window will open
- Login with your **work email** (Google login or email OTP)
- Once authenticated, WARP will show **"Connected"**

#### Step 4 — Access the Site
Open your browser and go to:
```
https://inventory.macrobalance.in
```

Login with Frappe credentials:
```
Username: Administrator
Password: (as provided by admin)
```

---

## 📋 Quick Checklist for New Members

```
[ ] 1. Install Cloudflare WARP from https://one.one.one.one/
[ ] 2. Open WARP → Preferences → Account → Login with Zero Trust
[ ] 3. Enter team name: macrobalance
[ ] 4. Authenticate with your work email
[ ] 5. WARP shows "Connected" ✅
[ ] 6. Open browser → https://inventory.macrobalance.in
[ ] 7. Login with provided credentials
```

---

## ⚠️ Common Mistakes

| Mistake | Fix |
|--------|-----|
| Installed WARP but not logged into org | Go to WARP → Account → Login with Zero Trust |
| Using personal WARP (not org) | Regular WARP won't work — must login to `macrobalance` org |
| Trying to access without WARP connected | Connect WARP first, then open the browser |
| Wrong team name entered | Confirm the exact team slug with your admin |

---

## 🔄 WARP vs Tunnel — At a Glance

| | WARP | Tunnel |
|--|------|--------|
| **What it is** | Client-side VPN-like app | Server-side connector to Cloudflare |
| **Installed on** | Every team member's device | Server only (once) |
| **Who sets it up** | Each team member themselves | Server admin (already done) |
| **Purpose** | Proves you're authorized | Makes the server reachable |

---

*Guide prepared for MacroBalance internal team — June 2026*

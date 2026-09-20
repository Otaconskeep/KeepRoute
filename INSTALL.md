# Install KeepRoute (step-by-step)

You install the **same KeepRoute field UI** we run on OtaconsKeep hardware — on **your** machine.

This is a **detailed self-install**, not a one-click installer. Follow the steps in order.

**What you are installing**

- **KeepRoute UI** — providers on the left, ask box on the right (the screen operators use)  
- **OmniRoute** underneath — upstream open-source routing gateway  
- **Optional: Mission Controller** — full KeepRoute 1.0 missions / checkpoints / Auto policy  

The UI alone still lets you ask Auto / providers through OmniRoute. Persistent missions need Mission Controller as well.

Public page with screenshots: https://otaconskeep-site.otaconskeep.workers.dev/keeproute/#install

---

## 0. What you need first

| Need | Notes |
|---|---|
| A computer you control | Linux is the path documented here |
| Windows | Use **WSL2 Ubuntu** and run the Linux steps inside it |
| **Python 3.10+** | `python3 --version` |
| **Docker** | To run OmniRoute |
| **pip** | `python3 -m pip --version` |
| GPU | Optional — for Local AI. No GPU? Use cloud providers only |

Optional foundation: install [OtaconsKeep Lite / Otacon](https://github.com/Otaconskeep/otacons-ai-ecosystem) if you also want the broader Keep agent ecosystem. **KeepRoute does not require Lite to run.**

### Check the basics

```bash
python3 --version
docker --version
python3 -m pip --version
```

If Docker is missing on Ubuntu/Debian:

```bash
# Install Docker Engine using Docker's official docs for your distro, then:
sudo usermod -aG docker "$USER"
# log out and back in (or reboot), then:
docker ps
```

---

## 1. Get the KeepRoute UI package

### Option A — from this GitHub repo (recommended if you are already here)

```bash
git clone https://github.com/Otaconskeep/KeepRoute.git
cd KeepRoute/KeepRoute-UI
ls
# You should see: desk/   docker-compose.omniroute.yml   README.txt   keeproute-ui.service.example
```

Or: GitHub → green **Code** → **Download ZIP** → extract → open the `KeepRoute-UI` folder inside.

### Option B — from the website zip

1. Open https://otaconskeep-site.otaconskeep.workers.dev/keeproute/#install  
2. Download **KeepRoute-UI.zip**  
3. Unzip somewhere permanent:

```bash
mkdir -p ~/keeproute
cd ~/keeproute
unzip ~/Downloads/KeepRoute-UI.zip
cd KeepRoute-UI   # or whatever the extracted folder is named
```

---

## 2. Install OmniRoute (the routing engine)

OmniRoute is the upstream open-source data plane. KeepRoute uses it; we did not invent it.  
Upstream: https://github.com/diegosouzapw/OmniRoute

From inside `KeepRoute-UI`:

```bash
cd /path/to/KeepRoute/KeepRoute-UI   # adjust to your path

# Set YOUR OWN first-login password (do not reuse examples from the internet)
export OMNIROUTE_INITIAL_PASSWORD='pick-a-strong-password'

docker compose -f docker-compose.omniroute.yml up -d
docker ps | grep omniroute
```

### What this compose file does

- Runs `diegosouzapw/omniroute:latest`  
- Binds OmniRoute to **loopback only**: `127.0.0.1:20127` on your host → container port `20128`  
- Persists OmniRoute data in a Docker volume  

### First login / providers

1. Open OmniRoute in a browser (same computer). With this compose file the host URL is:

   **`http://127.0.0.1:20127`**

2. Sign in with the password you set in `OMNIROUTE_INITIAL_PASSWORD`.  
3. Connect providers there (Claude / OpenAI / Cursor / xAI / Ollama, etc.).  
4. Create or copy an **OmniRoute API key** for the KeepRoute UI.

If the dashboard path differs slightly by OmniRoute version, use whatever login/providers UI the upstream image shows after start — the important part is: OmniRoute is healthy on `127.0.0.1:20127` and you have an API key.

---

## 3. Install Python deps and start KeepRoute UI

```bash
cd /path/to/KeepRoute/KeepRoute-UI

python3 -m pip install --user flask

mkdir -p desk/state
echo '{}' > desk/state/credentials.json
chmod 600 desk/state/credentials.json

# IMPORTANT: compose publishes OmniRoute on host port 20127
export OMNIROUTE_HOST=http://127.0.0.1:20127
export OMNIROUTE_DESK_PORT=20129
export OMNIROUTE_DESK_BIND=127.0.0.1

python3 desk/app.py
```

Leave that terminal open.

### Open the UI

In your browser:

**http://127.0.0.1:20129/**

You should see the **KEEPROUTE** screen (providers on the left, ask box on the right).

### Optional — start on login (systemd user unit)

```bash
mkdir -p ~/.config/systemd/user
cp keeproute-ui.service.example ~/.config/systemd/user/keeproute-ui.service
# Edit the unit: WorkingDirectory + Environment paths must match your install
systemctl --user daemon-reload
systemctl --user enable --now keeproute-ui.service
```

Also use the desktop/URL shortcuts under `desk/shortcuts/` if you want a clickable launcher.

---

## 4. Add providers and keys (inside KeepRoute)

In the KeepRoute UI click **ADD PROVIDERS**.

| Field | What to do |
|---|---|
| **OmniRoute API key** | Usually enough for Auto — paste from OmniRoute dashboard → Save Keys |
| **Claude** | Connect in OmniRoute first, then confirm under Your Providers |
| **Codex / OpenAI** | Same pattern |
| **Cursor** | Optional coding executor |
| **Grok** | Optional |
| **Local AI** | Connect Ollama (or similar) through OmniRoute. No GPU? Skip Local |

**Never** paste API keys into Discord, email, GitHub issues, or the public website. Keys stay on your computer inside KeepRoute / OmniRoute (`desk/state/credentials.json`, mode `600`).

---

## 5. Test it

1. Leave **Auto** selected.  
2. Type: `Hi` → press **GO** → expect a Local / inexpensive route when Local is healthy.  
3. Type: `Write a small Python calculator` → expect a coding-capable provider when one is connected.  
4. Status lights: **green** = connected; **Routed** = used on the last job.

### Example ready state

```
KeepRoute UI: http://127.0.0.1:20129/
OmniRoute: Connected
Local AI: Connected
Claude: Connected
Codex: Connected
Cursor: Not configured
Grok: Not configured

Routing: Ready
```

---

## 6. Optional — Mission Controller (full KeepRoute 1.0 orchestration)

The field UI works with OmniRoute alone.

For **persistent missions, checkpoints, and the full KeepRoute 1.0 control plane**, also run the Mission Controller sidecar from the KeepRoute 1.0 release package when you have it, then:

```bash
export MISSION_CONTROLLER_URL=http://127.0.0.1:20130
# restart KeepRoute UI so it picks up the env
```

Auto policy is owned by Mission Controller when that service is present.

> The public UI package in this repo gets you the operator surface + OmniRoute. Full Mission Controller packaging for every self-hoster is still being polished; the Keep reference deployment runs the complete 1.0 control plane.

---

## 7. Day-to-day use

- Prefer **Auto** unless you intentionally force a provider  
- Type normal language requests  
- For coding missions, connect at least one coding-capable provider (Claude / Codex / Cursor as available)  
- Watch the short for the product story: https://youtube.com/shorts/Icc8LA7KIzA  

### Stop

```bash
# In the KeepRoute UI terminal:
Ctrl+C

# Stop OmniRoute too:
cd /path/to/KeepRoute/KeepRoute-UI
docker compose -f docker-compose.omniroute.yml down
```

---

## Ports cheat sheet

| Service | Default on your machine |
|---|---|
| OmniRoute (this compose) | `http://127.0.0.1:20127` |
| KeepRoute UI | `http://127.0.0.1:20129` |
| Mission Controller (optional) | `http://127.0.0.1:20130` |

Always bind to **127.0.0.1** unless you know why you are exposing something on the LAN.

---

## If something fails

| Symptom | Fix |
|---|---|
| Page won’t open | Confirm `python3 desk/app.py` is still running and you used `http://127.0.0.1:20129` |
| Providers missing | Connect them in OmniRoute first, save the OmniRoute API key in KeepRoute, refresh |
| “Connection refused” to OmniRoute | `docker ps \| grep omniroute` — if empty, re-run compose `up -d`. Confirm `OMNIROUTE_HOST=http://127.0.0.1:20127` |
| Docker permission errors | Install Docker Engine, add your user to the `docker` group, log out/in, retry |
| Local AI silent | No GPU / no Ollama is fine — use cloud only; Local is optional |
| Wrong OmniRoute port | Desk default in code is `20128`; **our compose uses host `20127`** — always export `OMNIROUTE_HOST` as shown above |
| Password / login issues on OmniRoute | Reset via upstream OmniRoute docs; set a strong `OMNIROUTE_INITIAL_PASSWORD` before first boot |

Still stuck? [Discord](https://discord.gg/cZDeqECzX) · site FAQ: https://otaconskeep-site.otaconskeep.workers.dev/keeproute/#faq

---

## Security notes (read once)

- Keys and credentials stay **server-side on your machine**  
- Do not commit `desk/state/credentials.json` (keep it `{}` or gitignored locally with secrets)  
- Do not publish OmniRoute or KeepRoute to `0.0.0.0` on an untrusted network without knowing the risk  
- Cloud providers receive only what you send for a mission  

---

## Next

- Product story + UI screenshots: https://otaconskeep-site.otaconskeep.workers.dev/keeproute/  
- Why we built it: [WHY.md](WHY.md)  
- Main write-up: [README.md](README.md)

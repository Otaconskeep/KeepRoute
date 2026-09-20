KeepRoute UI package (OtaconsKeep)
==================================

This folder is the KeepRoute field UI (the operator desk).
Same product surface tested on OtaconsKeep hardware, branded KeepRoute for public use.

Default URL after start:  http://127.0.0.1:20129/

FULL HOW-TO (start here):
  ../INSTALL.md
  https://otaconskeep-site.otaconskeep.workers.dev/keeproute/#install

Quick start
-----------
1. Start OmniRoute:
     export OMNIROUTE_INITIAL_PASSWORD='your-strong-password'
     docker compose -f docker-compose.omniroute.yml up -d
   OmniRoute on this compose file: http://127.0.0.1:20127

2. Start KeepRoute UI:
     python3 -m pip install --user flask
     mkdir -p desk/state && echo '{}' > desk/state/credentials.json && chmod 600 desk/state/credentials.json
     export OMNIROUTE_HOST=http://127.0.0.1:20127
     export OMNIROUTE_DESK_PORT=20129
     export OMNIROUTE_DESK_BIND=127.0.0.1
     python3 desk/app.py
   Open: http://127.0.0.1:20129/

3. ADD PROVIDERS → paste OmniRoute API key (and others) → SAVE KEYS → leave Auto on → GO

Requirements:
  - Python 3.10+
  - Flask (`pip install flask`)
  - Docker (for OmniRoute)
  - Optional: Mission Controller for full Auto mission policy (KeepRoute 1.0 orchestration)

Never commit real API keys. Paste keys only inside the KeepRoute UI (ADD PROVIDERS).

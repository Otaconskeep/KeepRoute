# Why KeepRoute exists

**OmniRoute routes a request. KeepRoute owns the mission.**

## The problem

A coding session is halfway done. Claude hits quota. Or Cursor stalls. Or the host reboots.

Stock routing can send the *next* prompt to another model. The human still has to rebuild context by hand: what finished, what failed, what files mattered, what not to replay.

That is the failure mode KeepRoute was built to close.

## Why not “just use OmniRoute”?

You should — KeepRoute does.

- **OmniRoute** is the traffic director (providers, models, fallback, health).
- **KeepRoute** is mission control (mission ID, checkpoints, handoff, recovery, local-first policy).

Without KeepRoute you still have excellent fallback between models.  
With KeepRoute the **job itself** has memory and a recovery path.

## What we refused to accept

- Trivial “hi” burning paid tokens
- Every client inventing its own routing rules
- Starting over after a recoverable failure
- Replaying a destructive step because the new agent did not know it already ran

## The design bet

**The mission is the product. The model is replaceable.**

Claude, Codex, Cursor, Grok, and Local are executors — not the owner of your work.

See the public page for the full comparison and release notes:  
https://otaconskeep-site.otaconskeep.workers.dev/keeproute/#why

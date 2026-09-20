# Why KeepRoute exists

**OmniRoute routes a request. KeepRoute owns the mission.**

## The short version

| | OmniRoute | KeepRoute |
|---|---|---|
| Solves | Traffic — which model gets this prompt | Unfinished work — keep the *job* alive |
| When a model dies | Can route the *next* request elsewhere | Can checkpoint and continue the *same* mission |
| You should use | Yes | Yes, on top of OmniRoute |

## The problem we kept hitting

A coding session is halfway done.

- Claude hits quota  
- Or Cursor stalls  
- Or the host reboots  
- Or a provider returns a recoverable failure  

Stock routing can send the *next* prompt to another model. The human still has to rebuild context by hand:

- what finished  
- what failed  
- what files mattered  
- what not to replay  

That is the failure mode KeepRoute was built to close.

## Why not “just use OmniRoute”?

You should — **KeepRoute does**.

- **OmniRoute** is the traffic director (providers, models, fallback, health, combos).  
- **KeepRoute** is mission control (Mission ID, policy, checkpoints, handoff, recovery, local-first trivial routing).  

Without KeepRoute you still have excellent fallback between models.  
With KeepRoute the **job itself** has memory and a recovery path.

We did **not** invent OmniRoute. It is the excellent upstream routing gateway. KeepRoute is OtaconsKeep’s layer on top — built because long agent work kept dying when a single provider failed.

## What we refused to accept

1. Trivial “hi” burning paid tokens  
2. Every client inventing its own routing rules  
3. Starting over after a recoverable failure  
4. Replaying a destructive step because the new agent did not know it already ran  

## The design bet

**The mission is the product. The model is replaceable.**

Claude, Codex, Cursor, Grok, and Local are **executors** — not the owner of your work.

```
You → KeepRoute (mission) → OmniRoute (route) → provider/agent
                ↑________________checkpoint / handoff____________|
```

## Concrete examples

### Example A — limit mid-job

1. You ask KeepRoute (Auto) to implement a feature across several files.  
2. Claude makes progress, then hits a usage limit.  
3. **Without KeepRoute:** you copy scraps of chat into another tool and hope nothing was missed.  
4. **With KeepRoute:** mission state (completed work, remaining TODOs, errors, checkpoints) can continue on another supported agent path when configured.

### Example B — host reboot

1. A long mission is running.  
2. The machine reboots.  
3. **With KeepRoute 1.0 orchestration:** supported missions can recover from checkpoints (verified in release testing, including host-reboot cases).  

### Example C — trivial vs paid

1. You type `Hi`.  
2. Policy can keep that on **Local** when Local is healthy — so you are not paying a frontier model for a handshake.  

## What KeepRoute is not

- Not a claim that stock OmniRoute is bad  
- Not “our OmniRoute fork”  
- Not a guarantee every mission completes  
- Not a substitute for backups or judgment on destructive commands  

## Read next

- Install how-to: [INSTALL.md](INSTALL.md)  
- Full product write-up: [README.md](README.md)  
- Live page: https://otaconskeep.github.io/keeproute/#why  
- Short: https://youtube.com/shorts/Icc8LA7KIzA

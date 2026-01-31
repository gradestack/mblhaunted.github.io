# I Built a Thing That Makes Claude Code Actually Work on Big Tasks

So here's the problem: Claude Code is really good at single tasks. Need a function written? Done. Bug fix? Easy. But try something bigger, like "build user authentication" or "add an entire API layer", and you hit this wall. You're either manually breaking everything down into tiny pieces or watching Claude try to do everything one at a time.

I got tired of it. So I built **waverunner**.

## What It Actually Does

You give waverunner a goal. Something like "Add JWT authentication to my app." Instead of Claude just diving in, waverunner spins up a planning session with different personas. Tech Lead, Senior Dev, Explorer, Skeptic, and this Maverick character who questions everything.

They hash it out. The Explorer points out what's unknown. The Skeptic asks "wait, do we already have auth code we should reuse?" The Maverick challenges estimates. Eventually they come up with a plan with tasks and dependencies.

Then here's the cool part: **it runs everything in parallel that can run in parallel.**

Say you need to set up a database, auth system, and logging. Those don't depend on each other, so waverunner fires them all off at once. Once they're done, it moves to the next "wave" of tasks. Stuff that can run together, runs together.

```
Wave 1: [setup-db] [setup-auth] [setup-logging]  ← all at once
Wave 2: [implement-users]                         ← waits for auth + db
Wave 3: [implement-api] [implement-tests]         ← both run together
Wave 4: [integration-tests]                       ← final wave
```

You get this live dashboard showing progress bars for every running task, cost estimates, tokens per second, the whole deal. It's pretty satisfying to watch.

## The Self-Healing Stuff

After all tasks run, there's a cleanup pass that removes debug prints, test files, unused imports. All the junk you'd normally have to sweep up manually. Then Claude evaluates whether the original goal was actually met.

If not? It generates a follow-up sprint and tries again. And again. Until it works or you kill it.

There's also this "Reaper" system that monitors tasks. If something hangs or goes silent for too long, it kills the task but saves what went wrong. When the task gets retried, the new agent sees the corpse of the previous attempt. "Attempt #1: Got stuck on token validation. Attempt #2: JWT library had dependency issues." So it doesn't repeat the same mistakes.

It even detects when you're stuck in a loop. Like if the same task gets killed three times, or you're on iteration 5 with barely any progress. When that happens, it forces the planning team to try something completely different.

## When You'd Actually Use This

Any time you're about to break something down manually for Claude.

- "Add user authentication"
- "Build a REST API for this data model"
- "Refactor this auth system"
- "Fix these five bug reports"

Instead of feeding Claude one piece at a time, you just:

```bash
waverunner go "Add user authentication with JWT"
```

And it handles the rest. The team plans it, breaks it into waves, executes in parallel, cleans up, evaluates, and retries if needed.

You can also inject MCP servers if you need database access or external APIs. There's a Kanban mode if you're doing maintenance work instead of feature development. You can set confirmation flags, timeouts, context. There's a bunch of knobs if you need them.

## The Spike Thing

One feature I really like: if the planning team encounters something unknown, they create "spike" tasks. Investigation missions basically. A spike runs first, figures out what's going on, and its findings automatically flow to dependent tasks. So you're not implementing blind.

Spikes run in an isolated workspace so you don't clutter your project with random test files.

## Installation

Pretty standard:

```bash
git clone <your-repo-url>
cd waverunner
python3 -m venv .venv
source .venv/bin/activate
pip install -e .
```

You need Python 3.10+ and the Claude Code CLI already installed.

## Does It Actually Work?

Yeah. I mean, it's not magic. If your goal is impossible or your codebase is a disaster, waverunner can't fix that. But for mid-sized tasks that are too big for a single prompt and too tedious to micromanage? It's night and day.

The parallel execution alone saves a ton of time. And the retry logic means you're not left with half-finished work when something goes wrong.

Anyway, that's waverunner. If you're using Claude Code for anything beyond one-off functions, give it a shot.

---

**Source:** [github.com/yourusername/waverunner](https://github.com)
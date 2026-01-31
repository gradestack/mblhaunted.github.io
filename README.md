# You Probably Shouldn't Use This But I'm Running It In Production

So Claude Code is great at single tasks. Need a function? Done. Bug fix? Easy. But the second you ask it to do something bigger like "build user authentication" or "add an API layer", it just... doesn't work well. You end up either babysitting it through every little step or it tries to do everything one at a time and takes forever.

Got annoying pretty fast. So I made **waverunner**.

## What It Does

You give it a goal like "Add JWT authentication to my app." Instead of Claude just going for it, waverunner does this whole planning thing with different personas. There's a Tech Lead, Senior Dev, Explorer, Skeptic, and a Maverick who basically argues with everyone.

They argue it out. Explorer's like "we don't know what auth is already there." Skeptic goes "yeah what if there's existing code?" Maverick challenges their time estimates. Eventually they figure out a plan with all the tasks and what depends on what.

Then the good part: **it runs everything that can run at the same time, at the same time.**

Like if you need database setup, auth system, and logging, those don't need each other. So waverunner just starts all three. When they finish, it moves to the next batch of tasks. Everything that can be parallel, is parallel.

```
Wave 1: [setup-db] [setup-auth] [setup-logging]  ← all at once
Wave 2: [implement-users]                         ← waits for auth + db
Wave 3: [implement-api] [implement-tests]         ← both at once
Wave 4: [integration-tests]                       ← final one
```

There's a live dashboard with progress bars for each task, cost tracking, tokens per second. It's honestly fun to watch.

## The Self-Healing Part

After everything runs, it does a cleanup pass. Removes your debug prints, test files, unused imports, all that stuff you'd have to clean up yourself. Then Claude looks at what got done and decides if the original goal was met.

If not? Makes a new plan and tries again. Keeps going until it works or you stop it.

## The Reaper

This is honestly one of the cooler parts. There's a monitoring system called the Reaper that watches all running tasks. If something hangs or goes silent for too long, it kills the task. But here's the thing - it doesn't just kill it and forget about it.

It saves the "corpse." What the task was doing, how long it ran, what it was stuck on, any partial work it did. Everything.

When that task gets retried (and it will), the new agent sees all of this. "Attempt #1: Killed after 195s, was silent for 180s, got stuck on token validation. Attempt #2: Killed after 215s, tried JWT library but hit dependency issues."

So the new agent knows exactly what not to do. Won't try the same approach twice. Actually learns from failures.

It also catches thrashing. Same task dies 3 times? Or you're on iteration 5 and barely anything's done? The system detects this and forces the planning team to try something completely different. No more banging your head against the same wall.

## When You'd Use This

Basically anytime you'd normally break something down for Claude yourself.

- "Add user authentication"
- "Build a REST API for this data model"
- "Refactor this auth system"
- "Fix these bug reports"

Instead of walking Claude through it piece by piece:

```bash
waverunner go "Add user authentication with JWT"
```

That's it. Plans it, runs it in parallel waves, cleans up after itself, checks if it worked, retries if needed.

## The Persona Thing Actually Matters

So those personas aren't just for show. Each one has a real job and they actually catch different problems.

The **Explorer** is there to flag unknowns before you waste time implementing the wrong thing. "Do we even know what the current auth looks like?" That kind of stuff.

**Skeptic** questions assumptions. "What if there's already auth middleware we should use instead of writing new code?" Saves you from reinventing wheels.

**Maverick** is honestly the most useful one. Challenges everything. "You estimated this as small but how do you know? What if the integration is way more complex?" Forces the team to think harder about their plan.

**Tech Lead** keeps it moving and breaks stuff down. **Senior Dev** actually estimates complexity and pushes for simple solutions.

There's also a Kanban mode with different personas (Flow Master, Kaizen Voice, Quality Gate) based on Toyota Production System stuff. Better for maintenance work than feature development.

Point is, they're not roleplay. Each one catches different failure modes before you hit them.

## Swappable Models and MCP

You can use different LLM providers. There's a provider abstraction so you're not locked into just Claude. Extend the `LLMProvider` class and you can plug in whatever model you want.

MCP integration is solid too. Pass `--mcp` with a config file and every agent gets access to those tools. Need to query a database? Connect to an API? Read from some external service? Just inject the MCP server and all your agents can use it.

```bash
waverunner go "Analyze this week's user signups" --mcp ~/database-mcp.json
```

The MCP config gets passed to every Claude instance that spins up. So your Explorer can investigate the database, your implementation tasks can query it, whatever. It just works.

## Spike Tasks

This is a cool feature. If the team doesn't know something, they make "spike" tasks. Basically just investigation work. The spike runs first, figures things out, and the findings get passed to whatever tasks need them. So you're not building blind.

Spikes run in their own workspace so you don't end up with test junk all over your actual project.

## Setup

Nothing special:

```bash
git clone <your-repo-url>
cd waverunner
python3 -m venv .venv
source .venv/bin/activate
pip install -e .
```

Need Python 3.10+ and Claude Code CLI installed.

## Does It Work?

Yeah it does. I mean it's not gonna fix an impossible task or a completely broken codebase. But for stuff that's too big for one prompt and too annoying to micromanage? Big difference.

Just the parallel execution saves so much time. And the retry stuff means you don't get stuck with half-done work when something breaks.

That's waverunner. If you use Claude Code for more than quick one-off stuff, try it out.
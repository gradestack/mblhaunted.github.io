# I Built a Thing That Makes Claude Code Actually Work on Big Tasks

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

There's this "Reaper" thing too. If a task hangs or stops producing output, it kills it but saves what went wrong. Next time that task runs, the agent can see what failed before. "Attempt #1: Got stuck on token validation. Attempt #2: JWT library dependency issues." So it won't make the same mistake twice.

It'll even catch when you're in a loop. Same task dies 3 times? Or you're on try #5 and barely anything's done? It forces the team to try something totally different instead of banging their head against the same wall.

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

There's also MCP server support if you need database access or whatever. Kanban mode for maintenance work. Bunch of flags for timeouts and confirmation and stuff.

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

---

**Source:** [github.com/yourusername/waverunner](https://github.com)
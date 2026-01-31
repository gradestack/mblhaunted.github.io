# you probably shouldn't use this but i'm running it in production

claude code fails at complex tasks. you either manually decompose everything or watch it execute sequentially. both waste time.

waverunner is a protocol for parallel task orchestration.

## protocol

autonomous agents plan work through multi-agent consensus. each agent represents a perspective: tech lead, senior dev, explorer, skeptic, maverick.

they debate. explorer identifies unknowns. skeptic challenges assumptions. maverick forces falsification. the system reaches consensus on task decomposition and dependency graph.

execution is parallel by default. tasks with satisfied dependencies run simultaneously. work progresses in waves.

```
wave 1: [setup-db] [setup-auth] [setup-logging]  # parallel
wave 2: [implement-users]                         # blocked by wave 1
wave 3: [implement-api] [implement-tests]         # parallel
wave 4: [integration-tests]                       # blocked by wave 3
```

## self-correction

after execution, the system evaluates goal achievement. if incomplete, it generates a follow-up sprint. continues until success or manual termination.

failed tasks preserve failure context. next agent sees previous attempts, avoids repeated mistakes.

the reaper monitors task execution. kills hung processes. saves the corpse: runtime, failure mode, partial work. resurrection includes full failure history.

thrashing detection identifies stuck patterns. forces strategy change when same approach fails repeatedly.

## autonomy

each persona is an independent agent. no central coordinator. coordination emerges from debate.

like consensus protocols in distributed systems, correctness emerges from disagreement.

explorer prevents blind implementation. skeptic prevents redundant work. maverick prevents false confidence. senior dev prevents overengineering.

## extensibility

provider abstraction. swap llm backends through `llmprovider` interface.

mcp integration. inject external tools. agents query databases, apis, services.

```bash
waverunner go "analyze signups" --mcp ~/db.json
```

every agent instance receives mcp config. distributed tool access.

## investigation protocol

spike tasks explore unknowns before implementation. findings propagate to dependent tasks. isolated workspace prevents project pollution.

## usage

```bash
git clone <repo>
cd waverunner
python3 -m venv .venv
source .venv/bin/activate
pip install -e .
```

requires python 3.10+ and claude code cli.

```bash
waverunner go "add jwt auth"
```

## limitations

not magic. impossible tasks remain impossible. broken codebases remain broken.

effective for mid-complexity tasks. too large for single prompt, too tedious for manual decomposition.

parallel execution reduces latency. retry logic ensures completion.

that's the protocol.

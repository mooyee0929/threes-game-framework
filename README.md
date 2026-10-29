# Threes! Game Framework

A C++11 framework for playing and experimenting with Threes!, a sliding-tile puzzle in
the same family as 2048. The framework runs episodes of the game between a *slider* agent
(the player, which chooses a direction) and a *placer* agent (the environment, which spawns
tiles), records each episode, and aggregates win-rate and score statistics over many runs.
It is structured so that new agents can be dropped in without touching the game engine,
and it includes the extension points needed to build and train a weight-table
(n-tuple-network) learning agent.

## Components

| File | Role |
|------|------|
| `threes.cpp` | Entry point: parses options, builds agents and runs the episode loop |
| `board.h` | The 4x4 board, tile values, slide moves and reward calculation |
| `action.h` | Slide and place actions, with apply/undo and serialization |
| `agent.h` | Agent base classes and the baseline `random_slider` / `random_placer` |
| `weight.h` | Weight-table storage used by n-tuple-network agents |
| `episode.h` | Records the moves and metadata of a single game |
| `statistics.h` | Accumulates and reports results across episodes |

`agent.h` ships two working baseline agents (a random legal-move slider and a random tile
placer) plus `random_agent` and `weight_agent` base classes. `weight_agent` already
handles initializing, loading and saving weight tables and reading a learning rate, so it
serves as the starting point for a learning player.

## Requirements

- A C++11 compiler (`g++`).
- No external dependencies.

## Build and run

Build the program:

```bash
make
```

Run with the defaults (1000 games):

```bash
./threes
```

Run a specific number of games and report statistics periodically:

```bash
./threes --total=100000 --block=1000 --limit=1000
```

Save or reload the statistics of a run:

```bash
./threes --total=1000 --save=stats.txt
./threes --load=stats.txt
```

Seed the environment:

```bash
./threes --total=100000 --place="seed=12345"
```

The `makefile` also provides `make stats` (runs 1000 games and writes `stats.txt`) and
`make clean`.

### Weight-table agent options

A slider derived from `weight_agent` reads its configuration from the `--slide` argument,
for example initializing an 8x4-tuple network and saving it:

```bash
weights_size="65536,65536,65536,65536,65536,65536,65536,65536"
./threes --total=100000 --block=1000 --limit=1000 \
  --slide="init=$weights_size save=weights.bin alpha=0.0025"
```

and loading an existing network for evaluation:

```bash
./threes --total=1000 --slide="load=weights.bin alpha=0" --save=stats.txt
```

## Notes

- This framework is a starter framework, modified from the
  [2048-Framework](https://github.com/moporgic/2048-Framework). It provides the game
  engine, agent interfaces and baseline agents; building a trained learning player on top
  of the `weight_agent` base is left to the user.
- `starter/README.md` is the original documentation that shipped with the upstream
  starter framework, kept for reference.

# Source

The software that [`../specification/`](../specification/README.md) describes. There is no code here yet.

New to the repository? Start with [`../docs/getting-started.md`](../docs/getting-started.md).

## How code arrives

Code is added only by executing a story from an epic that a human has moved to `ready`
(see [`../specification/epics/README.md`](../specification/epics/README.md#executing-a-story)). Each story
lists the files it created or changed under **Files Modified**, and is committed together with them. That
list is the trace from a requirement to the code that satisfies it.

## Layout

Top-level directories under `src/` are the solution's modules. Each one corresponds to a `domain:` tag in the
table in [`../specification/requirements/README.md`](../specification/requirements/README.md#domain-tags-prefixed-domain),
so a requirement's tags point at a place in this tree. When a story introduces a new top-level module, the
same change adds its row to that table.

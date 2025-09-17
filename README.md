# Devspec Analyzer

The objective of this tool is to parse a [Devspec](https://devspec.io/) and extract its execution lifecycle.

The initial version focuses on generating a visual representation of the Devspec lifecycle graph.

This is an evolving implementation with partial Devspec feature coverage. Refer to examples below to verify supported capabilities.

For instance, given a Devspec [container-build-run.yaml](./tests/devspecs/container-build-run.yaml) declaring a single component and two commands, *build* and *run*, the lifecycle is visualized as:

```mermaid
graph TB
my-container["container: my-container<br/>image: my-image"]
my-build["command: my-build"]
my-container-my-run-expose["Expose ports<br/>http: 8080"]
my-container-stop["Stop container<br/>container: my-container"]
my-run["command: my-run"]
start["start"]
sync-all-my-container["Sync All Sources"]
sync-modified-my-container["Sync Modified Sources"]
start -->|"dev"| my-container
my-container -->|"container running"| sync-all-my-container
sync-all-my-container -->|"sources synced"| my-build
my-build -->|"build done, with run"| my-run
my-run -->|"command running"| my-container-my-run-expose
my-container-my-run-expose -->|"User quits"| my-container-stop
my-container-my-run-expose -->|"source changed"| sync-modified-my-container
sync-modified-my-container -->|"source synced"| my-build
my-container-my-run-expose -->|"devspec changed"| my-container
```

The tool outputs Mermaid-standard text format. This output integrates with Mermaid-compatible tools (GitHub pages, IDEs, inline visualizers) for graphical rendering.

## Usage

```bash
# From a local file
$ go run main.go path/to/my/devspec.yaml > devspec.md

# From a Devspec Registry (include trailing /)
$ go run main.go https://registry.devspec.io/devspecs/nodejs-basic/ > nodejs-basic.md
```

## Examples

Examples are available in the [tests/graphs directory](./tests/graphs), with corresponding Devspec sources in [tests/devspecs directory](./tests/devspecs).


## Next steps

Future iterations will develop a library (initially in Go) providing a state-machine implementation based on the Devspec, usable by Devspec-compatible tools.

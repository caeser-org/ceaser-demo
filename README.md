# Caeser demo fixture — gin `TestLateRouteRegistrationSkippedNodes`

> **This repository is not the Gin framework and is not accepting
> contributions.** It is an isolated, intentionally frozen reproduction fixture
> for the [Caeser](https://github.com/caeser-org/caeser) product demo.

Caeser is a Go CI-investigation engine. It reads a failed CI run, traces the
failure through the checked-out source at the exact failing revision, and
publishes its evidence-backed finding as a pull-request comment. This repository
provides one real, deterministic failure for that workflow to investigate
end to end.

## What this repository is

- **Source.** Imported verbatim from
  [`gin-gonic/gin`](https://github.com/gin-gonic/gin) — module
  `github.com/gin-gonic/gin`, version `v1.12.0` — at the **pre-fix revision** for
  the "skipped nodes" routing regression that Caeser's evaluation tracks as case
  `go-002-gin-skipped-nodes`. The routing tree in `tree.go` is the code under
  investigation; the rest is the unmodified upstream checkout (initial import
  commit `09ee4b35`).
- **The failure being reproduced.** `routes_test.go` adds
  `TestLateRouteRegistrationSkippedNodes`, which panics with
  `slice bounds out of range [:2] with capacity 1` inside `tree.go`
  `node.getValue` when a route is registered late after nodes have been skipped.
  CI (`.github/workflows/ci.yml`) runs
  `go test -run TestLateRouteRegistrationSkippedNodes .` and fails.
- **The demonstration.** [Pull request #1](https://github.com/caeser-org/ceaser-demo/pull/1)
  carries the regression test. When its CI fails, the Caeser workflow
  (`.github/workflows/ceaser.yml`) investigates the failure and posts the result
  as a PR comment authored by the repository's GitHub App.
  - Example investigation:
    [workflow run](https://github.com/caeser-org/ceaser-demo/actions/runs/45663802681)
    (artifact `caeser-diagnosis-*` contains `caeser-report.json` and the
    Markdown brief).
  - Caeser localizes the panic to the observed frame `tree.go:418`
    `node.getValue` and ranks it against `gin.go` `handleHTTPRequest` /
    `ServeHTTP`, as an investigation lead rather than a verified root cause.

## Reproduce locally

```sh
go test -run TestLateRouteRegistrationSkippedNodes .
```

`.caeser.yml` records the same command so Caeser reproduces the failure the way
CI does.

## Source, attribution and license

This checkout is derived from [gin-gonic/gin](https://github.com/gin-gonic/gin)
and is redistributed under the same [MIT license](LICENSE) — Copyright (c)
2014-present Manuel Martínez-Almeida and the Gin contributors. The license text
and every upstream copyright header are retained. All credit for the framework
source belongs to the Gin project; only the fixture and its CI wiring are part
of the Caeser demonstration.

## Please do not use this as a project

- Do not open issues or pull requests here.
- Do not merge changes into `main`; the repository is frozen at one revision so
  the demonstration stays reproducible.
- For Gin itself, use the upstream repository above. For Caeser, use
  [caeser-org/caeser](https://github.com/caeser-org/caeser).

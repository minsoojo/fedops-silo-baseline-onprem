# FedOps Federated Task Baseline

This repository develops and verifies the starter used to create a FedOps Federated
Task. Runtime products do not clone this repository. A verified release is vendored
into the FedOps Web backend, and Agent Studio receives it through an authenticated
FedOps Web API.

## Repository boundary

```text
federated-task-baseline/  exact user Workspace starter
tests/                    Baseline maintainer tests; not published
tools/                    release exporter; not published
```

The current starter is a functionally organized implementation-contract template rather than an MNIST example.
It supports:

- fixed, documented user hooks for model, data, training, readiness probes, and Tool AI
- local model training and Initial Model export after those hooks are implemented
- live Local Train percentage, epoch/batch position, loss, and evaluation metrics in Agent Studio
- FedOps federated participation using the same training implementation
- Release Readiness for Owner publication
- Participation Readiness for participant data and parameter-update preflight
- Agent Builder Tool inference with an Initial or Global Model
- one optional `load_inference_sample(data_root, index)` data adapter, reached through
  the fixed Tool wrapper, so Agent Builder can use a selected local-only Task Data
  file or directory for inference without uploading data

Owner-editable code is grouped under `local_training/`, `tool_ai/`, and `conf/`.
FedOps-managed integration is grouped under `federated_learning/`, `task_readiness/`,
and `runtime/`. The same model definition is shared across local training, federation,
and Tool AI.

Runnable domain examples are kept separately in
`../FedOps-AgentStudio-TestRunCases/`; they are not shipped as the default Baseline.

## Verify

On 2026-09-16, uv 0.8.13 installed 126 packages on Windows/Python 3.12.10.
All 14 existing Baseline tests, installed Git revision verification, CPU TorchVision
NMS, frozen sync, and dependency checks passed. Other locked package versions were
preserved. See [verification evidence](verification/task-dependency.json).
Linux/F execution and Web/Manager integration have not been verified by these checks.

### Optional server evaluation

On-prem Baseline 0.19.1 uses FedOps 1.1.30.19+onprem.20260916 from
`minsoojo/fedops-core-onprem` at immutable commit
`ff5f44ddea2705c8d901a54a0272f517822da8f4`. Its lock and Web/Server profile must
match this revision. Web/Manager acceptance and F Git authentication remain a
separate integration step; this source change does not update running Tasks.
Existing published Releases retain their original pins; never export this
template over Baseline 0.19.0 or an older release.

New config defaults to `server_evaluation.enabled: false` (client evaluation
aggregation). Enable it only with real server validation data. Smoke loaders are
for code checks, never a substitute for global model quality evaluation.

```bash
cd federated-task-baseline
uv tool run --from uv==0.8.13 uv sync --locked --link-mode copy
cd ..
federated-task-baseline/.venv/bin/python -m unittest discover -s tests
federated-task-baseline/.venv/bin/python tools/build_release.py
```

## Release policy

- Current on-prem candidate: `federated-task-baseline@0.19.1`
- Existing releases remain available through Git history and existing Web/S3 tasks.
- A release is immutable. Changes require a new version.
- Raw datasets, `.venv`, local artifacts, credentials, and readiness run details are
  excluded from the distributed starter.
- Python build output (`build/`, `dist/`) and hidden/cache files are never included in
  a Baseline manifest, even when a maintainer builds a wheel before exporting it.

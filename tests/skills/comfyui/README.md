# ComfyUI Skill Tests

Pytest suite covering the skill's scripts. Pure-stdlib unit tests run
without any setup; cloud integration tests need a Comfy Cloud API key.

## Running

Run from the repository root through the canonical test runner:

```bash
# Run this suite with the hermetic repository environment
scripts/run_tests.sh tests/skills/comfyui/ -q

# Explicitly select this suite's pytest configuration
scripts/run_tests.sh tests/skills/comfyui/ \
  -c tests/skills/comfyui/pytest.ini -q

# Run one test file
scripts/run_tests.sh tests/skills/comfyui/test_run_workflow.py -q
```

The runner clears credentials, so the cloud integration tests remain skipped
during normal repository validation. The test fixtures read the production
scripts and workflows from `skills/creative/comfyui/`.

## Test files

| File | Coverage |
|------|----------|
| `test_common.py` | Cloud detection, URL routing, format validation, embeddings, paths, seeds, model-list parsing, folder aliases |
| `test_extract_schema.py` | Connection tracing, positive/negative prompt detection, dedup logic, embedding deps |
| `test_run_workflow.py` | Param injection (incl. -1 seed, link refusal), output download walk, runner construction |
| `test_check_deps.py` | Model-name fuzzy matching, install command suggestions |
| `test_cloud_integration.py` | Live cloud API contract tests (auto-skipped without API key) |

## Adding tests

When you change a script:

1. Add a unit test if the change is pure logic (cloud detection, parsing, etc.)
2. Add a cloud integration test if the change depends on cloud API behavior
   (use `pytestmark = pytest.mark.cloud` so it auto-skips without a key)
3. Workflow fixtures live in `conftest.py` (`sd15_workflow`, `flux_workflow`,
   `video_workflow`)

## Test configuration

The local `pytest.ini` registers the `cloud` marker and disables xdist.
`scripts/run_tests.sh` supplies the hermetic environment and per-file subprocess
isolation through `scripts/run_tests_parallel.py`. The runner discovers the local
pytest configuration from the test path; pass `-c tests/skills/comfyui/pytest.ini`
to make that selection explicit.

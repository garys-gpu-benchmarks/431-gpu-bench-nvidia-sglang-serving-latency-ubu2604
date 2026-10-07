# SPEC.md: "The Human How"; Exact technical requirements, environment setup, implementation details, etc.

## Execution Description

Locked run_benchmark.sh starts tiny_sglang_server.py (smoke) or sglang.launch_server (baseline/extended) on port 30000, then scripts/bench_serving.py with a concurrent ThreadPoolExecutor. model Mistral-7B-v0.3, yaml dtype, input_len, output_len, num_prompts, and max_concurrency (smoke 2, baseline 8) come from yaml. Honors the full num_prompts count. This is concurrent serving, in contrast to sequential 230 Sweep dimensions: model_name, dtype, tensor_parallel_size, schedule_policy, prompt_source, input_len, output_len, max_total_tokens.

## Parameters

| Parameter | CLI Flag | Tested Values | Default | Description |
| --- | --- | --- | --- | --- |
| model_name | `--model-name` | smoke=mistralai/Mistral-7B-v0.3, baseline=mistralai/Mistral-7B-v0.3, extended=mistralai/Mistral-7B-v0.3 | mistralai/Mistral-7B-v0.3 | From Parameter list; see Execution Description With Parameters. |
| dtype | `--dtype` | smoke=bfloat16, baseline=bfloat16, extended=bfloat16 | bfloat16 | From Parameter list; see Execution Description With Parameters. |
| tensor_parallel_size | `--tensor-parallel-size` | smoke=1, baseline=1, extended=1 | 1 | From Parameter list; see Execution Description With Parameters. |
| schedule_policy | `--schedule-policy` | smoke=lpm, baseline=lpm, extended=lpm | lpm | From Parameter list; see Execution Description With Parameters. |
| prompt_source | `--prompt-source` | smoke=synthetic, baseline=synthetic, extended=synthetic | synthetic | From Parameter list; see Execution Description With Parameters. |
| input_len | `--input-len` | smoke=64, baseline=128, extended=128 | 128 | From Parameter list; see Execution Description With Parameters. |
| output_len | `--output-len` | smoke=16, baseline=3250, extended=7000 | 3250 | From Parameter list; see Execution Description With Parameters. |
| max_total_tokens | `--max-total-tokens` | smoke=2048, baseline=16384, extended=32768 | 16384 | From Parameter list; see Execution Description With Parameters. |
| max_running_requests | `--max-running-requests` | smoke=2, baseline=8, extended=8 | 8 | From Parameter list; see Execution Description With Parameters. |
| max_concurrency | `--max-concurrency` | smoke=2, baseline=8, extended=8 | 8 | From Parameter list; see Execution Description With Parameters. |
| request_rate | `--request-rate` | smoke=inf, baseline=inf, extended=inf | inf | From Parameter list; see Execution Description With Parameters. |
| num_prompts | `--num-prompts` | smoke=2, baseline=24, extended=48 | 24 | From Parameter list; see Execution Description With Parameters. |
| backend | `--backend` | smoke=sglang, baseline=sglang, extended=sglang | sglang | From Parameter list; see Execution Description With Parameters. |
| dataset_name | `--dataset-name` | smoke=synthetic, baseline=synthetic, extended=synthetic | synthetic | From Parameter list; see Execution Description With Parameters. |

## Invocation

```bash
Smoke starts scripts/tiny_sglang_server.py; baseline and extended start python -m sglang.launch_server, then scripts/bench_serving.py
```

## Raw Output Format

raw_results.csv with one req_N row per request and a final summary row. Each request row also carries the run-level p50 metrics

check_name,status,e2e_ms,ttft_ms,tpot_ms,end_to_end_request_latency_p50_msec,ttft_p50_msec,time_per_output_token_tpot_p50_msec,inter_token_latency_itl_p50_msec,request_throughput_requests_sec
req_0,ok,350,30,10,340,28,9,8,6
summary,ok,340,28,9,340,28,9,8,6

## Metrics

- **#1: E2E latency, ms** — stored as `end_to_end_request_latency_p50_msec`.
- **#2: TTFT, ms** — stored as `ttft_p50_msec`.
- **#3: TPOT, ms** — stored as `time_per_output_token_tpot_p50_msec`.
- **#4: ITL, ms** — stored as `inter_token_latency_itl_p50_msec`.
- **#5: Request throughput** — stored as `request_throughput_requests_sec`.

## Framework

Locked run_benchmark.sh starts tiny_sglang_server.py (smoke) or sglang.launch_server (baseline/extended) on port 30000, then scripts/bench_serving.py with a concurrent ThreadPoolExecutor. model Mistral-7B-v0.3, yaml dtype, input_len, output_len, num_prompts, and max_concurrency (smoke 2, baseline 8) come from yaml. Honors the full num_prompts count.

## Installation and Execution Summary

Launch scripts/tiny_sglang_server.py on smoke, or python -m sglang.launch_server with Mistral-7B-v0.3 on baseline and extended, on port 30000. Then run scripts/bench_serving.py with yaml num_prompts and max_concurrency, to measure concurrent serving latency and throughput. This is not prompt_response_client.py

## Platform Portability

- **AMD (primary):** ```bash
Smoke starts scripts/tiny_sglang_server.py; baseline and extended start python -m sglang.launch_server, then scripts/bench_serving.py
```
- **NVIDIA:** Native NVIDIA CUDA workload. Execute on the stated Ubuntu release with the host NVIDIA driver and CUDA userspace. ROCm porting notes do not apply.

## Model Context Protocols

- **Active:** None

## Execution-Loop Validation Contract

EXECUTION CHAIN: `run_benchmark.sh` ➔ raw output ➔ `scripts/parse_results.py` ➔ `results/benchmark.db` ➔ `scripts/validate_results.py`

This benchmark uses a lightweight, SQLite-integrated execution loop for result validation. All validation is performed by `scripts/validate_results.py`.

### Validation script usage

```bash
export BENCHMARK_PYTHON=/usr/bin/python3.13  # optional; select the installed interpreter

# After a live run:
".venv/bin/python" scripts/validate_results.py --db results/benchmark.db

# CI / no-GPU path (seeds fixture and validates it):
".venv/bin/python" scripts/validate_results.py --seed-fixture --quiet

# Override DB path via environment variable:
BENCHMARK_DB=tests/fixtures/benchmark.db \
  ".venv/bin/python" scripts/validate_results.py
```

### Run artifact contract

raw_results.csv with one req_N row per request and a final summary row. Each request row also carries the run-level p50 metrics

check_name,status,e2e_ms,ttft_ms,tpot_ms,end_to_end_request_latency_p50_msec,ttft_p50_msec,time_per_output_token_tpot_p50_msec,inter_token_latency_itl_p50_msec,request_throughput_requests_sec
req_0,ok,350,30,10,340,28,9,8,6
summary,ok,340,28,9,340,28,9,8,6

```bash
bash run_benchmark.sh --help
bash run_benchmark.sh --profile smoke --validate
bash run_benchmark.sh --profile baseline --validate
bash run_benchmark.sh --profile extended --validate
```
`run_benchmark.sh --help` prints usage and exits. The harness calls `scripts/ensure_setup.sh` when `.setup_state` is absent.

### Required integrity checks (built into `validate_results.py`)

1. Latest run exists and `runs.status = 'ok'`.
2. `run.error_message` is NULL.
3. `started_at` and `finished_at` are valid ISO-8601 UTC strings.
4. All required aggregate metrics in `runs` are non-NULL and finite.
5. All required aggregate metrics are physically sensible (positive values). Locked run_benchmark.sh starts tiny_sglang_server.py (smoke) or sglang.launch_server (baseline/extended) on port 30000, then scripts/bench_serving.py with a concurrent ThreadPoolExecutor. model Mistral-7B-v0.3, yaml dtype, input_len, output_len, num_prompts, and max_concurrency (smoke 2, baseline 8) come from yaml. Honors the full num_prompts count.
6. At least 2 sample rows exist for the latest `run_id` (sweep coverage).
7. No sample has `status = 'error'`.
8. Locked run_benchmark.sh starts tiny_sglang_server.py (smoke) or sglang.launch_server (baseline/extended) on port 30000, then scripts/bench_serving.py with a concurrent ThreadPoolExecutor. model Mistral-7B-v0.3, yaml dtype, input_len, output_len, num_prompts, and max_concurrency (smoke 2, baseline 8) come from yaml. Honors the full num_prompts count.

### Baseline / Threshold configuration (`config/benchmark_config.yaml`)

Expected ranges and gates live in `config/benchmark_config.yaml` under `baselines:` or `thresholds:`. To update them, edit that file — never edit validation code directly.

Threshold key suffixes encode comparison direction when `thresholds:` is present: `_min` → observed value must be ≥ threshold. `_max` → observed value must be ≤ threshold. Informational `baselines:` ranges are not pass/fail gates.

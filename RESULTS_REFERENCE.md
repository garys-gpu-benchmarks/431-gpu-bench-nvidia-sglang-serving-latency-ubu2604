# Results: 431 SGLang Serving Latency Benchmark

**Main goal:** Measure SGLang concurrent serving latency percentiles under load  
**Tools:** Every profile starts sglang.launch_server with Mistral-7B-v0.3, then bench_serving.py

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_148 · recorded 2026-11-19). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× NVIDIA H100 NVL (95830 MiB) · Intel(R) Xeon(R) Platinum 8452Y · Ubuntu 26.04.1 LTS · kernel 6.8.0-138-generic · driver 595.112.07
- **Run:** 2026-11-19T16:44:26Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 11:01 · benchmark 00:12 · whole run 12:36
- **Source:** run 431_20261119_164426
- **Command:** `bash run_benchmark.sh --smoke --dtype bfloat16 --input-len 64 --output-len 16 --max-total-tokens 1024 --model-name mistralai/Mistral-7B-v0.3 --max-concurrency 2 --max-running-requests 2 --num-prompts 2`

| # | Metric | Result |
|---|---|---|
| 1 | E2E latency p50, client-observed from send, ms (end_to_end_request_latency_p50_msec) | 311.1675840045791 |
| 2 | TTFT p50, client-observed from send, ms (ttft_p50_msec) | 56.77162099163979 |
| 3 | TPOT, ms (time_per_output_token_tpot_p50_msec) | 16.004706800837692 |
| 4 | ITL, ms (inter_token_latency_itl_p50_msec) | 15.896824013907462 |
| 5 | Request throughput (request_throughput_requests_sec) | 6.339533629891699 |
<!-- /profile:smoke -->

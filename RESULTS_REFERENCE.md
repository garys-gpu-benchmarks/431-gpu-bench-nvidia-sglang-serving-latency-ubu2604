# Results: 431 SGLang Serving Latency Benchmark

**Main goal:** Measure SGLang concurrent serving latency percentiles under load  
**Tools:** Every profile starts sglang.launch_server with Mistral-7B-v0.3, then bench_serving.py

Reference results measured by the maintainer on the system shown in each section (generator TEMPLATE_00_138 · recorded 2026-11-13). Your own runs write `results/RESULTS_ACTUAL.md` in the same format.

> Smoke runs use deliberately small sizes. They check that the workload installs and runs end to end; their numbers are not performance results. Baseline and extended are.

<!-- profile:smoke -->
## smoke

- **System:** 1× NVIDIA H100 80GB HBM3 (81559 MiB) · Intel(R) Xeon(R) Platinum 8470 · Ubuntu 26.04.1 LTS · kernel 6.8.0-147-generic · driver 595.91.07
- **Run:** 2026-11-13T00:11:06Z · status **PASS** (exit 0)
- **Time (mm:ss):** setup 06:23 · benchmark 02:12 · whole run 09:48
- **Source:** run 431_20261113_001106
- **Command:** `bash run_benchmark.sh --smoke --dtype bfloat16 --input-len 64 --output-len 16 --max-total-tokens 1024 --model-name mistralai/Mistral-7B-v0.3 --max-concurrency 2 --max-running-requests 2 --num-prompts 2`

| # | Metric | Result |
|---|---|---|
| 1 | E2E latency p50, client-observed from send, ms (end_to_end_request_latency_p50_msec) | 197.20723503269255 |
| 2 | TTFT p50, client-observed from send, ms (ttft_p50_msec) | 31.2211710261181 |
| 3 | TPOT, ms (time_per_output_token_tpot_p50_msec) | 10.429743599767486 |
| 4 | ITL, ms (inter_token_latency_itl_p50_msec) | 9.57577908411622 |
| 5 | Request throughput (request_throughput_requests_sec) | 10.085430098622385 |
<!-- /profile:smoke -->

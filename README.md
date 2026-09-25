---
base_model: Qwen/Qwen3-4B
tags:
- gguf
- llama.cpp
- ollama
- quantization
- qwen3
- q4_k_m
- q8_0
- text-generation
- 4b
- wikitext-2
license: apache-2.0
pipeline_tag: text-generation
---

# Qwen3-4B Q4_K_M GGUF — verified 4GB edge build

<div align="center">

<img alt="Model" src="https://img.shields.io/badge/model-Qwen3--4B-8A2BE2?style=for-the-badge">
<img alt="Published formats" src="https://img.shields.io/badge/GGUF-Q8_0%20%7C%20Q4_K_M-FFD21E?style=for-the-badge">
<img alt="Parameters" src="https://img.shields.io/badge/params-4B-00A6A6?style=for-the-badge">
<img alt="Quantizer" src="https://img.shields.io/badge/imatrix-calibrated-16A34A?style=for-the-badge">
<img alt="Peak memory" src="https://img.shields.io/badge/Q4%20RAM-~3GB-0069B4?style=for-the-badge">
<img alt="License" src="https://img.shields.io/badge/license-Apache--2.0-7C3AED?style=for-the-badge">

</div>

Base: `Qwen/Qwen3-4B` (Apache-2.0) · Quant: llama.cpp `0.4.1-dev (60081bb)` · imatrix on 3000-line wikitext-2 (seed 42, 618 chunks, 4 threads)

## Format status

| File | Status | Note |
|---|---|---|
| `smol-Q4_K_M.gguf` | Published | Main release, ~2.4 GB |
| `smol-Q8_0.gguf` | Published | ~4.0 GB |
| F16 | Reference only | 8.0 GB source-side reference used for the PPL baseline; not published |

The F16 row in the eval table below is a measurement reference, not a downloadable file.

## Eval (wikitext, 128 held-out lines, 2048 ctx)

| model | PPL | size |
|---|---|---|
| F16 | 11.4970 | 8.0 GB |
| Q8_0 | 11.5480 (+0.44%) | 4.0 GB |
| Q4_K_M | 11.8856 (**+3.38%**) | 2.4 GB |

Gate was `<5%` — passed. Task evals not run: perplexity only, stated honestly. Files: `smol-Q4_K_M.gguf`, `smol-Q8_0.gguf`, `imatrix.dat`, `calib.txt`, `REPRO.json`, `SHA256.txt`. Full repro: see GitHub link below.

## Verified outputs

Verbatim `smol-Q4_K_M.gguf` completions, `--temp 0 -no-cnv`, 24 new tokens, prompt form `Question: ...\nAnswer:`. Qwen3-4B ships a chat template, so without `-no-cnv` llama.cpp switches to conversation mode and ignores `-p`.

| Question | Model answer |
|---|---|
| What is the capital of Japan? | `The capital of Japan is Tokyo. It is the largest city in Japan and serves as the political, economic, and cultural` |
| What is the capital of Italy? | `The capital of Italy is Rome. Rome is a city in the central part of Italy, and it is known for its` |
| What is the capital of Egypt? | `The capital of Egypt is Cairo. It is a city that has been the political, economic, and cultural center of Egypt` |
| What is the largest ocean on Earth? | `The Pacific Ocean is the largest ocean on Earth, covering approximately 63 million square miles (165 million square` |
| Which planet is closest to the Sun? | `Mercury is the planet closest to the Sun. It is the smallest planet in our solar system and has the shortest orbital period` |
| How many days are in a leap year? | `A leap year has 366 days. This is because, in addition to the usual 365 days` |
| How many continents are there? | `There are seven continents: Africa, Antarctica, Asia, Europe, North America, Australia, and South America.` |
| What is the chemical symbol for gold? | `The chemical symbol for gold is Au.` |

Eight of eight are correct. Sentences are cut at the 24-token limit, so the trailing text is incomplete by construction.

## Use

Ollama (see `Modelfile`, non-thinking params per Qwen3 best practices):
```
ollama create qwen3-4b-q4 -f Modelfile
ollama run qwen3-4b-q4 "Explain overfitting in 3 lines."
```

llama.cpp:
```
./llama-cli -m smol-Q4_K_M.gguf -p "Explain overfitting in 3 lines." -c 4096
```

Q4 peaks ~3GB: fits 4GB GPUs, fully offline. For Qwen3 thinking mode use Temperature=0.6, TopP=0.95; for fast non-thinking answers use Temperature=0.7, TopP=0.8 (this Modelfile).

## Limitations

- Perplexity-only validation; verify on your task before production use.
- English wikitext calibration: other languages/domains may vary.
- Qwen3 can repeat itself under greedy decoding — use the sampling params above.

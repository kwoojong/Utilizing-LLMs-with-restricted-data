# Coding Physical Intimate Partner Violence in Suicide Narratives with a Local LLM

This repository documents a transparent procedure for coding suicide case
narratives for evidence of **physical intimate partner violence (IPV)** in the
period before the death, using an **open-weight large language model run
locally**. Each case has two source narratives (referred to as **LE** and
**ME**), and each narrative is coded independently as physical IPV *present* (1)
or *not present* (0) under a deliberately conservative coding standard. Because
the underlying records are restricted, the model (`llama3.2:3b`) is served
locally through [Ollama](https://ollama.com) so the text never leaves the
analysis machine.

## What's here

- **[`ipv_llm_coding_walkthrough.Rmd`](ipv_llm_coding_walkthrough.Rmd)** — a
  step-by-step walkthrough of the full pipeline: the exact prompt, how the local
  model is queried, how its answers are parsed, and how results are assembled.
  It is written for transparency, so the code is shown but not executed.

- The walkthrough renders to a GitHub-friendly page when knit
(`rmarkdown::render("ipv_llm_coding_walkthrough.Rmd")`), or GitHub will display
the `.Rmd` source directly.

## Method at a glance

- **Local, private inference.** Narratives are sent only to a model running on
  the local machine via Ollama — never to an external API.
- **One narrative at a time.** LE and ME reports are coded separately, so a code
  for one report cannot be influenced by the other.
- **Conservative standard.** The model defaults to 0 and codes 1 only when a
  physical act between intimate partners is explicitly described and connected
  to the circumstances of the death.
- **Deterministic and auditable.** Queries use `temperature = 0`, and every
  parsed code is stored alongside the model's raw output for later review.

## Requirements

- R with the packages: `httr`, `jsonlite`, `readxl`, `stringr`, `dplyr`,
  `writexl` (and `rmarkdown` to knit the walkthrough).
- [Ollama](https://ollama.com) running locally with the model pulled:
  `ollama pull llama3.2:3b`.

## Data access and ethics

The narratives are sensitive, restricted-access records and are **not** included
in this repository. Only the procedure is shared here; reproducing any results
requires separately authorized access to the source data.

# Coding Physical Intimate Partner Violence in Suicide Narratives with Local LLMs

This repository documents a transparent procedure for coding suicide case narratives for evidence of **physical intimate partner violence (IPV)** in the period before the death, using an **open-weight large language model run locally**. Each case has two source narratives, **LE** (law enforcement) and **ME** (coroner/medical examiner), and each narrative is coded independently as physical IPV *present* (1) or *not present* (0) under a deliberately conservative coding standard. Because the underlying records are restricted, the model is served locally through [Ollama](https://ollama.com) so the text never leaves the analysis machine.

## What's here

- **[`ipv_llm_coding_walkthrough.Rmd`](ipv_llm_coding_walkthrough.Rmd)**: a  short, readable walkthrough of the pipeline itself: the exact prompt, how the local model is queried, how its answers are parsed, and how results are saved. The code is shown for transparency but not executed.

- The walkthrough is intentionally light on prose so the code is easy to follow. **The background, design rationale, and post-processing live in this README** (below), so the script and the explanation stay cleanly separated.

## Method at a glance

- **Local, private inference.** Narratives are sent only to a model running on the local machine via Ollama, not shared with an external API.
- **One narrative at a time.** LE and ME reports are coded separately, so a code for one report cannot be influenced by the other.
- **Conservative standard.** The model defaults to 0 and codes 1 only when a physical act between intimate partners is explicitly described and connected to the circumstances of the death.
- **Deterministic and auditable.** Queries use `temperature = 0`, and every parsed code is stored alongside the model's raw output for later review.

---

## Study background

### The data and the task

The narratives come from the National Violent Death Reporting System (NVDRS), covering 2003–2019. Every suicide case includes **two** narratives written by different sources: a **law enforcement (LE)** report and a **coroner/medical examiner (ME)** report. Each describes the person's background and the circumstances leading up to the death.

The goal is narrow and specific: decide whether **physical** intimate partner violence appears in the circumstances before the suicide, recorded as a simple yes/no (1 or 0). We focused on *physical* IPV because physical violence usually shows up as concrete, observable actions(someone was hit, choked, and so on), which is easier to define clearly and code consistently than other forms of abuse.

### The four models

The walkthrough shows the pipeline for a single model, but the study ran **four** small, open-weight models from four different developers:

- **Llama 3.2** (3 billion parameters)
- **Gemma 3** (4B)
- **Qwen 2.5** (3B)
- **Phi-3 mini** (3.8B)

Small models (all under 4B parameters) were chosen for practical reasons: 
they run on an ordinary personal laptop with no high-end GPU; and using models from different organizations allows comparison and later combination.
The code is **identical for every model.** Only the model name changes, so the walkthrough shows it once rather than four times.

### Running everything locally

All four models were downloaded with [Ollama](https://ollama.com) and then run completely offline. An internet connection was needed only to download the models the first time; after that, no case data ever left the machine. All processing happened on a secure, password-protected standalone laptop, which is why the code talks to a model at `localhost` rather than a web address.

## Beyond the script: case-level coding, validation, and ensembles

The walkthrough stops where one model finishes coding every narrative. Three further steps follow in the full study.

### From two report codes to one case code

Because each report (LE and ME) is coded separately, every case ends up with two codes. They are combined into a single **case-level** code with a simple rule: a case counts as physical IPV (1) if **either** its LE report **or** its ME report was coded 1; otherwise 0. This step is done in R by the research team. not by the model, so the rule is explicit and consistent.

## Requirement

- [Ollama](https://ollama.com) running locally with the model pulled:
  `ollama pull llama3.2:3b`.

## Data access and ethics

The narratives are sensitive, restricted-access records and are **not** included in this repository. Only the procedure is shared here; reproducing any results requires separately authorized access to the source data.

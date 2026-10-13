---
title: Ollama
nav_order: 10
---

# {{ page.title }}
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Ollama commands

### Pull models

#### Pull a model

* ollama pull <model name>

  Downloads a model, e.g. `ollama pull llama3`

  More info: https://github.com/ollama/ollama

#### Pull a specific model version/tag

* ollama pull <model name>:<tag>

  e.g. `ollama pull llama3:8b`

### Run models

#### Run a model

* ollama run <model name>

  Starts an interactive session with the model, pulling it first if not already installed

#### Run a model with a single prompt

* ollama run <model name> "<prompt>"

  Runs the model with the given prompt and returns the output

### List models

#### List installed models

* ollama list

  Lists all models that have been pulled locally

#### List running models

* ollama ps

  Lists the models currently loaded into memory

### Remove models

#### Remove a model

* ollama rm <model name>

# LLM Local Setup Tests

Testiranje postavljanja i performansi lokalnih LLM modela na regularnom hardveru (CPU i GPU), kroz tri različita alata: Ollama, LM Studio i text-generation-webui.

## Notebook-ovi (Google Colab)

| Notebook | Opis | Link |
|---|---|---|
| `notebooks/01_ollama_cpu_gpu_testovi.ipynb` | Instalacija Ollame, GPU test (Colab T4), 5 modela | https://colab.research.google.com/drive/1VF0fdXUq6nCRSzss8hDTUaShy3CbM5VV?usp=sharing
| `notebooks/02_textgenwebui_gpu_testovi.ipynb` | Instalacija text-generation-webui, GPU test, 5 modela | https://colab.research.google.com/drive/1-lObWuqWX5Gjl1CM2o6UEoBq5OZRgS5G?usp=sharing

## Rezultati

- `results/rezultati_ollama_cpu_lokalni_racunar.txt` — rezultati pomoću Ollame, CPU, lični računar (bez dedikovane GPU kartice)
- `results/rezultati_lmstudio_kolega.txt` — rezultati pomoću LM Studio-a, isti test-prompt

## Testirani modeli

- Qwen2.5-Coder 7B (Q4_K_M)
- Qwen2.5-Coder 1.5B (Q4_K_M)
- Gemma2 2B (Q4_0)
- Llama3.2 3B (Q4_K_M)
- Mistral 7B (Q4_K_M)

## Šta je testirano

- Brzina generisanja (tokens/s) — CPU vs GPU
- Brzina i kvalitet — Ollama vs text-generation-webui vs LM Studio
- Kvalitet odgovora po veličini/specijalizaciji modela na složenijem zadatku (LRU cache implementacija)

## Napomena o okruženju

CPU testovi izvedeni su na ličnom računaru bez dedikovane GPU kartice (Intel integrisana grafika). GPU testovi izvedeni su putem Google Colab (Nvidia Tesla T4, 15GB VRAM) kao simulacija jačeg, ali i dalje regularnog hardvera — ne predstavlja instalaciju na fizičkom, privatnom računaru.

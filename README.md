# LLM Local Setup Tests

Testiranje postavljanja i performansi lokalnih LLM modela na regularnom hardveru (CPU i GPU), kroz četiri različita alata: Ollama, LM Studio, text-generation-webui i vLLM (server pristup za više korisnika).

## Notebook-ovi (Google Colab)

| Notebook | Opis | Link |
|---|---|---|
| `notebooks/01_ollama_cpu_gpu_testovi.ipynb` | Instalacija Ollame, GPU test (Colab T4), 5 modela | https://colab.research.google.com/drive/1VF0fdXUq6nCRSzss8hDTUaShy3CbM5VV?usp=sharing |
| `notebooks/02_textgenwebui_gpu_testovi.ipynb` | Instalacija text-generation-webui, GPU test, 5 modela | https://colab.research.google.com/drive/1-lObWuqWX5Gjl1CM2o6UEoBq5OZRgS5G?usp=sharing |
| `notebooks/03_vllm_server_gpu_testovi.ipynb` | vLLM kao API server (Colab T4), 2 modela, simulacija 1/8/16 istovremenih korisnika | https://colab.research.google.com/drive/1m1k0djoFMS1W2t4xeSUVOp2xky1Y7M9G?usp=sharing |

## Rezultati

- `results/rezultati_ollama_cpu_lokalni_racunar.txt` — rezultati pomoću Ollame, CPU, lični računar (bez dedikovane GPU kartice)
- `results/rezultati_lmstudio_kolega.txt` — rezultati pomoću LM Studio-a, isti test-prompt
- `results/rezultati_vllm.txt` — rezultati pomoću vLLM servera (GPU, Colab T4): pojedinačni zahtev + paralelni zahtevi (1, 8 i 16 istovremenih korisnika)

## Testirani modeli

**Ollama / LM Studio / text-generation-webui (GGUF, Q4):**
- Qwen2.5-Coder 7B (Q4_K_M)
- Qwen2.5-Coder 1.5B (Q4_K_M)
- Gemma2 2B (Q4_0)
- Llama3.2 3B (Q4_K_M)
- Mistral 7B (Q4_K_M)

**vLLM (safetensors, AWQ 4-bit):**
- Qwen2.5-Coder-7B-Instruct (AWQ) — isti model kao gore, radi direktnog poređenja alata
- Qwen3-8B (AWQ) — novija generacija, trenutno među najboljima u maloj klasi

## Šta je testirano

- Brzina generisanja (tokens/s) — CPU vs GPU
- Brzina i kvalitet — Ollama vs text-generation-webui vs LM Studio vs vLLM
- Kvalitet odgovora po veličini/specijalizaciji modela na složenijem zadatku (LRU cache implementacija)
- Server pristup za tim (vLLM): ponašanje pod opterećenjem od 1, 8 i 16 istovremenih korisnika — ukupan protok servera (tokens/s), prosečno i najgore vreme čekanja po korisniku

### Ključni nalaz vLLM testa

Zahvaljujući continuous batching-u, protok servera raste sa brojem istovremenih korisnika umesto da se zahtevi ređaju u red: Qwen2.5-Coder 7B na jednom T4 GPU-u daje ~31 tokena/s za jednog korisnika, a ~278 tokena/s ukupno za 16 istovremenih korisnika (~9x), pri čemu prosečno čekanje po korisniku raste tek sa ~17s na ~22s. Kod alata za jednog korisnika (Ollama, LM Studio) 16 istovremenih zahteva bi se obrađivalo sekvencijalno.

## Napomena o okruženju

CPU testovi izvedeni su na ličnom računaru bez dedikovane GPU kartice (Intel integrisana grafika). GPU testovi izvedeni su putem Google Colab (Nvidia Tesla T4, 15GB VRAM) kao simulacija jačeg, ali i dalje regularnog hardvera — ne predstavlja instalaciju na fizičkom, privatnom računaru.

Napomene specifične za vLLM na T4: potrebna je pinovana verzija `vllm==0.9.2` (novije verzije ne rade na compute capability 7.5) uz `transformers<4.54`, kao i `--dtype half` jer T4 ne podržava bfloat16. Modeli se preuzimaju sa Hugging Face-a (Llama i Gemma zahtevaju HF nalog i prihvatanje licence, pa su korišćeni otvoreni Qwen modeli).

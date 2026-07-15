# LLM Local Setup Tests

Testiranje postavljanja i performansi lokalnih LLM modela na regularnom hardveru (CPU i GPU), kroz pet različitih alata: Ollama, LM Studio, llama.cpp, text-generation-webui i vLLM (server pristup za više korisnika).

## Notebook-ovi (Google Colab)

| Notebook | Opis | Link |
|---|---|---|
| `notebooks/01_ollama_cpu_gpu_testovi.ipynb` | Instalacija Ollame, GPU test (Colab T4), 5 modela | https://colab.research.google.com/drive/1VF0fdXUq6nCRSzss8hDTUaShy3CbM5VV?usp=sharing |
| `notebooks/02_textgenwebui_gpu_testovi.ipynb` | Instalacija text-generation-webui, GPU test, 5 modela | https://colab.research.google.com/drive/1-lObWuqWX5Gjl1CM2o6UEoBq5OZRgS5G?usp=sharing |
| `notebooks/03_vllm_server_gpu_testovi.ipynb` | vLLM kao API server (Colab T4), 2 modela, simulacija 1/8/16 istovremenih korisnika | https://colab.research.google.com/drive/1m1k0djoFMS1W2t4xeSUVOp2xky1Y7M9G?usp=sharing |

## Rezultati

- `results/rezultati_ollama_cpu_lokalni_racunar.txt` — rezultati pomoću Ollame, CPU, lični računar bez dedikovane GPU kartice
- `results/rezultati_lmstudio.txt` — rezultati pomoću LM Studio-a, isti test-prompt
- `results/rezultati_llama_cpp.txt` — rezultati pomoću llama.cpp, CPU, lokalni Windows računar
- `results/rezultati_vllm.txt` — rezultati pomoću vLLM servera (GPU, Colab T4): pojedinačni zahtev + paralelni zahtevi (1, 8 i 16 istovremenih korisnika)

## Testirani modeli

**Ollama / LM Studio / text-generation-webui (GGUF, Q4):**
- Qwen2.5-Coder 7B (Q4_K_M)
- Qwen2.5-Coder 1.5B (Q4_K_M)
- Gemma2 2B (Q4_0)
- Llama3.2 3B (Q4_K_M)
- Mistral 7B (Q4_K_M)

**llama.cpp (GGUF, Q4_K_M):**
- Qwen2.5-Coder 7B
- Qwen2.5-Coder 1.5B
- Llama3.2 3B

**vLLM (safetensors, AWQ 4-bit):**
- Qwen2.5-Coder-7B-Instruct (AWQ) — isti osnovni model kao gore, radi orijentacionog poređenja
- Qwen3-8B (AWQ)

## Šta je testirano

- Brzina generisanja (tokens/s) — CPU vs GPU
- Brzina i kvalitet — Ollama vs llama.cpp vs text-generation-webui vs LM Studio vs vLLM
- Kvalitet odgovora na istom zadatku: Python funkcija za proveru da li je broj prost, sa objašnjenjem koda
- llama.cpp kao lokalni OpenAI-kompatibilan server, uz merenje prompt-eval i generation metrika
- Server pristup za tim (vLLM): ponašanje pod opterećenjem od 1, 8 i 16 istovremenih korisnika — ukupan protok servera (tokens/s), prosečno i najgore vreme čekanja po korisniku

### Ključni nalaz vLLM testa

Zahvaljujući continuous batching-u, protok servera raste sa brojem istovremenih korisnika: Qwen2.5-Coder 7B na jednom T4 GPU-u daje oko 31 token/s za jednog korisnika, a oko 278 token/s ukupno za 16 istovremenih korisnika, dok prosečno vreme čekanja raste sa približno 17 s na 22 s. Ostali alati u ovom radu nisu testirani pod istim paralelnim opterećenjem.

## Napomena o okruženju

Ollama CPU i llama.cpp CPU testovi izvedeni su na lokalnom računaru bez dedikovane GPU kartice. llama.cpp je kompajliran kao Release CPU build pomoću MSVC-a i CMake-a, a modeli su pokretani preko `llama-server` API-ja. GPU testovi izvedeni su putem Google Colab-a (NVIDIA Tesla T4, 15 GB VRAM), dok je LM Studio testiran na dodatnom uređaju.

Napomene specifične za vLLM na T4: korišćena je pinovana verzija `vllm==0.9.2` uz `transformers<4.54` i `--dtype half`. vLLM test je rađen sa AWQ modelima, dok su ostali alati prvenstveno koristili GGUF modele.

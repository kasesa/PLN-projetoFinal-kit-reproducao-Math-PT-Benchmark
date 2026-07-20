# readmecolab

Este arquivo descreve **apenas** o notebook `kit-reproducao-colab.ipynb` — o que ele contém e como executá-lo. Para os demais notebooks/scripts do repositório, ver `README.md`.

## O que é

`kit-reproducao-colab.ipynb` reúne, em um único notebook autocontido, todo o pipeline deste repositório para reproduzir e comparar com o artigo **MATH-PT: A Math Reasoning Benchmark for European and Brazilian Portuguese** (Teixeira et al.; dataset em [HuggingFace](https://huggingface.co/datasets/tiagoteixeira03/MATH-PT); repositório oficial [deep-spin/math-benchmark](https://github.com/deep-spin/math-benchmark)):

- Cálculo de acurácia de múltipla escolha e questões abertas a partir dos dados já publicados no repositório.
- Comparação número a número com as Tabelas 1–4 do artigo, com interpretação didática dos resultados.
- Geração opcional de novos dados (envio de prompts a um modelo via API e avaliação das respostas).

Feito para rodar do início ao fim no Google Colab, sem depender de nada do ambiente local além do próprio notebook.

## Estrutura do notebook

| Parte | Conteúdo | Precisa de chave de API? |
|---|---|---|
| 0 | Setup: instala dependências (`openai`, `tenacity`, `tqdm`, `python-dotenv`, `nest_asyncio`, `pandas`, `matplotlib`) e clona o repositório | Não |
| 1 | Credenciais de API (`OPEN_ROUTER_API_KEY`) | — (só é usada na Parte 5) |
| 2 | Núcleo assíncrono de envio de prompts (`send_prompts_to_model`) e funções de avaliação reutilizáveis (`compute_mc_accuracy`, `compute_oe_accuracy`, `parse_boxed_letter`, ...) | Não |
| 3 | Reproduz os resultados já publicados: 3.1 múltipla escolha (execução única), 3.2 variabilidade entre execuções (`first_run`/`second_run`, gera um JSON por execução + gráfico de média±desvio padrão), 3.3 questões abertas via juiz LLM | Não |
| 4 | Compara cada uma das Tabelas 1–4 do artigo com os números recalculados neste notebook (artigo × nosso × Δ), com interpretação didática das diferenças | Não |
| 5 | Gera novos dados do zero (opcional): monta prompts a partir de `data/*.json`, envia a um modelo novo (OpenRouter ou servidor vLLM local) e avalia as respostas | Sim |
| 6 | Exporta a pasta `results/` (zip para download ou cópia para o Google Drive) | Não |

As Partes 0–4 usam somente os arquivos já publicados no repositório (`data/`, `prompts/`, `results/`, `judge-prompts/`, `judge-results/`) e não fazem nenhuma chamada de API. Só a Parte 5 chama um modelo de fato.

## Como executar

### Opção A — Google Colab (recomendado)

1. Abra o notebook pelo badge "Open in Colab" no topo do arquivo, ou diretamente em:
   `https://colab.research.google.com/github/deep-spin/math-benchmark/blob/main/kit-reproducao-colab.ipynb`
2. Rode as células **em ordem**, de cima para baixo (Parte 0 → 1 → 2 → ...). A Parte 0 já clona o repositório dentro do ambiente do Colab.
3. Se for só reproduzir/comparar com o artigo (Partes 3 e 4), pode pular a Parte 1 (credenciais) — nenhuma chave é necessária.
4. Se quiser gerar dados com um modelo novo (Parte 5), rode a Parte 1 e cole sua `OPEN_ROUTER_API_KEY` quando solicitado (ou configure um servidor local compatível com a API da OpenAI, ex. vLLM).
5. Ao final, use a Parte 6 para baixar os resultados (`.zip`) ou copiá-los para o Google Drive.

### Opção B — Ambiente local (Jupyter)

1. Tenha este repositório clonado localmente (ele já contém `data/`, `prompts/`, `results/`, `judge-prompts/`, `judge-results/`).
2. Instale as dependências:
   ```bash
   pip install openai tenacity tqdm python-dotenv nest_asyncio pandas matplotlib jupyter
   ```
3. Abra o notebook a partir da raiz do repositório (ele assume que o diretório de trabalho é a raiz):
   ```bash
   jupyter notebook kit-reproducao-colab.ipynb
   ```
4. Na Parte 0, pule a célula de `!git clone` (você já está no repositório) — a célula detecta isso automaticamente se rodada de dentro dele.
5. Rode as demais células em ordem, como na Opção A. Para a Parte 5, crie um arquivo `.env` na raiz do repositório com `OPEN_ROUTER_API_KEY=<sua-chave>` em vez de colar a chave interativamente.

## Saídas geradas

Tudo é salvo em `results/accuracy-reports/`:

- `*-acc-report.txt` — relatório de acurácia por modelo/nível (Parte 3.1 e 3.3).
- `*-first_run-acc.json`, `*-second_run-acc.json` — acurácia bruta de cada execução (Parte 3.2).
- `*-variability.png` — gráfico de acurácia média ± desvio padrão entre execuções (Parte 3.2).

## Observação sobre o artigo

O notebook usa como referência o artigo **MATH-PT** (ver seção acima). Um link para `aclanthology.org/2025.findings-emnlp.3` foi cogitado inicialmente para essa comparação, mas aponta para um artigo diferente (sobre formatos de prompt em tradução automática) — sem relação com este benchmark. Isso está documentado na própria Parte 4 do notebook.

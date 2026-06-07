---
name: video-analyzer
description: Analisa um arquivo de vídeo com o Google Gemini e retorna um relatório estruturado em markdown cobrindo resumo geral, decomposição cena a cena, transcrição do áudio (ou uma nota honesta de "silencioso"), detalhes visuais e momentos-chave com marcação de tempo. Possui salvaguardas fortes contra alucinação — não inventa narradores, locuções nem nomes de quem fala. Use quando precisar entender o que de fato acontece em um vídeo.
argument-hint: <caminho/para/video.mp4> [--prompt "..."] [--fps N] [--model ...]
disable-model-invocation: true
allowed-tools: Bash, Read
---

# Analisar Vídeo

Analisa um arquivo de vídeo com o Gemini e retorna um relatório estruturado em markdown.

## Pré-requisitos

- Python 3.10+
- `google-genai` instalado globalmente (qualquer Python que o shell encontre via `python3` funciona — verificado funcionando na versão 1.64.0)
- `GEMINI_API_KEY` definida no ambiente do shell do usuário (por exemplo, exportada no `~/.zshrc`)

## Passos

1. Faça o parse dos argumentos a partir de `$ARGUMENTS`:
   - **caminho do vídeo** (obrigatório) — caminho para o arquivo de vídeo
   - **--prompt** (opcional) — prompt de análise personalizado; o padrão é um prompt de relatório estruturado com regras anti-alucinação
   - **--fps** (opcional) — taxa de amostragem de quadros personalizada (útil para capturar cortes de menos de um segundo em filmagens de ritmo acelerado)
   - **--model** (opcional) — ID do modelo Gemini; o padrão é `gemini-3-flash-preview`

2. Verifique se o arquivo de vídeo existe no caminho informado. Se não existir, reporte o erro e pare.

3. Execute o script de análise usando o caminho absoluto do local de instalação:

```bash
python3 ~/.claude/skills/video-analyzer/scripts/analyze_video.py $ARGUMENTS
```

4. O script irá:
   - Fazer upload do vídeo — inline para arquivos ≤18MB, via Files API para arquivos maiores (com sondagem de até 300s aguardando o estado ACTIVE)
   - Enviar o prompt ao Gemini com o vídeo anexado
   - Imprimir o relatório completo em markdown na stdout (linhas de informação/progresso vão para a stderr)

5. Capture a stdout e apresente o relatório ao usuário.

6. Se o script terminar com erro, ajude o usuário a resolver o problema:
   - **Chave de API ausente**: confirme que `echo $GEMINI_API_KEY` não está vazio no shell dele. Se estiver apenas no `~/.zshrc`, pode ser necessário abrir um novo terminal ou rodar `source ~/.zshrc`.
   - **Formato não suportado**: deve ser um dos seguintes: mp4, mov, avi, webm, mpeg, mpg, wmv, 3gpp, 3gp, flv
   - **Timeout de upload**: arquivo grande ou conexão lenta — tente novamente ou use um clipe mais curto
   - **Erro de modelo / 404**: tente outro modelo com `--model gemini-2.5-flash`

## Saída

Um relatório em markdown impresso na stdout com estas seções:

- **Resumo Geral** — visão geral de 2 a 3 frases do que de fato acontece
- **Decomposição Cena a Cena** — marcações de tempo `MM:SS` para cada corte/cena, com conteúdo na tela, ações e texto literal
- **Áudio** — transcrição literal com marcações de tempo, OU uma nota honesta de "sem áudio / silencioso / apenas ambiente" (o prompt proíbe explicitamente inventar narradores)
- **Detalhes Visuais** — texto na tela, elementos de interface, produtos, marca, pessoas
- **Momentos-Chave** — 3 a 7 destaques com marcação de tempo que um espectador lembraria

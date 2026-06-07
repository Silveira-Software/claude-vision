# Claude Vision — Skill de Análise de Vídeo

Junte-se a mais de 550 equipes de Marketing de Performance dentro do SCALE AI: https://www.skool.com/scale-ai/about

Dê ao Claude Code a capacidade de "assistir" vídeos.

Esta é uma skill do Claude Code que encaminha qualquer arquivo de vídeo pela API Gemini do Google (que tem entendimento nativo de vídeo) e retorna um relatório estruturado em markdown — resumo geral, decomposição cena a cena com marcações de tempo, transcrição de áudio, detalhes visuais e momentos-chave. Funciona com gravações de tela, anúncios UGC, tutoriais, demonstrações, gravações de reuniões — qualquer coisa que o Gemini consiga processar.

Possui salvaguardas fortes contra alucinação: não inventa narradores, locuções nem nomes de quem fala que não estejam de fato no vídeo.

## Instalação

### 1. Clone este repositório

```bash
git clone https://github.com/mikefutia/claude-vision.git
```

### 2. Mova para a sua pasta de skills do Claude Code

```bash
mv claude-vision ~/.claude/skills/video-analyzer
```

O nome da pasta **deve** ser `video-analyzer` — é assim que o Claude Code encontra a skill.

### 3. Obtenha uma chave de API gratuita do Gemini

Acesse o [Google AI Studio](https://aistudio.google.com/apikey) e crie uma chave. O nível gratuito é generoso e suficiente para uso pessoal.

### 4. Defina a chave de API

A forma mais fácil: abra o Claude Code em qualquer projeto e peça para ele configurar a chave para você. Algo como:

> "Defina minha GEMINI_API_KEY como `sua_chave_aqui` para que fique disponível em todo novo shell."

O Claude Code adicionará o export ao perfil do seu shell e confirmará que funciona. Você não precisará mexer no `.zshrc` por conta própria.

### 5. Instale a dependência Python

A skill usa o SDK oficial do Gemini do Google:

```bash
pip install google-genai
```

Se o pip reclamar de um ambiente gerenciado externamente, use:

```bash
pip install google-genai --break-system-packages
```

### 6. Use a skill

No Claude Code, basta apontar para um vídeo:

> "Use a skill video-analyzer em /caminho/para/meu-video.mp4"

Ou invoque diretamente:

> "/video-analyzer ~/Downloads/demo.mp4"

O Claude executará a análise e apresentará o relatório estruturado.

## O que você pode fazer com ela

- **Decomposição de anúncios** — solte um anúncio UGC de um concorrente e obtenha uma análise momento a momento
- **Tutorial → POP** — transforme uma gravação do Loom em um guia escrito passo a passo
- **Resumos de reuniões** — extraia decisões e itens de ação de uma chamada
- **Notas de demonstração** — resuma o que aconteceu em uma gravação de tela
- **"O que tem neste vídeo?" em geral** — qualquer vídeo, qualquer pergunta

## Formatos suportados

mp4, mov, webm, avi, mpeg, mpg, flv, wmv, 3gpp, 3gp

## Flags opcionais

```
/video-analyzer <caminho> [--prompt "prompt personalizado"] [--fps N] [--model gemini-2.5-flash]
```

- `--prompt` — substitua o prompt padrão de relatório estruturado pelo que quiser
- `--fps` — altere a taxa de amostragem de quadros (padrão 1 fps; aumente para conteúdo com cortes rápidos)
- `--model` — escolha outro modelo Gemini (padrão `gemini-3-flash-preview`)

## Resolução de problemas

- **"GEMINI_API_KEY environment variable is not set"** — sua chave não está visível para o shell em que o Claude Code está rodando. Abra um novo terminal e tente novamente, ou peça ao Claude Code para corrigir.
- **"google-genai is not installed"** — rode `pip install google-genai` (veja o passo 5).
- **Timeout de upload em arquivos grandes** — a Files API do Gemini pode levar de 30 a 60 segundos para processar vídeos mais longos. O script faz sondagem por até 5 minutos antes de desistir.
- **Modelo 404** — tente `--model gemini-2.5-flash` se o modelo preview padrão não estiver disponível na sua região.

## Licença

MIT — faça o que quiser com ela.

# LEIA! — Webapp de Correção Diagnóstica

**Levantamento Integrado de Avaliação em Leitura · v1-alpha**

Aplicativo web de correção e diagnóstico do instrumento LEIA!, desenvolvido para uso em celular diretamente pelo professor, sem instalação e sem servidor.

---

## Arquivos

| Arquivo | Modo de uso |
|---|---|
| `index.html` | Entrada manual de respostas |
| `scan.html` | Leitura automática da folha de respostas por câmera |

Ambos disponíveis em: **https://rodrigoleaopb.github.io/leia-app/**

---

## index.html — Entrada manual

Fluxo completo offline (zero dependências externas — jsPDF embutido):

1. **Identificação** — nome, turma, nº, escola, data
2. **Respostas** — 20 questões com avanço automático 280 ms após seleção; grade de miniatura para navegar entre questões
3. **Ficha diagnóstica** — perfil de leitura (A–D), subscores por processo (P0–P4), diagnóstico por distrator de cada erro
4. **Exportar PDF** — ficha individual no padrão visual LEIA! (cabeçalho navy, tabela de revisão com diagnósticos)

Funciona completamente offline após o primeiro carregamento.

---

## scan.html — Leitura por câmera (OMR)

Lê automaticamente a **folha de respostas para escaneamento** (`04-folha-respostas-scan.pdf`) usando processamento de imagem no próprio navegador.

### Fluxo

1. **Fotografar** — abre câmera traseira ou galeria
2. **Reconhecer** — processamento automático no navegador:
   - Detecta os 4 marcadores de canto (quadrados pretos 8 × 8 mm)
   - Corrige perspectiva via transformação homográfica (OpenCV.js)
   - Normaliza para 1050 × 1485 px (5 px/mm, A4)
   - Amostra cada bolha com threshold Otsu + critério duplo de detecção
3. **Confirmar** — grade editável com status de detecção:
   - Badge verde: 4 marcadores detectados, perspectiva corrigida
   - Badge âmbar: marcadores não localizados, enquadramento automático usado
   - Questões sem resposta detectada destacadas com borda laranja — toque para preencher manualmente
4. **Identificação** → **Ficha + PDF** — mesma lógica e exportação do `index.html`

### Critério de detecção das bolhas

As bolhas contêm letras impressas em navy (≈ 20–25 % de pixels escuros mesmo sem preenchimento). O app só registra uma resposta quando:

- **Fill ≥ 0.42** — descarta bolhas com apenas a letra impressa
- **Dominância ≥ 1.8×** — a bolha marcada deve ter quase o dobro do fill da segunda mais escura

Questões que não atingirem os dois critérios ficam em branco para correção manual.

### Dependências (online)

| Biblioteca | Uso |
|---|---|
| OpenCV.js 4.9.0 (CDN, ~8 MB) | Perspectiva + threshold |
| jsPDF 2.5.1 (CDN) | Exportação PDF |

---

## Dados do instrumento

| Constante | Valor |
|---|---|
| Questões | 20 (múltipla escolha, 4 alternativas) |
| Processos | P0 (4 q.) · P1 (4 q.) · P2 (7 q.) · P3 (4 q.) · P4 (1 q.) |
| Perfis | A · B · C · D (critério sequencial sobre P0→P1→P2) |

---

## Folha de respostas para escaneamento

Gerada por `exame/gerar-folha-scan.py` (ReportLab). Especificações relevantes para o OMR:

- Bolhas: raio 4,6 mm, espaçamento centro a centro 12,4 mm
- Layout: 2 colunas × 10 linhas, primeira bolha a 45,6 mm da borda esquerda (col. 1) e 140,6 mm (col. 2)
- Marcadores de canto: quadrados 8 × 8 mm com cruz branca central, recuo 9,5 mm das bordas

---

*Projeto LEIA! · Rodrigo Leão · contato.profleao@gmail.com*

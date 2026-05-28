# Validacao local - ICARUS solar panel PCB v0.6

Data: 2026-05-24

Estado: revisao interna pre-validacao fisica. Nao aprovado para fabrico final nem para voo.

## Correcao de polaridade

A ligacao eletrica final da string 2S1P e:

| Terminal | Net |
|---|---|
| `SC1_NEG` | `PANEL_NEG` |
| `SC1_POS` | `SERIES_MID` |
| `SC2_NEG` | `SERIES_MID` |
| `SC2_POS` | `PANEL_POS` |

Foram corrigidos textos e labels que indicavam a polaridade invertida de `SC2`.

## Alteracoes de PCB

- `SC1` movida para centro nominal `(105.000, 84.425)`.
- `SC2` movida para centro nominal `(105.000, 125.575)`.
- Gap nominal entre corpos das celulas: `1.000 mm`.
- Rota principal `PANEL_NEG` em `B.Cu` desviada para fora do corpo nominal das celulas.
- Rotas dos test pads laterais desviadas pela margem exterior.
- `J1` mantem pinout EPS: pinos 1/2 negativos, pinos 3/4 positivos.
- `D1` continua DNP e excluido da BOM.
- Os ficheiros KiCad de referencia desta revisao sao `hardware/ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_sch`, `hardware/ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_pcb` e `hardware/ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_pro`.

## Estado dos relatorios

Ficheiros gerados:

- `reports/ERC_v0p6.rpt`
- `reports/DRC_v0p6.rpt`
- `reports/DRC_v0p6_layout_only.rpt`
- `reports/v0p6_top.svg`
- `reports/v0p6_bottom.svg`
- `reports/v0p6_fab.svg`

Resultado ERC CLI:

- KiCad CLI 10.0.3 gerou `reports/ERC_v0p6.rpt`.
- Resultado: `0 errors`, `3 warnings`.
- Os 3 warnings sao de resolucao de bibliotecas de footprints (`ICARUS_Solar` e `ICARUS_Connector`) no contexto do CLI. Nao sao erros eletricos.
- As tabelas locais foram confirmadas: `hardware/sym-lib-table` aponta para `ICARUS.kicad_sym`; `hardware/fp-lib-table` aponta para `ICARUS_Solar` e `ICARUS_Connector`.
- Waiver tecnico: `test_logs/LIBRARY_WARNINGS_WAIVER_v0p6.md`.

Resultado DRC CLI:

- O DRC CLI abortou no macOS com exit code `-1` (registado em `reports/DRC_v0p6.rpt` e `reports/DRC_v0p6_layout_only.rpt`).
- Em 2026-05-28 o DRC foi corrido com sucesso no KiCad 10.0 (Windows), com schematic parity: `reports/DRC_v0p6_kicad10_win.rpt` (ERC: `reports/ERC_v0p6_kicad10_win.rpt`).
- Paridade esquematico<->PCB: **0 problemas** (confirma a correcao de polaridade de SC2). Itens desconectados: **0**.
- Total: **24 violacoes = 11 erros + 13 avisos**.
- Erros de layout (a corrigir antes de fabrico):
  - 2x `shorting_items`: **curto entre `PANEL_POS` e `PANEL_NEG`** junto a J1 (bus inferior de PANEL_POS demasiado perto das vias de PANEL_NEG em (103.75, 148.5) e (126, 148.5)).
  - 6x `hole_clearance`: pistas dentro dos furos M3 (H1..H4), isolamento 0.0 mm vs 0.25 mm.
  - 3x `clearance`: 0.15 mm vs 0.20 mm na zona de J1.
- Avisos (13): 3x bibliotecas locais nao ativadas no contexto CLI (ver waiver); 10x `lib_footprint_mismatch` em footprints standard (TestPoint/Diode/MountingHole) que diferem das copias de biblioteca.

## Bloqueadores antes de fabrico

- Footprint Molex 53398-0471 ainda preliminar ate comparacao com drawing oficial e conector real.
- Footprint da celula ainda preliminar ate validacao com celula real/drawing.
- Cobre sob celula ainda precisa de decisao final de isolamento/keepout.
- Harness real ainda nao validado pino a pino.
- Analise termica ainda pendente para o risco `Voc_2S > 5.5 V` em frio.
- Erros de DRC de layout por corrigir: curto `PANEL_POS`/`PANEL_NEG` junto a J1 e pistas dentro dos furos M3 (ver `reports/DRC_v0p6_kicad10_win.rpt`).

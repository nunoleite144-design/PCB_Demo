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

- O DRC CLI abortou no macOS com exit code `-1`, tanto com schematic parity como em modo layout-only.
- `reports/DRC_v0p6.rpt` e `reports/DRC_v0p6_layout_only.rpt` registam esta falha.
- Nao e reivindicado DRC aprovado por CLI.
- O DRC GUI completo no KiCad continua obrigatorio antes de qualquer decisao de fabrico.
- O relatorio GUI real esperado e `reports/DRC_GUI_v0p6.rpt`; nao foi gerado neste ambiente porque exige execucao interativa no PCB Editor.

## Bloqueadores antes de fabrico

- Footprint Molex 53398-0471 ainda preliminar ate comparacao com drawing oficial e conector real.
- Footprint da celula ainda preliminar ate validacao com celula real/drawing.
- Cobre sob celula ainda precisa de decisao final de isolamento/keepout.
- Harness real ainda nao validado pino a pino.
- Analise termica ainda pendente para o risco `Voc_2S > 5.5 V` em frio.

# Acoes de revisao - ICARUS solar panel PCB v0.6

Data: 2026-05-24

> **⚠️ SUPERSEDED em v0.7 (2026-05-28).** A revisao v0.7 mudou a topologia para 1S2P paralelo e o J1 para B.Cu — ver [VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md](VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md). As accoes abaixo referem-se ao baseline 2S1P da v0.6.

Estado: pronta para revisao tecnica interna apos revisao GUI da equipa. Nao e fabrication-ready nem flight-ready.

## Alteracoes feitas

- Corrigida a polaridade logica final da string 2S1P.
- Corrigidos textos de `SC2` para `SC2- = SERIES_MID` e `SC2+ = PANEL_POS`.
- Normalizado o footprint local da celula: pad `1` = NEG, pad `2` = POS.
- Criado gap nominal de 1.0 mm entre os corpos de `SC1` e `SC2`.
- Desviada a rota principal `PANEL_NEG` para fora do corpo nominal das celulas.
- Mantido `J1` com pins 1/2 em `PANEL_NEG` e pins 3/4 em `PANEL_POS`.
- Mantido `D1` como TVS/clamp opcional DNP, excluido da BOM.
- Atualizada documentacao de validacao e riscos.

## Ficheiros principais modificados

- `hardware/ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_sch`
- `hardware/ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_pcb`
- `hardware/ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_pro`
- `hardware/footprints/ICARUS_Solar.pretty/TJ3xSCA_4080_CIC.kicad_mod`
- `README.md`
- `docs/MECHANICAL_CELL_CLEARANCE_v0p6.md`
- `docs/CELL_UNDER_COPPER_RISK_v0p6.md`
- `docs/VALIDACAO_LOCAL_v0p6.md`

## Riscos ainda abertos

- Footprint Molex preliminar.
- Footprint da celula preliminar.
- Conector `J1` precisa de validacao de orientacao, harness e possivel interferencia/sombreamento.
- Cobre/pads sob a celula dependem de isolamento, adesivo e geometria real das tabs.
- Risco termico de `Voc_2S` exceder 5.5 V em frio continua pendente.
- DRC CLI KiCad 10.0.3 abortou no macOS; DRC GUI completo continua obrigatorio.
- DRC GUI real `reports/DRC_GUI_v0p6.rpt` ainda tem de ser gerado no KiCad PCB Editor.

## Testes fisicos obrigatorios antes de ligar ao EPS

- Medir polaridade real de cada celula sob iluminacao.
- Medir `Voc` de cada celula.
- Medir `Voc_2S` e confirmar soma aproximada.
- Medir continuidade de `PANEL_NEG`, `SERIES_MID` e `PANEL_POS`.
- Validar harness pino a pino.
- Testar primeiro com carga resistiva ou fonte limitada.
- Ligar ao EPS so depois de continuidade, polaridade e carga aprovadas.
- Nunca ligar ao EPS sem confirmar polaridade no conector.

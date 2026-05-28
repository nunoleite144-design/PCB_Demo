# Validacao local - ICARUS solar panel PCB v0.6

Data: 2026-05-24

> **⚠️ SUPERSEDED em v0.7 (2026-05-28).** Os relatorios e estado descritos abaixo referem-se a v0.6 (2S1P). A v0.7 (1S2P paralelo, J1 em B.Cu) tem relatorios proprios em `reports/ERC_v0p7_kicad10_win.rpt` e `reports/DRC_v0p7_kicad10_win.rpt` — ver [VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md](VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md).

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

Ficheiros gerados (corrida actual, KiCad 10.0 Windows):

- `reports/ERC_v0p6_kicad10_win.rpt`
- `reports/DRC_v0p6_kicad10_win.rpt`
- `reports/v0p6_top.svg`
- `reports/v0p6_bottom.svg`
- `reports/v0p6_fab.svg`

Nota: as corridas anteriores no macOS (`DRC_v0p6.rpt`, `DRC_v0p6_layout_only.rpt`, `ERC_v0p6.rpt`) abortaram com exit code `-1` e foram removidas em 2026-05-28 como obsoletas.

Resultado ERC CLI (Windows KiCad 10.0):

- KiCad CLI 10.0 gerou `reports/ERC_v0p6_kicad10_win.rpt`.
- Resultado: `0 errors`, 3 warnings de resolucao de bibliotecas de footprints (`ICARUS_Solar` e `ICARUS_Connector`) no contexto do CLI. Nao sao erros eletricos.
- As tabelas locais foram confirmadas: `hardware/sym-lib-table` aponta para `ICARUS.kicad_sym`; `hardware/fp-lib-table` aponta para `ICARUS_Solar` e `ICARUS_Connector`.
- Waiver tecnico: `test_logs/LIBRARY_WARNINGS_WAIVER_v0p6.md`.

Resultado DRC CLI (Windows KiCad 10.0):

- DRC corrido com sucesso, com schematic parity: `reports/DRC_v0p6_kicad10_win.rpt`.
- A primeira corrida (estado anterior) tinha 11 erros de layout, incluindo um curto `PANEL_POS`/`PANEL_NEG` e pistas dentro dos furos M3, e ainda a anti-serie das celulas (ver `CORRECAO_TOPOLOGIA_SERIE_v0p6.md`).
- Apos a correcao de topologia (serie 2S real) e o reroute completo, a corrida atual da:
  - Paridade esquematico<->PCB: **0 problemas**. Itens desconectados: **0**. Curtos: **0**. `clearance`/`hole_clearance`: **0**.
  - Total: **13 violacoes = 0 erros + 13 avisos**.
- Avisos (13): 3x bibliotecas locais nao ativadas no contexto CLI (ver waiver); 10x `lib_footprint_mismatch` em footprints standard (TestPoint/Diode/MountingHole) que diferem das copias de biblioteca. Nenhum e erro.
- Plots regenerados: `reports/v0p6_top.svg`, `reports/v0p6_bottom.svg`, `reports/v0p6_fab.svg`.

## Bloqueadores antes de fabrico

- Footprint Molex 53398-0471 ainda preliminar ate comparacao com drawing oficial e conector real.
- Footprint da celula ainda preliminar ate validacao com celula real/drawing.
- Cobre sob celula ainda precisa de decisao final de isolamento/keepout.
- Harness real ainda nao validado pino a pino.
- Analise termica ainda pendente para o risco `Voc_2S > 5.5 V` em frio.
- Esquema de contacto de verso da celula e geometria das tabs por confirmar com o desenho de montagem CIC do fornecedor (o routing e a colocacao de D1 sao provisorios ate la).

# Waiver tecnico de warnings ERC/DRC - ICARUS v0.6

Data: 2026-05-24

Estado: valido apenas para revisao interna pre-validacao fisica. Nao autoriza fabrico nem voo.

## Relatorios de referencia

- ERC CLI: `reports/ERC_v0p6.rpt`
- DRC CLI: `reports/DRC_v0p6.rpt` regista falha do `kicad-cli pcb drc` no macOS.
- DRC GUI: pendente ate ser executado manualmente no KiCad e guardado em `reports/DRC_GUI_v0p6.rpt`.

## Bibliotecas locais confirmadas no projeto

- `hardware/sym-lib-table` contem a biblioteca local `ICARUS` apontada para `${KIPRJMOD}/ICARUS.kicad_sym`.
- `hardware/fp-lib-table` contem `ICARUS_Solar` apontada para `${KIPRJMOD}/footprints/ICARUS_Solar.pretty`.
- `hardware/fp-lib-table` contem `ICARUS_Connector` apontada para `${KIPRJMOD}/ICARUS_Connector.pretty`.
- `hardware/ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_pro` aponta para o esquematico `ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_sch`.

## Warnings ERC restantes

| Warning | Itens | Interpretacao | Estado |
|---|---|---|---|
| `footprint_link_issues` | `SC1`, `SC2` | O ERC CLI nao resolveu `ICARUS_Solar` no contexto usado, apesar de a tabela local existir. | Aceitavel para revisao interna; validar no KiCad GUI |
| `footprint_link_issues` | `J1` | O ERC CLI nao resolveu `ICARUS_Connector` no contexto usado, apesar de a tabela local existir. | Aceitavel para revisao interna; validar no KiCad GUI |

## Limites deste waiver

- Estes warnings nao sao aprovacao de footprints.
- O footprint Molex continua preliminar ate comparacao com drawing oficial e conector real.
- O footprint da celula continua preliminar ate validacao com celula real/drawing.
- O DRC GUI completo com schematic parity continua obrigatorio antes de qualquer decisao de fabrico.
- A PCB continua nao `fabrication-ready` e nao `flight-ready`.

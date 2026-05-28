# Waiver tecnico de warnings ERC/DRC - ICARUS v0.6

Data: 2026-05-28 (revisto)

Estado: valido apenas para revisao interna pre-validacao fisica. Nao autoriza fabrico nem voo.

## Relatorios de referencia

- ERC CLI: `reports/ERC_v0p6_kicad10_win.rpt` (KiCad 10.0, Windows; 0 erros, 3 warnings de lib).
- DRC CLI: `reports/DRC_v0p6_kicad10_win.rpt` (KiCad 10.0, Windows; 0 erros, 13 warnings; 0 desconectados; 0 paridade).
- Corridas anteriores no macOS (`reports/ERC_v0p6.rpt`, `reports/DRC_v0p6.rpt`, `reports/DRC_v0p6_layout_only.rpt`) abortaram com exit -1 e foram removidas em 2026-05-28.
- DRC GUI: pendente ate ser executado manualmente no KiCad e guardado em `reports/DRC_GUI_v0p6.rpt`.

## Bibliotecas locais confirmadas no projeto

- `hardware/sym-lib-table` contem a biblioteca local `ICARUS` apontada para `${KIPRJMOD}/ICARUS.kicad_sym`.
- `hardware/fp-lib-table` contem `ICARUS_Solar` apontada para `${KIPRJMOD}/footprints/ICARUS_Solar.pretty`.
- `hardware/fp-lib-table` contem `ICARUS_Connector` apontada para `${KIPRJMOD}/footprints/ICARUS_Connector.pretty` (unificado em 2026-05-28).
- `hardware/ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_pro` aponta para o esquematico `ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_sch`.

## Warnings ERC restantes (3)

| Warning | Itens | Interpretacao | Estado |
|---|---|---|---|
| `footprint_link_issues` | `SC1`, `SC2` | O ERC CLI nao resolveu `ICARUS_Solar` no contexto usado, apesar de a tabela local existir. | Aceitavel para revisao interna; validar no KiCad GUI |
| `footprint_link_issues` | `J1` | O ERC CLI nao resolveu `ICARUS_Connector` no contexto usado, apesar de a tabela local existir. | Aceitavel para revisao interna; validar no KiCad GUI |

## Warnings DRC restantes (13)

### Grupo A: 3x `lib_footprint_issues` (mesma raiz que warnings ERC)

| Item | Footprint | Lib | Estado |
|---|---|---|---|
| `SC1` @ (105.000, 84.425) | `TJ3xSCA_4080_CIC` | `ICARUS_Solar` nao activada no contexto CLI | Limitacao do CLI; lib local existe |
| `SC2` @ (105.000, 125.575) | `TJ3xSCA_4080_CIC` | `ICARUS_Solar` nao activada no contexto CLI | Limitacao do CLI; lib local existe |
| `J1` @ (105.000, 150.000) | `Molex_53398-0471_1x04_P1.25mm_PRELIMINARY` | `ICARUS_Connector` nao activada no contexto CLI | Limitacao do CLI; lib local existe |

Raiz comum: o `kicad-cli` corre sem o contexto de utilizador que activa as bibliotecas locais do projeto (project-local lib tables). O KiCad GUI nao reporta estes warnings. Verificado abrindo o projeto no KiCad 10.0 Windows.

### Grupo B: 10x `lib_footprint_mismatch` (footprints standard embebidos)

| Item | Footprint | Lib | Razao do mismatch |
|---|---|---|---|
| `TP1`, `TP2`, `TP3` | `TestPoint_Pad_D2.0mm` | `TestPoint` | Copia embebida divergente da lib KiCad standard (campos cosmeticos: versao geradora, propriedades, tags) |
| `TP4`, `TP5` | `TestPoint_Pad_D2.5mm` | `TestPoint` | Idem |
| `D1` | `D_SOD-123` | `Diode_SMD` | Idem |
| `H1`, `H2`, `H3`, `H4` | `MountingHole_3.2mm_M3` | `MountingHole` | Idem |

Razao: ao guardar o projeto, KiCad copia o footprint para dentro do `.kicad_pcb`. A versao da lib instalada pode mudar de release para release. O mismatch nao indica erro electrico ou geometrico — apenas metadata divergente.

## Decisao para v0.6

- **Grupo A (3 warnings)**: limitacao do CLI; nao requer accao. Validacao final e feita no KiCad GUI durante a revisao manual.
- **Grupo B (10 warnings)**: dois caminhos validos:
  - (B1) Recomendado para fabrico: antes do CDR, abrir o projeto no KiCad GUI, "Update Footprints from Library" para `TestPoint`, `Diode_SMD`, `MountingHole`, e re-correr DRC. Espera-se que reduza a 0 warnings (mantendo geometria identica). 
  - (B2) Aceitavel para revisao interna: manter como esta, com este waiver. Os footprints embebidos sao auto-suficientes — o PCB pode ser fabricado com a versao actual sem depender da lib KiCad instalada na maquina de fabrico.
- v0.6 adopta (B2) para esta release interna. (B1) e accao de fecho antes do CDR.

## Actualizacao v0.7 (2026-05-28)

Na v0.7 o `D1` (TVS DNP) foi removido (paralelo torna desnecessario), eliminando 1 warning automaticamente. Adicionalmente, foi aplicada manualmente a opcao (B1) — os 4 footprints TestPoint (`TP1`, `TP3`, `TP4`, `TP5`) e os 4 MountingHole (`H1`-`H4`) foram sincronizados com as versoes da biblioteca standard do KiCad 10.0 (`C:\Program Files\KiCad\10.0\share\kicad\footprints\TestPoint.pretty`, `...\MountingHole.pretty`). Resultado em `reports/DRC_v0p7_kicad10_win.rpt`:

- **0 erros, 3 warnings**.
- Os 3 warnings restantes pertencem todos ao Grupo A (`lib_footprint_issues` para `ICARUS_Solar` em SC1/SC2 e `ICARUS_Connector` em J1) — limitacao do CLI sobre libs locais, mantida sob waiver.

Em v0.7 o waiver e portanto apenas para Grupo A. Grupo B esta resolvido.

## Limites deste waiver

- Estes warnings nao sao aprovacao de footprints.
- O footprint Molex continua preliminar ate comparacao com drawing oficial e conector real.
- O footprint da celula continua preliminar ate validacao com celula real/drawing.
- O DRC GUI completo com schematic parity continua obrigatorio antes de qualquer decisao de fabrico.
- A PCB continua nao `fabrication-ready` e nao `flight-ready`.

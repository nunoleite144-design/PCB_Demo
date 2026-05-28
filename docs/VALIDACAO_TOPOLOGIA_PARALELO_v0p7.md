# Validação da topologia paralela (1S2P) — ICARUS v0.7

Data: 2026-05-28

Estado: revisão interna pré-validação física. Não aprovado para fabrico final nem para voo.

Relacionado: [ANALISE_TERMICA_VOC_v0p6.md](ANALISE_TERMICA_VOC_v0p6.md), [VALIDACAO_DATASHEETS_v0p6.md](VALIDACAO_DATASHEETS_v0p6.md), [DECISAO_TVS_v0p7.md](DECISAO_TVS_v0p7.md).

## 1. Mudança de topologia

A v0.7 muda da topologia 2S1P (duas células em série) para **1S2P (duas células em paralelo)**. Decisão executada conforme orientação do líder de equipa do Porto Space (2026-05-28).

Justificação técnica (descobertas via leitura cruzada do EPS I User Manual, Tab. 12 e Secção "Solar Panel Channels"):

- O EPS I tem **Over-voltage protection** integrada que actua acima de 5.5 V (citação directa: "Over-voltage protection — engages above 5.5 V"). O limite 5.5 V **não é destrutivo absoluto** — é o trigger de OVP.
- O EPS I MPPT é **boost (step-up)**; UVLO ≈ `VF + 0.5 V` ≈ 0.78 V. Aceita tensões de entrada muito abaixo de 1 V.
- O `Voltage Min` para o canal solar na Tabela 12 é **0 V**.

Estas três descobertas tornam o paralelo eléctricamente atractivo e a anterior decisão de TVS na v0.6 ([DECISAO_TVS_v0p7.md](DECISAO_TVS_v0p7.md)) **obsoleta** — superseded pela nova topologia.

## 2. Parâmetros eléctricos comparados

| Parâmetro | 2S1P (v0.6) | 1S2P (v0.7) | Limite EPS | Veredicto |
|---|---|---|---|---|
| `Voc` por canal @ 25 °C BOL | 5.40 V | **2.70 V** | < 5.5 V (OVP) | ✅ |
| `Voc` cold extreme −80 °C BOL | 6.66 V | **3.33 V** | < 5.5 V (OVP) | ✅ OVP risk eliminado |
| `Vmp` @ 25 °C | 4.74 V | 2.37 V | > 0.78 V (UVLO) | ✅ |
| `Vin típica EPS` ref. | 4.66 V | 4.66 V | — | nota abaixo |
| `Isc` @ 25 °C | 0.56 A | 1.12 A | < 1.8 A | ✅ |
| `Imp` @ 25 °C | 0.54 A | 1.08 A | < 1.8 A | ✅ |
| `Pmp` @ 25 °C | 2.56 W | ≈ 2.65 W | — | ligeiramente maior |

Nota sobre `Vin típica` (4.66 V): é o ponto de operação típico **referenciado** pelo EPS, não um requisito. O EPS opera correctamente abaixo deste valor — usa o MPPT para encontrar o ponto óptimo dinamicamente. Operar a `Vmp_1S = 2.37 V` significa que o boost converter tem rácio mais alto (~1.7×), com pequena penalização de eficiência (boost típico é mais eficiente perto da unidade), mas funcionalmente correcto.

## 3. Topologia final (1S2P)

```
   ┌────────────── PANEL_POS ──────────────┐
   │                                        │
 SC1 (+, centro/verso, F.Cu)              SC2 (+, centro/verso, F.Cu)
 SC1 (−, tabs/frente, F.Cu)               SC2 (−, tabs/frente, F.Cu)
   │                                        │
   └────────────── PANEL_NEG ──────────────┘
```

Polaridade física da célula (TJ30SCA-4080V4, sem alteração de v0.6):
- Tabs da frente = NEG
- Centro/verso = POS

Mapa cell-pad → net:

| Célula | Pad físico | Pad # | Net |
|---|---|---:|---|
| SC1 | tabs (frente) | 1 | `PANEL_NEG` |
| SC1 | centro (verso) | 2 | `PANEL_POS` |
| SC2 | tabs (frente) | 1 | `PANEL_NEG` |
| SC2 | centro (verso) | 2 | `PANEL_POS` |

Note que ambas as células têm a mesma orientação eléctrica (tabs=NEG, centro=POS), simétrica entre si. Não há `SERIES_MID`.

## 4. Mudanças aplicadas

### 4.1 Schematic (`ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_sch`)
- Renomeado de v0p6 → v0p7.
- Labels globais `SERIES_MID` (3 instâncias) substituídas: 2 viraram `PANEL_POS` e `PANEL_NEG` respectivamente; 1 foi removida com `TP2`.
- Símbolo `TP2` (test point SERIES_MID) eliminado.
- Símbolo `D1` (TVS DNP) eliminado por completo (ver Secção 5).
- Texto descritivo da topologia actualizado para 1S2P.
- Fios órfãos das labels antigas do `D1` removidos.

### 4.2 PCB (`ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pcb`)
- Renomeado de v0p6 → v0p7.
- Net `SERIES_MID` removida.
- Pads `SC1` centro (3×): net `SERIES_MID` → `PANEL_POS`.
- Pads `SC2` tabs (4×): net `SERIES_MID` → `PANEL_NEG`.
- Footprint `TP2` removido.
- Footprint `D1` removido.
- Texto fp_text actualizado (`SC1+ = PANEL_POS`, `SC2- = PANEL_NEG`).
- **J1 (Molex) movido de `F.Cu` para `B.Cu`** (parte inferior, conforme orientação do líder). Toda a referência F.* trocada para B.* (pads, silk, fab, courtyard, justificações de texto com `mirror`).
- Routing completamente refeito:
  - `PANEL_NEG`: bus F.Cu superior (tabs SC1) + bus F.Cu inferior (tabs SC2) + corredor B.Cu vertical à esquerda (x=70) ligando os dois + ligação B.Cu ao J1 (pin 1/2). Test pads TP1/TP4 ligam aos buses F.Cu via stubs.
  - `PANEL_POS`: bus F.Cu local em cada cell (y=84.425 e y=125.575) + corredor B.Cu vertical à direita (x=137) ligando os dois + ligação B.Cu ao J1 (pin 3/4). Test pads TP3/TP5 ligam aos buses F.Cu.

### 4.3 Pinout J1
- Mantido: P1/P2 = PV−, P3/P4 = PV+ (conforme Tabela 8 do EPS I User Manual e foto do conector real fornecida pelo líder).
- "Dois negativos e dois positivos" cumprido pela duplicação de pins na mesma polaridade (paralelo no conector — distribui corrente, reduz queda).

## 5. Destino do `D1` (TVS DNP)

`D1` foi **removido por completo** na v0.7. Justificação:
- Topologia paralela elimina o risco de Voc OVP (`Voc_2S = 6.18 V` em frio era o motivo da v0.6 considerar TVS; `Voc_1S = 3.33 V` em frio fica muito abaixo dos 5.5 V).
- EPS I tem OVP integrada no canal solar (manual confirma).
- Manter `D1` como DNP só ocupa área e BOM sem função real.
- `DECISAO_TVS_v0p7.md` fica como histórico — superseded.

## 6. Estado dos relatórios (KiCad 10.0 Windows)

- ERC: `reports/ERC_v0p7_kicad10_win.rpt`
  - 0 erros eléctricos.
  - 3 warnings: bibliotecas locais não activadas no contexto CLI (same as v0.6, mesmo waiver).

- DRC parity: `reports/DRC_v0p7_kicad10_win.rpt`
  - **0 erros**.
  - **0 desconectados**.
  - **0 paridade**.
  - 11 warnings: 3x `lib_footprint_issues` (lib CLI) + 8x `lib_footprint_mismatch` (TestPoint, MountingHole — embedded standard libs). Mesma natureza dos waivers da v0.6, agora com 2 menos (D1 e TP2 foram removidos).

- Plots: `reports/v0p7_top.svg`, `reports/v0p7_bottom.svg`, `reports/v0p7_fab.svg`.

## 7. Bloqueadores antes de fabrico (inalterados de v0.6)

- Footprint Molex 53398-0471 ainda preliminar até comparação com drawing oficial e conector real.
- Footprint da célula ainda preliminar até validação com célula real / desenho de montagem CIC (AzurSpace).
- Validação mecânica 1U (board outline, padrão de furos, encaixe das células).
- Continuidade pino-a-pino do harness real.
- Revisão por segunda pessoa (Porto Space hardware lead).

## 8. O que muda no risco de design

| Risco v0.6 | Estado v0.7 |
|---|---|
| Voc cold OVP > 5.5 V | **Eliminado** (Voc_1S max ≈ 3.33 V) |
| TVS clamp dimensionamento | **Não aplicável** (D1 removido) |
| Resposta EnduroSat sobre 5.5 V destrutivo | **Não bloqueante** (OVP integrada confirmada na Tab. 12) |
| Eficiência MPPT a 2.37 V | **Novo risco menor** — boost converter opera fora do ponto típico 4.66 V; espera-se ~5–10 % de perda de eficiência adicional vs 2S |
| Corrente de barramento (Isc 1.12 A vs 1.8 A limite) | Margem reduzida mas ainda 38 % de folga |

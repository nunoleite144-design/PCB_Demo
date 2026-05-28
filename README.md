# PCB do Painel Solar 1U — ICARUS

Repositório de trabalho relativo ao desenho preliminar da PCB do painel solar 1U do projeto ICARUS, no âmbito da Porto Space Team.

Esta versão agrega o projeto KiCad, os footprints preliminares e a documentação técnica de apoio à revisão interna.

## Revisão atual

A revisão de trabalho actual é a **v0.7**:

- `hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_sch`
- `hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pcb`
- `hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pro`

Esta revisão adopta **1S2P (duas células em paralelo)** como topologia base, **move o conector Molex `J1` para a face inferior (`B.Cu`)** conforme orientação do líder de equipa, mantém os test pads `PANEL_NEG` e `PANEL_POS`, e mantém `BENCH_ONLY_PANEL_NEG`/`BENCH_ONLY_PANEL_POS` apenas como fallback de bancada. O `D1` (TVS DNP) e o `TP2` (SERIES_MID) da v0.6 foram **removidos** porque deixam de ser necessários em paralelo.

Estado desta revisão: **pré-validação física**. Não está aprovada para fabrico final nem para voo.

A v0.6 (2S1P série) permanece em histórico Git como branch `v0.6-internal-review` e em `main` antes do merge da v0.7.

## Estado atual

A configuração elétrica de referência é **1S2P** com duas células solares **TJ3xSCA-4080 Tipo 32** por painel, ligadas em paralelo.

Parâmetros de referência usados no dimensionamento:

| Parâmetro | Valor |
|---|---:|
| `Voc_cell` | 2.70 V @ 25 °C |
| `Vmp_cell` | 2.37 V @ 25 °C |
| `Jm` | 17.9 mA/cm² |
| Área ativa | 30.18 cm² |
| `dVoc/dT` | −6.0 mV/°C por célula |

Valores calculados para o painel 1S2P:

| Grandeza | Valor |
|---|---:|
| `Voc_panel` @ 25 °C BOL | 2.70 V |
| `Vmp_panel` @ 25 °C | 2.37 V |
| `Isc` @ 25 °C | ≈ 1.12 A |
| `Imp` @ 25 °C | ≈ 1.08 A |
| `Pmp` @ 25 °C | ≈ 2.65 W |

Ligação elétrica final (1S2P):

| Nó | Ligação |
|---|---|
| `SC1_NEG` (tabs/frente) | `PANEL_NEG` |
| `SC1_POS` (centro/verso) | `PANEL_POS` |
| `SC2_NEG` (tabs/frente) | `PANEL_NEG` |
| `SC2_POS` (centro/verso) | `PANEL_POS` |

Não existe net intermédia (`SERIES_MID` foi removida na transição 2S1P → 1S2P).

## Interface EPS I

O EPS usado é o **EnduroSat EPS I**. O pinout solar que deve ser respeitado no cabo/PCB é (Tabela 8 do EPS I User Manual):

| Pino do conector solar EPS I | Função |
|---:|---|
| 1 | PV− / negativo |
| 2 | PV− / negativo |
| 3 | PV+ / positivo |
| 4 | PV+ / positivo |

`J1` está montado em `B.Cu` (parte inferior da PCB) por instrução do líder de equipa, mantendo o pinout acima.

Limites relevantes por canal solar (Tabela 12 do EPS I User Manual):

| Limite | Valor | Notas |
|---|---:|---|
| Tensão mínima de entrada (boost UVLO) | ≈ 0.78 V | `VF_blocking_diode + 0.5 V` |
| Tensão típica de operação | 4.66 V | Ponto referenciado pelo EPS |
| Tensão máxima — OVP trigger | 5.5 V | EPS tem OVP **integrada** acima deste valor |
| Corrente máxima de entrada | 1.8 A | — |

A PCB e o harness devem ser verificados por continuidade antes de qualquer ligação ao EPS.

## Margens eléctricas (1S2P)

A transição para paralelo **elimina** o risco de sobretensão a frio que dominava a v0.6:

| Cenário | Valor 1S2P | vs limite 5.5 V (OVP trigger) |
|---|---:|---|
| `Voc_panel` @ 25 °C BOL | 2.70 V | margem 2.80 V |
| `Voc_panel` @ −40 °C BOL | 2.94 V | margem 2.56 V |
| `Voc_panel` @ −80 °C BOL (extremo) | 3.33 V | margem 2.17 V |

`Vmp` opera abaixo do ponto típico do EPS (2.37 V vs 4.66 V); o boost MPPT trabalha com rácio mais alto, com ligeira perda de eficiência mas funcionalmente correcto. UVLO ≈ 0.78 V está confortavelmente abaixo de `Vmp`.

Ver `docs/VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md` para a análise completa.

## Conteúdo do repositório

- `hardware/` — projeto KiCad da PCB (esquemático, PCB, projeto), símbolos `ICARUS.kicad_sym`, footprints (`footprints/ICARUS_Solar.pretty`, `footprints/ICARUS_Connector.pretty`) e tabelas de bibliotecas.
- `docs/VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md` — **documento de referência da v0.7**: justificação técnica, mapa de pads, mudanças aplicadas, estado dos relatórios.
- `docs/VALIDACAO_DATASHEETS_v0p6.md` — cruzamento do design com os datasheets oficiais (célula e EPS I); inclui actualização v0.7 sobre a OVP integrada.
- `docs/ANALISE_TERMICA_VOC_v0p6.md` — análise térmica de sobretensão (histórica, **superseded** pela mudança para 1S2P).
- `docs/DECISAO_TVS_v0p7.md` — decisão de clamp TVS (histórica, **superseded** — `D1` foi removido).
- `docs/CORRECAO_TOPOLOGIA_SERIE_v0p6.md` — correcção da anti-série da v0.6 (histórica).
- `docs/REVIEW_ACTIONS_v0p6.md` — resumo das ações da v0.6 (histórico).
- `docs/VALIDACAO_LOCAL_v0p6.md` — estado dos relatórios ERC/DRC da v0.6 (histórico).
- `docs/MECHANICAL_CELL_CLEARANCE_v0p6.md` — folga mecânica nominal entre células (continua válido em v0.7).
- `docs/CELL_UNDER_COPPER_RISK_v0p6.md` — auditoria de cobre sob as células da v0.6 (histórico parcial).
- `reports/` — relatórios ERC/DRC e plots SVG (top/bottom/fab) das revisões v0.6 e v0.7.
- `test_logs/LIBRARY_WARNINGS_WAIVER_v0p6.md` — waiver técnico dos warnings de biblioteca do ERC/DRC.
- `TJ30SCA-4080V4.pdf` — datasheet da célula solar TJ3xSCA-4080.
- `EPS_I_User_Manual.pdf` — manual do EnduroSat EPS I (limites de entrada solar e pinout do conector).

## Regra de fecho

Não considerar o desenho **flight-ready** nem **fabrication-ready** sem:

- ERC/DRC oficial no KiCad GUI (CLI já corrido e limpo);
- desenho de montagem CIC da AzurSpace (geometria das tabs e contacto de verso);
- drawing oficial do Molex 53398-0471 para validar footprint final;
- validação mecânica com estrutura e células reais;
- validação do harness por continuidade;
- testes elétricos com carga antes de ligação ao EPS;
- revisão por pelo menos uma segunda pessoa.

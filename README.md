# PCB do Painel Solar 1U — ICARUS

Repositório de trabalho relativo ao desenho preliminar da PCB do painel solar 1U do projeto ICARUS, no âmbito da Porto Space Team.

Esta versão agrega o projeto KiCad, os footprints preliminares e a documentação técnica de apoio à revisão interna.

## Revisão atual

A revisão de trabalho para revisão técnica interna é a **v0.6-internal-review**:

- `hardware/ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_sch`
- `hardware/ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_pcb`
- `hardware/ICARUS_1U_Solar_Panel_PCB_v0p6.kicad_pro`

Esta revisão mantém 2S1P como baseline, corrige a polaridade lógica de `SC2`, elimina a sobreposição nominal entre os corpos das células, substitui o conector placeholder por um footprint preliminar Molex 53398-0471, mantém test pads para `PANEL_NEG`, `SERIES_MID` e `PANEL_POS`, e adiciona `BENCH_NEG`/`BENCH_POS` apenas como fallback de bancada.

Estado desta revisão: **pré-validação física**. Não está aprovada para fabrico final nem para voo.

## Estado atual

A configuração elétrica de referência é **2S1P** com duas células solares **TJ3xSCA-4080 Tipo 32** por painel.

Parâmetros de referência usados no dimensionamento:

| Parâmetro | Valor |
|---|---:|
| `Voc_cell` | 2.70 V @ 25 °C |
| `Vmp_cell` | 2.37 V @ 25 °C |
| `Jm` | 17.9 mA/cm² |
| Área ativa | 30.18 cm² |
| `dVoc/dT` | −6.0 mV/°C por célula |

Valores calculados para o painel 2S1P:

| Grandeza | Valor |
|---|---:|
| `Voc_2S` | 5.40 V @ 25 °C |
| `Vmp_2S` | 4.74 V |
| `Imp` | ≈ 0.54 A |
| `Pmp` | ≈ 2.56 W |

Ligação elétrica final da string:

| Nó | Ligação |
|---|---|
| `SC1_NEG` | `PANEL_NEG` |
| `SC1_POS` | `SERIES_MID` |
| `SC2_NEG` | `SERIES_MID` |
| `SC2_POS` | `PANEL_POS` |

## Interface EPS I

O EPS usado é o **EnduroSat EPS I**. O pinout solar que deve ser respeitado no cabo/PCB é:

| Pino do conector solar EPS I | Função |
|---:|---|
| 1 | PV− / negativo |
| 2 | PV− / negativo |
| 3 | PV+ / positivo |
| 4 | PV+ / positivo |

Limites relevantes por canal solar:

| Limite | Valor |
|---|---:|
| Tensão máxima de entrada | 5.5 V |
| Corrente máxima de entrada | 1.8 A |

A PCB e o harness devem ser verificados por continuidade antes de qualquer ligação ao EPS.

## Risco principal

A configuração 2S1P está bem casada com a entrada típica do EPS (Vmp_2S ≈ 4.74 V vs Vin típica 4.66 V), mas corre perto do limite máximo de entrada. `Voc_2S` @ 25 °C BOL = **5.40 V**, apenas **0.10 V abaixo** do máximo de 5.5 V do EPS I. Com o coeficiente do datasheet (−6.0 mV/°C por célula, −12 mV/°C em 2S), o limite é atingido em circuito aberto a ≈ **16.7 °C**, e abaixo disso é excedido:

| Temp. da célula | `Voc_2S` (aberto, BOL) | vs 5.5 V |
|---:|---:|---|
| 16.7 °C | 5.50 V | no limite |
| 0 °C | 5.70 V | +0.20 V |
| −20 °C | 5.94 V | +0.44 V |
| −40 °C | 6.18 V | +0.68 V |

O pior caso é **BOL + frio + circuito aberto** (início de missão, bateria cheia/sem carga); a degradação por radiação baixa `Voc` ao longo da vida. **O manual do EPS I não documenta qualquer proteção de sobretensão na entrada solar**: os 5.5 V são o máximo da tabela elétrica e o díodo de bloqueio integrado (queda 0.275–0.325 V) fica a jusante do pino, não limitando `Voc`. Esta condição tem de ser confirmada com a EnduroSat (se 5.5 V é destrutivo) e mitigada se necessário (ex.: clamp/TVS dimensionado < 5.5 V; o `D1` atual é DNP). Ver `docs/VALIDACAO_DATASHEETS_v0p6.md`.

## Conteúdo do repositório

- `hardware/` — projeto KiCad da PCB (esquemático, PCB, projeto), símbolos `ICARUS.kicad_sym`, footprints e tabelas de bibliotecas.
- `docs/REVIEW_ACTIONS_v0p6.md` — resumo das ações da v0.6, riscos e validações pendentes.
- `docs/VALIDACAO_LOCAL_v0p6.md` — validação local da revisão v0.6 e estado real dos relatórios ERC/DRC.
- `docs/MECHANICAL_CELL_CLEARANCE_v0p6.md` — folga mecânica nominal entre células e distâncias principais.
- `docs/CELL_UNDER_COPPER_RISK_v0p6.md` — auditoria de cobre sob as células e riscos pendentes.
- `docs/VALIDACAO_DATASHEETS_v0p6.md` — cruzamento do design com os datasheets oficiais (célula e EPS I).
- `reports/` — relatórios ERC/DRC e plots SVG (top/bottom/fab) da v0.6.
- `test_logs/LIBRARY_WARNINGS_WAIVER_v0p6.md` — waiver técnico dos warnings de biblioteca do ERC/DRC.
- `TJ30SCA-4080V4.pdf` — datasheet da célula solar TJ3xSCA-4080.
- `EPS_I_User_Manual.pdf` — manual do EnduroSat EPS I (limites de entrada solar e pinout do conector).

## Regra de fecho

Não considerar o desenho **flight-ready** nem **fabrication-ready** sem:

- ERC/DRC oficial no KiCad;
- validação mecânica com estrutura e células reais;
- validação do harness por continuidade;
- testes elétricos com carga antes de ligação ao EPS;
- revisão por pelo menos uma segunda pessoa.

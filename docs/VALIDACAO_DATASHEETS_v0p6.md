# Validação contra datasheets oficiais — ICARUS v0.6

Data: 2026-05-28

Estado: revisão interna pré-validação física. Não aprovado para fabrico final nem para voo.

## Fontes

- Célula: `TJ30SCA-4080V4.pdf` — Triple-Junction GaAs Solar CIC TJ3xSCA-4080 (coluna **Tipo 32**).
- EPS: `EPS_I_User_Manual.pdf` — EnduroSat EPS I & EPS I Plus, versão 7.12 (abril 2024), HW 1.7 / FW 16.78.

## Parâmetros confirmados (design vs datasheet)

| Parâmetro no design | Fonte oficial | Veredicto |
|---|---|---|
| Voc 2.70 V; Vmp 2.37 V; Jm 17.9 mA/cm²; área 30.18 cm² | Célula, coluna Tipo 32, AM0 25 °C | Match exato |
| dVoc/dT = −6.0 mV/°C por célula | Célula, Temperature Gradients (Voc, Tipo 32) | Match exato |
| EPS Vin máx = 5.5 V por canal | EPS Tab. 12 (Solar Panels Input, Voltage Max) | Match exato |
| EPS Iin máx = 1.8 A por canal | EPS Tab. 12 (Solar Panels Input, Current Max) | Match exato |
| Conector Molex 53398-0471, mates 51021-0400 | EPS Tab. 9 (conectores solares P1–P6) | Match exato |
| Pinout 1/2 = PV−, 3/4 = PV+ | EPS Tab. 8 | Match exato |

Margens de corrente/potência:

- Imp ≈ 0.54 A; Isc ≈ 0.56 A (Jsc 18.7 mA/cm² × 30.18 cm²) << 1.8 A → margem ampla de corrente.
- Pmp_2S ≈ 2.56 W; Vin típica do EPS = 4.66 V vs Vmp_2S = 4.74 V → ponto de operação bem casado.

## Risco de sobretensão (quantificado)

`Voc_2S` @ 25 °C BOL = 5.40 V, a apenas 0.10 V do máximo de 5.5 V. Coeficiente 2S = −12 mV/°C.

| Temp. da célula | `Voc_2S` (aberto, BOL) | vs 5.5 V |
|---:|---:|---|
| 16.7 °C | 5.50 V | no limite |
| 0 °C | 5.70 V | +0.20 V |
| −20 °C | 5.94 V | +0.44 V |
| −40 °C | 6.18 V | +0.68 V |

- Pior caso: **BOL + frio + circuito aberto** (início de missão, bateria cheia / sem carga).
- A radiação degrada `Voc` ao longo da vida (EOL/BOL Voc = 0.91–0.96 conforme fluência), pelo que o pior caso é mesmo no BOL.
- O manual do EPS I **não documenta proteção de sobretensão na entrada solar**. O díodo de bloqueio integrado (Vf 0.275–0.325 V) fica a jusante do pino de entrada e não limita `Voc`.
- Nota de extrapolação: o coeficiente térmico do datasheet é caracterizado para 20–80 °C; os valores a frio são extrapolação linear conservadora.
- Ação pendente: confirmar com a EnduroSat se 5.5 V é máximo absoluto/destrutivo; avaliar clamp/TVS dimensionado < 5.5 V (o `D1` atual é DNP).

## Dados novos a integrar no design

- **Díodo de bloqueio por canal no EPS** → o painel não precisa de díodo de bloqueio próprio.
- **Díodo bypass integrado na célula** (Vf < 1.0 V @ 2.5 A) → proteção de sombreamento já existe ao nível da célula; o `D1` (TVS) serve apenas como clamp/transiente.
- **Geometria mecânica da célula disponível no datasheet** (corpo 40.15 × 80.15 mm; tabs cotadas; altura com tabs 46.98 mm; CIC 270/300 µm; coverglass 120 µm; interconnector prata/Kovar, pull > 1.6 N; absorção ≤ 0.89) → permite validar/corrigir o footprint e alimentar a análise térmica que define a gama real de temperatura do painel.

## Impacto nas ações da v0.6

- Conector: P/N validado → **removido da lista de riscos**.
- Footprint da célula: passa de "preliminar sem referência" a "corrigível contra desenho cotado".
- README: removida a afirmação infundada de proteção de sobretensão do EPS na entrada solar.
- Sobretensão: **promovida a risco de design prioritário**, com decisão de mitigação pendente.

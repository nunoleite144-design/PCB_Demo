# PCB do Painel Solar 1U — ICARUS

Repositório de trabalho relativo ao desenho preliminar da PCB do painel solar 1U do projeto ICARUS, no âmbito da Porto Space Team.

Esta versão agrega o projeto KiCad, os footprints preliminares e a documentação técnica de apoio à revisão interna.

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

A configuração 2S1P é compatível com a entrada típica do EPS em ponto de máxima potência, mas há risco de **`Voc_2S > 5.5 V` em frio**. Com o coeficiente térmico assumido, o limite de 5.5 V é atingido em circuito aberto a aproximadamente **16.7 °C**.

Isto não deve ser tratado como condição destrutiva, porque o EPS tem proteção de sobretensão, mas também não deve ser usado como modo nominal de operação. Se a proteção atuar, o canal solar pode deixar de colher energia temporariamente.

## Conteúdo do repositório

- `hardware/` — ficheiros KiCad da PCB.
- `docs/DESIGN_REVIEW_NOTES.md` — notas de revisão técnica.
- `docs/solar_panel_electrical_baseline.md` — baseline elétrico e cálculos.
- `docs/harness_pinout.md` — pinout EPS/PCB/harness e procedimento de continuidade.
- `docs/test_plan_cell_arrival.md` — plano de teste para chegada das células.
- `docs/release_checklist_v0p3.md` — checklist para considerar a PCB pronta para teste presencial.

## Regra de fecho

Não considerar o desenho **flight-ready** nem **fabrication-ready** sem:

- ERC/DRC oficial no KiCad;
- validação mecânica com estrutura e células reais;
- validação do harness por continuidade;
- testes elétricos com carga antes de ligação ao EPS;
- revisão por pelo menos uma segunda pessoa.

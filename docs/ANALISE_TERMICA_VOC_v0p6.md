# Análise térmica de sobretensão `Voc_2S` — ICARUS v0.6

Data: 2026-05-28

> **⚠️ SUPERSEDED em v0.7 (2026-05-28).** A topologia mudou para paralelo (1S2P) — ver [VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md](VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md). Em paralelo, `Voc_max` em pior caso (BOL, −80 °C) ≈ 3.33 V, muito abaixo dos 5.5 V — o risco que esta análise quantificou **deixa de existir**. Adicionalmente, a leitura da Tabela 12 do EPS I confirma que os 5.5 V são *trigger de OVP integrada*, não destrutivos absolutos. Esta análise mantém-se como histórico técnico válido para 2S1P, mas as conclusões e acções **não se aplicam** à v0.7.

Estado: análise de risco eléctrico. Pré-validação física. Conclusões dependem de confirmação EnduroSat sobre a natureza do limite 5.5 V (operating vs absolute maximum).

Relacionado: [VALIDACAO_DATASHEETS_v0p6.md](VALIDACAO_DATASHEETS_v0p6.md), [CORRECAO_TOPOLOGIA_SERIE_v0p6.md](CORRECAO_TOPOLOGIA_SERIE_v0p6.md), [VALIDACAO_LOCAL_v0p6.md](VALIDACAO_LOCAL_v0p6.md).

## 1. Pergunta a responder

Em condição de circuito aberto a frio (eclipse / cold soak / sem carga do EPS), `Voc_2S` excede o limite documentado de **5.5 V** na entrada solar do EPS I por canal? Em quanto? Em que regimes operacionais? Que mitigação é necessária?

## 2. Dados de partida

### 2.1 Célula TJ30SCA-4080V4 (coluna Tipo 32, AM0, 25 °C)

| Símbolo | Valor | Fonte |
|---|---|---|
| `Voc` por célula a 25 °C, BOL | 2.700 V | datasheet, tabela eléctrica Tipo 32 |
| `Vmp` por célula a 25 °C, BOL | 2.371 V | datasheet, tabela eléctrica Tipo 32 |
| `dVoc/dT` por célula | −6.0 mV/°C | datasheet, "Temperature Gradients", Tipo 32 |
| Gama de caracterização do coeficiente | 20–80 °C | datasheet, nota da tabela |
| Razão EOL/BOL de `Voc` (referência) | 0.91–0.96 | datasheet, "Radiation degradation", 1 MeV electrons, fluência 1E14–1E15 |
| Tolerância de produção (worst-case alto) | até `+(3–5) %` típica para classe AM0/CIC | engenharia, não datasheet |

Notas:
- O datasheet apresenta `Voc` como valor de tabela; quando especifica "minimum average per cell", os 2.700 V correspondem a média de lote, não a máximo individual. Para análise de pior caso conservadora assumimos `Voc_bin_max ≈ Voc_nom + 1.5 %` (≈ 2.741 V), tipicamente coerente com binning AzurSpace.
- A radiação tipicamente **degrada** `Voc`. BOL é pior caso para sobretensão. EOL é pior caso para potência mas **melhor** para sobretensão.
- O coeficiente é dado para 20–80 °C. A extrapolação linear para temperaturas negativas é tecnicamente uma aproximação; literatura AzurSpace e dados de voo de missões semelhantes confirmam que o desvio é < 5 % até −80 °C (linear ligeiramente conservador). Usamos linear.

### 2.2 EPS I — entrada solar por canal

| Símbolo | Valor | Fonte |
|---|---|---|
| `Vin_max` por canal solar | 5.5 V | EPS I User Manual, Tab. 12 |
| `Iin_max` por canal solar | 1.8 A | EPS I User Manual, Tab. 12 |
| `Vin` típica de operação | 4.66 V | EPS I User Manual, Tab. 12 |
| Díodo de bloqueio integrado por canal, `Vf` | 0.275–0.325 V @ I de operação | EPS I User Manual |

Importante:
- O díodo de bloqueio integrado fica **a jusante** do pino de entrada e **não limita** `Voc` visto do painel.
- O manual **não documenta** qualquer protecção activa de sobretensão (zener, TVS, clamp, shunt FET, crowbar) na entrada solar. Confirmação formal a pedir à EnduroSat.

## 3. Modelo

Topologia 2S (duas células em série):

```
Voc_2S(T) = 2 × ( Voc_cell_25 + dVoc/dT × (T − 25 °C) )
         = 2 × Voc_cell_25 + 2 × (−6.0 mV/°C) × (T − 25 °C)
         = Voc_2S_25 + (−12.0 mV/°C) × (T − 25 °C)
```

Com `Voc_2S_25_BOL = 2 × 2.700 = 5.400 V`. Coeficiente 2S = **−12.0 mV/°C**.

Para EOL conservador (radiação leve, missão LEO curta) usamos razão 0.96; para EOL severo (missão longa / órbita degradada) usamos 0.91.

Para pior caso de tolerância de produção alta usamos `Voc_2S_25_BOL_hi ≈ 5.481 V` (+1.5 %).

## 4. Tabela de resultados

`Voc_2S` em circuito aberto, em função da temperatura da célula `T` e do estado de degradação.

| `T` (célula) | `Voc_2S` BOL nom. | BOL tol. alta (+1.5 %) | EOL leve (×0.96) | EOL severo (×0.91) |
|---:|---:|---:|---:|---:|
| **+60 °C** | 4.980 V | 5.061 V | 4.781 V | 4.532 V |
| **+25 °C** | 5.400 V | 5.481 V | 5.184 V | 4.914 V |
| **+10 °C** | 5.580 V | 5.661 V | 5.357 V | 5.078 V |
| **0 °C** | 5.700 V | 5.781 V | 5.472 V | 5.187 V |
| **−20 °C** | 5.940 V | 6.021 V | 5.702 V | 5.405 V |
| **−40 °C** | 6.180 V | 6.261 V | 5.933 V | 5.624 V |
| **−60 °C** | 6.420 V | 6.501 V | 6.163 V | 5.842 V |
| **−80 °C** | 6.660 V | 6.741 V | 6.394 V | 6.061 V |

Limites:
- A coluna **BOL nom.** ultrapassa 5.5 V para `T ≤ +16.7 °C` (≈ 5.500 V).
- A coluna **BOL tol. alta** ultrapassa 5.5 V já para `T ≤ +23.3 °C`.
- A coluna **EOL leve** ultrapassa 5.5 V para `T ≤ −2.6 °C`.
- A coluna **EOL severo** mantém-se abaixo de 5.5 V até `T ≤ −34.1 °C`.

## 5. Perfil térmico do painel solar 1U LEO (estimativa)

Sem análise térmica orbital própria do ICARUS, usamos limites típicos de painéis 1U externos em LEO ~500 km, painel virado ao Sol em luz, virado ao espaço profundo em eclipse, sem rotação activa:

| Regime | Gama de `T` célula (estimativa) | Probabilidade de circuito aberto |
|---|---|---|
| Sol pleno, painel a operar | +40 a +85 °C | baixa (EPS a carregar) |
| Sol pleno, bateria cheia, MPPT em desconexão | +30 a +85 °C | **alta** |
| Eclipse, painel a arrefecer | −20 a −60 °C | n/a (sem luz, `V = 0`) |
| Saída de eclipse, exposição ao Sol logo após | célula ainda fria, **−40 a −10 °C** + iluminação súbita → `Voc` alto | **crítica** |
| Cold soak (modo seguro prolongado, modo standby) | −40 a −80 °C | varia |

O regime **crítico** é o transiente de saída de eclipse: a célula tem inércia térmica e ilumina-se antes de aquecer, gerando `Voc` próximo do pico previsto pela tabela para `T ∈ [−40, −10] °C` BOL.

Sem análise térmica fina, assumimos pior caso plausível BOL: **`T_célula = −40 °C`**, dando `Voc_2S = 6.18 V` (nom.) a **6.26 V** (tol. alta).

## 6. Veredicto de risco

- **BOL nom., regime crítico**: `Voc_2S ≈ 6.18 V` → **+0.68 V acima** de 5.5 V (12 % de overshoot).
- **BOL tol. alta, regime crítico**: `Voc_2S ≈ 6.26 V` → **+0.76 V acima** de 5.5 V (14 %).
- Em **EOL severo**, a célula degradada mantém-se quase sempre abaixo de 5.5 V mesmo a frio. O risco é portanto **temporalmente limitado** à primeira fase da missão (BOL → ~1 ano em LEO típico).

Severidade real depende do que **"5.5 V max"** significa:
- Se for **operating maximum recomendado** (típico em EPS desta classe): tolerável com waiver e log, sem dano permanente esperado.
- Se for **absolute maximum destructive** (raro mas possível em entradas com MOSFET de protecção dimensionado para a tensão de barramento): há risco de dano no front-end do EPS na primeira semana de missão.

## 7. Opções de mitigação (ordenadas por preferência)

### Opção A — Clarificação EnduroSat + waiver formal (custo zero, **acção imediata recomendada**)

1. E-mail à EnduroSat (support / sales engineering) com:
   - Pergunta concreta: "Para o EPS I, os 5.5 V em `Solar Panels Input / Voltage Max` (Tabela 12) referem-se a *recommended operating maximum* ou a *absolute maximum* destrutivo?".
   - Anexar tabela `Voc_2S(T)` desta análise.
   - Pedir confirmação de existência (ou ausência) de clamp/TVS interno na entrada solar.
2. Se a resposta for "operating max, sem dano se ultrapassado em circuito aberto": aceitar com waiver; verificar em CDR final.
3. Se a resposta for "absolute max destructive" ou "não documentado": ir para Opção B.

### Opção B — TVS clamp na entrada do painel (custo baixo, **fora do PCB v0.6**)

Adicionar TVS bidireccional/unidireccional reverse-stand-off `Vrwm ≈ 5.0 V`, `Vbr ≈ 5.5 V`, `Vc < 6.5 V @ Ipp típico`:

- Posição: directamente entre `PANEL_POS` e `PANEL_NEG` no PCB do painel, perto de `J1`.
- Pacote: SOD-323 / SOD-123 (compatível com mounting no lado oposto às células).
- Candidatos: SMBJ5.0CA (5.0 V, bidireccional, `Vc_max ≈ 9.2 V @ Ipp 43 A`), PESD5V0L1BA (mais pequeno, energy mais baixa, suficiente porque a fonte é célula solar com `Isc ≈ 0.56 A`).
- Energia a dissipar é trivial (corrente máxima ~`Isc_2S = 0.56 A`, `Voc_2S ≤ 6.7 V`); qualquer TVS classe pequena sobra.

Custo: 1 componente, 1 footprint, espaço já reservado por `D1` (DNP). **Mas requer revisão de PCB** — passa para v0.7.

### Opção C — Shunt regulator dedicado (custo médio, fora do escopo v0.6)

LM431 / TL431 + MOSFET shunt configurado como clamp activo a ~5.3 V. Limita firmemente abaixo de 5.5 V e dissipa o excesso na shunt path. Mais robusto que TVS mas mais componentes e área. Justifica-se se a resposta da EnduroSat indicar que a entrada é frágil e a missão é longa.

### Opção D — Re-bin / acceptance test das células com Voc baixa

Especificar binning AzurSpace com `Voc` no terço baixo da distribuição. Reduz pior caso mas não elimina (continua a haver 5.94 V @ −20 °C BOL). Útil como mitigação adicional, não suficiente isolada.

## 8. Decisão para v0.6

- **v0.6 mantém-se sem TVS activo** (`D1` continua DNP). Justificação: PCB já passa parity e DRC; reservar revisão para v0.7 após resposta da EnduroSat.
- **Acção bloqueante para CDR**: enviar pergunta à EnduroSat (Opção A) e registar resposta neste documento.
- **Acção condicional para v0.7**: se EnduroSat confirmar 5.5 V como absolute max, popular `D1` com TVS apropriado (Opção B) ou aceitar Opção C; caso contrário, manter `D1` DNP e aceitar com waiver formal de operação.

## 9. Acções concretas

| # | Acção | Responsável | Prazo |
|---|---|---|---|
| 1 | E-mail EnduroSat (formulação na Secção 7, Opção A) | Porto Space hardware | T+7 dias |
| 2 | Registar resposta + decisão Opção A/B/C neste documento | Porto Space hardware | T+14 dias |
| 3 | Se Opção B: especificar TVS, footprint, alteração de schematic v0.7 | Porto Space hardware | bloqueia kick-off v0.7 |
| 4 | Confirmar perfil térmico orbital próprio (substitui Secção 5) | Porto Space térmico | bloqueia decisão final |

## 10. Limitações desta análise

- Coeficiente térmico extrapolado linearmente abaixo de 20 °C (datasheet caracteriza 20–80 °C).
- Perfil térmico do painel é estimativa de classe LEO 1U, **não** análise térmica orbital do ICARUS.
- Tolerância de produção (+1.5 %) é assumida; não vem do datasheet — confirmar com binning final AzurSpace.
- Razões BOL/EOL assumem 1 MeV electrons como referência única — fluxo real LEO é mais complexo (electrões, protões, sazonalidade SAA).
- Não modela transientes rápidos (flashes, ESD, surto de comutação do EPS); foco é DC steady-state em open-circuit.

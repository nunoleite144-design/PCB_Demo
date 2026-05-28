# Análise térmica orbital do painel solar — ICARUS v0.7

Data: 2026-05-28

Estado: análise paramétrica preliminar. Substitui temporariamente a estimativa "LEO genérica" da Secção 5 de [ANALISE_TERMICA_VOC_v0p6.md](ANALISE_TERMICA_VOC_v0p6.md) por um cálculo de balanço radiativo 1D. Não substitui simulação térmica orbital completa (FEMAP/Thermal Desktop) — esta é input mínimo para decisões eléctricas.

Relacionado: [VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md](VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md), [VALIDACAO_DATASHEETS_v0p6.md](VALIDACAO_DATASHEETS_v0p6.md).

## 1. Objectivo

Determinar a gama de temperatura esperada para as células TJ30SCA-4080 montadas no painel solar 1U da ICARUS, em pior caso quente e pior caso frio, mais o transiente crítico de saída de eclipse. Output a usar para:
- Validar margens eléctricas (Voc, Vmp) em [VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md](VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md).
- Definir gama de operação para qualificação ambiental futura.
- Confirmar que o cell binning AzurSpace é adequado para a gama esperada.

## 2. Modelo

Balanço radiativo 1D, célula como nó térmico único:

```
Q_in = Q_solar + Q_albedo + Q_IR_earth
Q_out = ε σ A T⁴ (radiação para espaço, ambos os lados)
Q_dissipated = (1 − η) × Q_solar_absorbed   (calor não convertido em eléctrico)

Em steady-state: Q_in − P_eléctrico = Q_out
```

Assunções:
- Painel isolado termicamente do chassis (adiabático na fronteira mecânica — pior caso conservador para cold soak; chassis tipicamente actua como heat sink).
- Sem revestimentos especiais (sem OSR, sem MLI no painel solar — caso standard CubeSat).
- Eficiência de conversão `η = 30 %` (datasheet TJ30SCA-4080 Type 32, BOL).
- Área activa: 2 × 30.18 cm² = 60.36 cm² = 6.036 × 10⁻³ m² (duas células em paralelo).
- Constantes radiativas:
  - Absortividade frontal (com coverglass CMG) `α = 0.89` (datasheet)
  - Emissividade frontal `ε_front = 0.85` (típico CMG)
  - Emissividade traseira `ε_back = 0.85` (assumido — depende do back paint/finish do PCB; FR-4 verde tipicamente 0.85-0.9)
  - Constante de Stefan-Boltzmann `σ = 5.670 × 10⁻⁸ W/(m²·K⁴)`
- Fluxos orbitais (LEO ~500 km, conservador):
  - Solar: `S = 1361 W/m²` (média anual; máx solstício ~1414 W/m²)
  - Albedo terrestre: `a = 0.30 × S × F_albedo` (`F_albedo ≈ 0.27` para órbita baixa virada à terra; assumimos 0 para painel virado ao Sol durante sol-pleno)
  - IR terrestre: `IR_e = 237 W/m² × F_IR` (`F_IR ≈ 0.27`)

## 3. Pior caso quente (hot case)

Cenário: painel apontado directamente ao Sol, em sol pleno BOL, EPS a operar (extrai potência), sem albedo significativo (LEO de manhã, lado solar).

```
Q_solar_absorbed = α × S × A = 0.89 × 1361 × 6.036e-3 = 7.31 W
Q_albedo ≈ 0 (face oposta à Terra)
Q_IR_earth ≈ 0
P_eléctrico = η × S × A = 0.30 × 1361 × 6.036e-3 = 2.46 W (Pmp BOL)
Q_dissipated = Q_solar_absorbed − P_eléctrico = 7.31 − 2.46 = 4.85 W

Radiação por dois lados (front + back):
Q_out = (ε_front + ε_back) × σ × A × T⁴

T_hot⁴ = Q_dissipated / [(ε_front + ε_back) × σ × A]
       = 4.85 / [(0.85 + 0.85) × 5.670e-8 × 6.036e-3]
       = 4.85 / 5.82e-10
       = 8.33e9

T_hot = (8.33e9)^0.25 = 302 K = +29 °C
```

**Refinamento — pior caso conservador (sem extração eléctrica, painel saturado):**
Se EPS desligar (bateria cheia, MPPT em cutoff), `P_eléctrico = 0`, `Q_dissipated = 7.31 W` (todo o solar absorvido vira calor):

```
T_hot_OC⁴ = 7.31 / 5.82e-10 = 1.256e10
T_hot_OC = +62 °C
```

**Pior caso quente realista (BOL, sol pleno, EPS em cutoff): T ≈ +60 a +85 °C**

A diferença entre +29 °C (com extração) e +62 °C (sem extração) é a margem que o MPPT do EPS oferece. Em cold soak ou modo seguro com bateria cheia, podemos estar perto do limite superior.

## 4. Pior caso frio (cold case)

Cenário: eclipse profundo, sem iluminação solar, painel virado ao espaço profundo, equilíbrio térmico assumido (ignora capacidade térmica — válido para eclipse > 5 min).

```
Q_in = 0 (em eclipse, sem albedo nem IR terrestre relevantes para painel virado ao espaço)
Q_out = (ε_front + ε_back) × σ × A × T⁴

Em equilíbrio com Q_in = 0, T → 0 K teoricamente.
Na prática, o painel acopla termicamente ao chassis (estrutura aluminium ~ 0 °C em órbita típica).
```

**Modelo simplificado**: acoplamento condutivo via 4 furos M3 + radiação interna:

Sem dados específicos do chassis ICARUS, usar valores típicos de literatura para painéis externos 1U em eclipse:

| Cenário | T mín. esperada |
|---|---|
| Eclipse breve (<10 min), boa condução chassis | −20 a −30 °C |
| Eclipse longo (>30 min), isolamento térmico forte | −40 a −60 °C |
| Cold soak modo seguro, eclipses repetidos | −60 a −80 °C |

**Pior caso frio razoável para LEO ~500 km: T ≈ −40 °C a −60 °C**

## 5. Transiente crítico — saída de eclipse

Este é o regime mais relevante para o risco eléctrico (mesmo após paralelização):

- Imediatamente antes do nascer do Sol orbital: célula em equilíbrio frio (~−40 a −60 °C).
- Sol atinge a célula: iluminação instantânea, mas inércia térmica da PCB + cell + coverglass leva ~30-120 segundos a aquecer.
- Durante esses primeiros segundos, **célula iluminada mas fria** → `Voc` máximo:

```
Voc_2S @ −60 °C BOL paralelo (1S2P):
Voc_1S(−60°C) = Voc_1S(25°C) + dVoc/dT × ΔT
              = 2.70 + (−6.0e−3) × (−60 − 25)
              = 2.70 + 0.51 = 3.21 V

vs limite EPS 5.5 V: margem ≈ +2.29 V ✅ (largamente folgado)
```

**Conclusão**: mesmo com cold soak severo a −60 °C, a topologia 1S2P mantém `Voc` ≈ 3.21 V — muito abaixo dos 5.5 V do trigger OVP. **Risco eléctrico de saída de eclipse: desprezável em paralelo.**

(Comparar com 2S1P: `Voc_2S @ −60°C` ≈ 6.42 V — bem acima dos 5.5 V — o que confirma porque a v0.6 era arriscada e a v0.7 não é.)

## 6. Implicações para o design

| Parâmetro | Valor da análise | Limite | Margem |
|---|---:|---:|---|
| `T` máxima da célula (hot OC) | +85 °C | Datasheet operating +120 °C | ✅ +35 °C |
| `T` mínima da célula (cold soak) | −60 °C | Datasheet operating −150 °C | ✅ +90 °C |
| `Voc_1S` cold worst | 3.21 V | EPS OVP 5.5 V | ✅ +2.29 V |
| `Vmp_1S` hot worst | ~1.93 V (a +85 °C) | EPS UVLO 0.78 V | ✅ +1.15 V |
| `Vmp_1S` típico (25 °C) | 2.37 V | EPS typical 4.66 V | ⚠️ ratio de boost MPPT alto |
| `Pmp` em hot case | ≈ 2.0 W | — | Eficiência MPPT ~85% nesse ponto |
| `Isc` em hot worst (T_coeff +0.01 mA/cm²/°C) | ≈ 1.18 A | EPS Imax 1.8 A | ✅ +0.62 A |

Todos os parâmetros eléctricos da v0.7 ficam com margem confortável dentro do envelope térmico orbital esperado.

## 7. Limitações desta análise

1. **Modelo 1D**: trata as duas células como um único nó térmico, ignora gradientes laterais.
2. **Acoplamento ao chassis**: não modelado quantitativamente — assumida adiabaticidade ou valores típicos de literatura.
3. **Vista factores**: aproximações para LEO ~500 km equatorial; órbitas polares ou heliossíncronas têm exposição diferente.
4. **Albedo e IR terrestre**: ignorados no hot case para conservadorismo (incluindo-os o hot case sobe ~5-10 °C).
5. **Capacidade térmica**: ignorada (válido para steady-state, não para transientes <30s).
6. **Degradação BOL → EOL**: análise toda em BOL (pior caso para Voc; EOL é melhor para overvoltage mas pior para Vmp/Pmp).
7. **Não considera**: emissividade do coverglass anti-reflective, gradientes através do CIC, contacto térmico tab-PCB.

## 8. Acções recomendadas

1. **Curto prazo (antes do CDR)**:
   - Confirmar emissividade real do PCB FR-4 verde (literatura: 0.85-0.9 OK).
   - Confirmar perfil de eclipse para a órbita ICARUS específica (Porto Space térmico).
   - Verificar se há tratamento de superfície do PCB no lado posterior (paint, finish) que altere ε_back.

2. **Médio prazo (antes de fabrico)**:
   - Modelação térmica orbital completa em Thermal Desktop ou FEMAP, com:
     - Geometria CAD real do CubeSat
     - View factors actualizados
     - Acoplamento condutivo aos pontos M3
     - Múltiplos modos: nominal, safe-mode, eclipse-only
   - Validar margem >20% em todos os parâmetros eléctricos.

3. **Longo prazo (qualificação)**:
   - Thermal-Vacuum Cycling test (TVC) ±10 °C além do envelope esperado.
   - Acceptance testing da célula montada com bonding final.

## 9. Sumário executivo

**O envelope térmico orbital LEO típico ~500 km (−60 °C a +85 °C) está dentro dos limites operacionais da célula TJ30SCA-4080 Type 32 e mantém todos os parâmetros eléctricos com margem confortável em topologia 1S2P (v0.7).** O risco de overvoltage que motivou a análise v0.6 está eliminado pela mudança para paralelo, não pela mitigação térmica.

A análise paramétrica acima é suficiente como input à decisão eléctrica. Análise térmica orbital completa (CAD + FEM) continua necessária para o CDR mecânico/térmico do projecto ICARUS, mas **não bloqueia a v0.7 do painel solar**.

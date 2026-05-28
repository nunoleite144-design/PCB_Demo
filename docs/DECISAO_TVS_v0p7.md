# Decisão: popular `D1` com TVS na v0.7 — ICARUS

Data: 2026-05-28

> **⚠️ SUPERSEDED em v0.7 (2026-05-28, mais tarde no mesmo dia).** Mudança de topologia para paralelo (1S2P) — ver [VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md](VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md). Em paralelo, `Voc_max` cold ≈ 3.33 V, OVP não é necessária e o EPS já tem protecção integrada acima de 5.5 V. **`D1` foi removido por completo na v0.7**; o clamp TL431+MOSFET não é implementado. Este documento mantém-se como histórico do trade-off antes da mudança de topologia.

Estado: decisão de engenharia para a próxima revisão (v0.7). Aplica-se ao próximo PCB; v0.6 mantém-se como release de revisão interna sem alteração de BOM.

Relacionado: [ANALISE_TERMICA_VOC_v0p6.md](ANALISE_TERMICA_VOC_v0p6.md), [VALIDACAO_DATASHEETS_v0p6.md](VALIDACAO_DATASHEETS_v0p6.md).

## 1. Contexto

A `ANALISE_TERMICA_VOC_v0p6.md` quantificou que `Voc_2S` em pior caso (BOL, frio, circuito aberto, transiente pós-eclipse) chega a **6.18–6.26 V**, contra o limite de 5.5 V declarado para a entrada solar do EPS I por canal (Tab. 12 do manual).

A análise propôs três caminhos:
- A — clarificar com a EnduroSat se 5.5 V é operating ou absolute max
- B — popular `D1` com TVS clamp dimensionado < 5.5 V
- C — shunt regulator activo

## 2. Decisão

**Adoptar Opção B na v0.7.** `D1` deixa de ser DNP e passa a TVS bidireccional / unidireccional reverse-stand-off.

### Justificação

- Resposta da EnduroSat tipicamente demora semanas e atrasa o ciclo do projecto.
- Postura conservadora para flight hardware: **na ausência de confirmação documental contrária, tratar 5.5 V como destrutivo**. Custo do erro é a perda do EPS na primeira semana de missão.
- O custo do TVS é desprezável: 1 componente SMD, footprint `D_SOD-323` já reservado em `D1`, BOM cost < 1 €, área PCB < 5 mm².
- Mesmo que a EnduroSat venha mais tarde confirmar "operating max", o TVS não introduz risco — fica em alta impedância em condições nominais (`Vrwm > Vmp_2S = 4.74 V`).
- Estamos em BOL durante toda a primeira fase da missão, que é precisamente quando o risco existe. EOL severo (cell degradada) mitiga sozinho mas só ao fim de meses/anos.

## 3. Especificação do TVS

| Parâmetro | Requisito | Justificação |
|---|---|---|
| Topologia | Unidireccional | Painel só produz tensão positiva (PANEL_POS > PANEL_NEG); bidireccional desperdiça energia em reverse-leakage. |
| `Vrwm` (Reverse stand-off) | ≥ 4.8 V | Acima de `Vmp_2S = 4.74 V` para não conduzir em operação normal. |
| `Vbr_min` | ≥ 5.0 V | Margem ao Vmp para evitar leakage térmico. |
| `Vbr_max` | ≤ 5.4 V | Abaixo de 5.5 V para garantir clamp efectivo antes do limite EPS. |
| `Vc_max @ Ipp` | ≤ 6.0 V | Clamp em sobre-corrente (sem real risco aqui — `Isc_2S = 0.56 A` máx). Margem confortável vs limite EPS. |
| Pacote | SOD-323 (footprint actual de `D1`) | Sem alteração de layout. |
| Polaridade | Cátodo → PANEL_POS, Ânodo → PANEL_NEG | Standard reverse clamp. |
| Capacitância | < 200 pF | Irrelevante para DC; sem requisito RF. |
| Qualificação | Comercial-grade aceitável para v0.7 (revisão); upgrade para AEC-Q101 ou MIL-class antes de flight. | Risco mecânico/radiação é avaliado em revisão futura. |

### Candidatos concretos

| Part Number | `Vrwm` | `Vbr` | `Vc_max @ Ipp` | Pacote | Notas |
|---|---|---|---|---|---|
| **SMAJ5.0A** (Vishay) | 5.0 V | 6.40–7.07 V | 9.2 V | SMA | ❌ `Vbr` alto demais (6.4–7.0 V); só cortaria > 7 V, não protege. |
| **PESD5V0L1BA** (NXP) | 5.0 V | 6.0 V (typ) | 9.0 V | SOD-323 | ❌ Mesma razão; é ESD, não clamp DC. |
| **SMBJ5.0A** (Diodes Inc) | 5.0 V | 6.40–7.07 V | 9.2 V | SMB | ❌ Idem. |
| **TVS clamp 5.0 V de classe "low-clamping"** | — | — | — | — | Datasheet típico tem `Vbr` ~10–15 % acima de `Vrwm`; com `Vrwm = 5.0 V`, `Vbr` ≈ 5.5–5.75 V → ainda muito justo. |
| **Zener de precisão + MOSFET shunt** | — | 5.1 V Zener (`BZX84-C5V1`) | clamp activo a ~5.3 V | SOT-23 | ✅ Permite ajustar ponto de clamp; mais robusto. |

**Conclusão técnica**: o catálogo standard de TVS de 5.0 V não atinge a janela aperta de **[5.0, 5.4] V** que esta aplicação exige. Há um trade-off real:

- **Opção B1 (TVS simples, p.ex. SMBJ5.0A)**: protege mas só a partir de ~7 V → não cobre os 6.2 V do pior caso.
- **Opção B2 (Zener 5.1 V + N-MOSFET shunt)**: requer 2 componentes mas clamp efectivo a 5.3 V. Recomendado.
- **Opção B3 (TVS especial "low-clamping" — Nexperia PESD2CAN, ProTek SLVU2.8, etc.)**: pesquisar família com `Vbr ≤ 5.4 V` específica.

### Decisão de componente para v0.7

Adoptar **B2 com TL431** (precisão ±1 %, ver tabela detalhada na alternativa abaixo). Substituir `D1` actual (TVS DNP em SOD-323) por:

- **U1**: TL431AFTA em SOT-23 (referência precisão 2.495 V ±1 %)
- **Q1**: N-MOSFET BSS138 em SOT-23 (shunt de potência)
- **R1**: 10 kΩ 0603 ±1 % (divisor superior)
- **R2**: 8.87 kΩ 0603 ±1 % E96 (divisor inferior — fixa clamp em 5.30 V)
- **R3**: 100 Ω 0603 (limitação de corrente cátodo → gate)

Topologia: divisor `R1/R2` em `PANEL_POS` produz `Vref = 2.495 V` quando `V_PANEL = 5.30 V`. Acima disso, o cátodo do TL431 sink corrente, puxa gate de Q1 para terra → Q1 conduz e faz shunt do excesso entre `PANEL_POS` e `PANEL_NEG`.

Footprint changes vs v0.6:
- Remover `D1` (SOD-323 DNP).
- Adicionar `U1` (SOT-23), `Q1` (SOT-23), `R1`, `R2`, `R3` (3× 0603) ≈ 8 × 5 mm.
- Re-layout local em torno da posição actual de `D1` (120, 138) — a área disponível acomoda.

**Variante fallback (mais simples mas menos preciso)** — se houver pressão de área/BOM cost:
- **Z1**: Zener BZX84-C5V1 em SOT-23 (`Vz = 5.1 V`, tol. ±5 %)
- **Q1**: BSS138 SOT-23
- **R1**: 1 kΩ 0603
- Clamp em ≈ 5.1 V + Vgs(th) ≈ 5.9 V, com tolerância ±0.25 V do Zener. **Atenção**: janela apertada (5.0 V Vmp a 5.5 V EPS), tolerância pode comprometer o clamp. Só usar se TL431 for inviável.

**Alternativa single-part investigada e descartada (2026-05-28):**
- Bourns CDSOD323-T05L (SOD-323): `Vrwm = 5.0 V`, mas `Vbr_min = 6.0 V` → não clamp dentro do limite.
- TI TSDxx: variantes 3.6 V/4.5 V conduzem em MPPT (perde potência) ou 5.5 V/6.0 V não clamp.
- Nexperia PESDxL2BT: família ESD bidireccional, `Vbr` demasiado alto.
- Raiz do problema: TVS standard tem `Vbr ≈ 1.2 × Vrwm` mínimo. Para `Vrwm ≥ 4.8 V` (acima de `Vmp_2S = 4.74 V`), o `Vbr_min` cai para ≥ 5.8 V, fora da janela [5.0, 5.4 V] requerida.
- **Conclusão**: não existe TVS comercial single-part que cubra a janela. Clamp activo (Z1+Q1+R1, ou TL431+Q1+R1+R2) é a abordagem correcta.

**Variante recomendada — TL431 + MOSFET (preferível ao Zener+MOSFET):**

| Componente | Função | Referência sugerida |
|---|---|---|
| `U1` (TL431) | Referência precisão 2.495 V ±1 % | SOT-23, p.ex. Diodes Inc TL431AFTA |
| `R1` (10 kΩ) | Divisor superior, para `Vref = 2.495 V` corresponder a `Vclamp = 5.30 V` | 0603, ±1 % |
| `R2` (8.91 kΩ) | Divisor inferior (`R2 = R1 × Vref/(Vclamp−Vref) = 10k × 2.495/2.805`) | 0603, ±1 %, valor preferido 8.87 kΩ E96 |
| `Q1` | N-MOSFET small-signal a fazer shunt; `Vgs(th)` < 2 V | SOT-23, BSS138 ou DMN2300U |
| `R3` (100 Ω) | Limitação de corrente do cátodo do TL431 para a gate de Q1 | 0603 |

Vantagem vs Zener+MOSFET: precisão ±1 % do clamp em vez de ±5 % do Zener. Importante porque a janela [5.0, 5.4] V é apertada — tolerância larga do Zener (5.1 V ±5 % = 4.85–5.36 V) pode fechar a janela. TL431 fixa o clamp em 5.30 V ±0.05 V.

Espaço ocupado: 5 footprints SMD (TL431 SOT-23 + 3 resistors 0603 + 1 MOSFET SOT-23) ≈ 8 × 5 mm. Mais que o D1 actual SOD-323, mas área PCB tem espaço (canto de D1 actual + zona adjacente).

**AzurSpace / fornecedor espacial:** se preferes TVS qualificado, é praticamente impossível ter um na janela apertada — a recomendação dos fabricantes de células será tipicamente "use shunt regulator activo".

## 4. Trabalho concreto para v0.7

1. Re-spec `D1` como acima (Z1+Q1+R1 ou TVS low-clamping a confirmar).
2. Re-routing local em torno de `D1` para acomodar 3 footprints (se B2).
3. Re-validação do clamp em SPICE: simular `Voc_2S(T)` de −40 °C a +60 °C com clamp activo, confirmar `Vout_max < 5.5 V` em todas as condições.
4. Re-correr ERC + DRC parity.
5. Actualizar `VALIDACAO_DATASHEETS_v0p6.md` → `VALIDACAO_DATASHEETS_v0p7.md` reflectindo o clamp.
6. Manter este doc como rationale formal da decisão.

## 5. O que isto **não** resolve

- Não substitui análise térmica orbital própria (continua como acção do Porto Space térmico).
- Não substitui validação com a EnduroSat — se eles responderem mais tarde "5.5 V é operating max, sobressai sem dano até 7 V", o TVS fica como redundância barata; se confirmarem destrutivo, o nosso clamp já estava certo.
- Não cobre transientes ESD ou surtos do EPS (esses requerem TVS adicional na entrada digital do EPS, fora do escopo do painel).
- Continua a depender de qualificação espacial em revisões futuras.

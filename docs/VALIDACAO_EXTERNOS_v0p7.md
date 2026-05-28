# Validação contra fontes externas — ICARUS v0.7

Data: 2026-05-28

Estado: revisão interna em curso. Não aprovado para fabrico nem voo. Documento vivo — actualizar à medida que respostas externas chegam.

Relacionado: [VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md](VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md), [VALIDACAO_DATASHEETS_v0p6.md](VALIDACAO_DATASHEETS_v0p6.md).

## 1. Footprint Molex 53398-0471 (J1)

### 1.1 Fontes consultadas

- Molex Product Specification "PS-53398-001" (via Jameco datasheet).
- Molex página de produto: https://www.molex.com/en-us/products/part-detail/53398-0471
- SnapMagic Search (sem dimensional info detalhada acessível).

### 1.2 Confirmado contra datasheet

| Parâmetro | Datasheet Molex | Footprint actual | Veredicto |
|---|---|---|---|
| Part Number | 53398-0471 | 53398-0471 | ✅ Match |
| Pitch | 1.25 mm | 1.25 mm | ✅ Match |
| Number of circuits | 4 | 4 + 2 MP | ✅ Match |
| Type | SMT vertical (right-angle) | SMT vertical | ✅ Match |
| Plating | Tin | (não relevante para footprint) | — |
| Mates with | 51021-**00 series | 51021-0400 (4 ckt) | ✅ Match (EPS Tab. 9) |
| Operating temp | −40 a +85 °C | (não validado para esta missão) | ⚠️ Margem espacial pendente |

### 1.3 Pendente — geometria exacta dos pads

A Product Specification (PS-53398) **contém** uma "Recommended PCB Layout" drawing (página com cotas), mas a versão textual do PDF não preserva a tabela de cotas (são desenhos vectoriais/raster, não texto). O que sabemos:

- Pitch entre pinos: 1.25 mm ✅
- Footprint actual: pads `0.65 × 1.8 mm`, MP pads `1.2 × 2.2 mm`, MP a `±3.85 mm` do centro
- Footprint standard PicoBlade (catálogo Molex SDK geral): pads `0.7 × 1.6 mm`, MP ~ `1.0 × 2.0 mm`

**Diferenças detectadas vs valores típicos**:
- Pad width: 0.65 vs 0.7 mm (5% menor — pode comprometer alinhamento)
- Pad length: 1.8 vs 1.6 mm (12% maior — provavelmente seguro)
- MP size: 1.2 × 2.2 vs ~1.0 × 2.0 (15-20% maior — provavelmente seguro)

### 1.4 Acção bloqueante para fabrico

Comparar footprint actual com **drawing oficial Molex SD-53398-001 ou similar** (obter de Molex em www.molex.com → procurar "Sales Drawing" para 53398-0471) **OU** medir conector físico com paquímetro digital. Depois remover o sufixo `_PRELIMINARY` do nome do ficheiro `hardware/footprints/ICARUS_Connector.pretty/Molex_53398-0471_1x04_P1.25mm_PRELIMINARY.kicad_mod`.

Prioridade: **alta** (qualquer fabricação requer footprint correcto), **mas baixo risco** (pitch e P/N estão certos — apenas tolerâncias dos pads).

## 2. Mecânica CubeSat 1U

### 2.1 Fonte consultada

- CubeSat Design Specification (CDS) Rev 14.1, Cal Poly SLO (2022-02-09).

### 2.2 Geometria actual do PCB

| Parâmetro | Valor |
|---|---:|
| Outline da PCB (Edge.Cuts) | 95.0 × 95.0 mm |
| Furos M3 (4 cantos) | (62.5, 62.5), (147.5, 62.5), (62.5, 147.5), (147.5, 147.5) mm |
| Distância centro-a-centro dos furos | 85.0 mm |
| Distância da borda da PCB ao centro do furo | 5.0 mm |
| Diâmetro do furo (clearance M3) | 3.2 mm |
| Espessura da PCB nominal | 1.6 mm (assumido — confirmar com fabricante) |

### 2.3 Confirmado contra CDS Rev 14.1

| Requisito CDS | Estado v0.7 |
|---|---|
| 1U = 10 × 10 × 11.35 cm exterior | PCB cabe no perfil (95 × 95 mm < 100 × 100 mm interior dos rails) |
| Rails mínimo 8.5 mm (CDS §2.2.5) | OK — 5 mm de borda ao furo + componentes mais para dentro deixa margem |
| Componentes ≤ 6.5 mm de protrusão (CDS §2.2.3) | ✅ J1 (Molex) altura ~3.7 mm; células CIC ~0.4 mm; tudo bem abaixo |
| Material Al alloy para estrutura (CDS §2.2.12) | N/A (PCB FR-4) — confirma estrutura aluminium da Porto Space |

### 2.4 Pendente — verificação contra chassis específico

A CDS Rev 14.1 define os limites do **dispenser** (P-POD ou equivalente). O padrão exacto dos furos M3 do **chassis** depende do fabricante específico (ISIS, Pumpkin, EnduroSat, ou Porto Space custom).

- Padrão actual: **85.0 mm c/c**
- Padrões comuns no mercado:
  - ISIS / Pumpkin / EnduroSat side panel typical: **85.6–87.0 mm** c/c
  - CDS standard side panel attach points (não obrigatórios): variam

### 2.5 Acção bloqueante para fabrico

Confirmar com Porto Space (líder mecânico) o padrão de furos do chassis ICARUS específico. Se 85.0 mm não bater, mover os 4 furos no PCB. **Mudança puramente paramétrica — fácil de aplicar.**

Prioridade: **alta**, **baixo risco** (a maioria dos chassis 1U usa 85.0 ±0.6 mm).

## 3. Footprint da célula TJ30SCA-4080 CIC

### 3.1 Fonte consultada

- Datasheet TJ30SCA-4080V4 da AzurSpace (já validado em [VALIDACAO_DATASHEETS_v0p6.md](VALIDACAO_DATASHEETS_v0p6.md)) — confirma parâmetros eléctricos, dimensões do corpo nominal e dimensões das tabs. **Mas o datasheet não inclui o CIC assembly drawing detalhado.**

### 3.2 Confirmado contra datasheet

| Parâmetro | Datasheet | Footprint actual | Veredicto |
|---|---|---|---|
| Corpo da célula | 40.15 × 80.15 mm | 80.15 × 40.15 mm (rotated) | ✅ Match |
| Coverglass (CMG) | 120 µm | (estamos a contar com isto) | ✅ Consistente |
| Pull strength tab | > 1.6 N | N/A no footprint | OK |

### 3.3 Pendente crítico — desenho de montagem CIC

Precisamos do **CIC Assembly Drawing oficial** da AzurSpace para confirmar:

1. **Esquema de contacto de verso**: bond condutivo único no centro vs múltiplos pontos vs wrap-around.
2. **Geometria exacta das tabs**: número de tabs (3 ou 4?), posição relativa, dimensões, altura sobre o plano da célula.
3. **Tolerâncias dimensionais**: ±X mm para posicionamento na PCB.
4. **Recomendações de assembly**: bond paste, soldering, cura térmica.

### 3.4 Acção crítica bloqueante

**Enviar email à AzurSpace** pedindo o CIC Assembly Drawing. Email pronto a enviar abaixo (Secção 4).

Prioridade: **crítica**, **alto risco** (sem isto, o footprint da célula é especulativo e qualquer fabrico é provisório).

## 4. Template de email para AzurSpace

**Para**: support@azurspace.com (ou contact comercial Porto Space-AzurSpace já estabelecido)
**Assunto**: Request for CIC Assembly Drawing — TJ30SCA-4080 Type 32 — Porto Space ICARUS CubeSat

```
Dear AzurSpace team,

We are designing a 1U CubeSat solar panel PCB for the Porto Space Team's
ICARUS mission, using the Triple-Junction GaAs Solar CIC TJ30SCA-4080
Type 32. Our reference is the datasheet TJ30SCA-4080V4.pdf.

The datasheet has been sufficient to confirm electrical parameters, body
dimensions, and tab pull strength. However, we need additional mechanical
detail to finalize the PCB footprint before fabrication.

Could you please send us:

1. The CIC Assembly Drawing for the TJ30SCA-4080 Type 32, showing:
   - Exact tab count, position, and dimensions
   - Back-contact scheme (single bond pad vs distributed vs wrap-around)
   - Tab height above the cell substrate plane
   - Recommended PCB landing pattern (if any)

2. AzurSpace-recommended bonding/assembly procedure:
   - Conductive epoxy / solder paste recommendation for the back contact
   - Soldering profile or thermal cure profile
   - Cleaning and handling requirements

3. Dimensional tolerances for the CIC body and tabs (±X mm), so we can
   verify our footprint clearances.

Our current PCB design (v0.7) places two cells in parallel (1S2P) at
nominal centers (105, 84.425) and (105, 125.575) mm on a 95×95 mm board.
We are willing to share the layout under NDA if helpful for your review.

Our planned launch window is [TBD by Porto Space]; we have approximately
[TBD weeks] before our Critical Design Review (CDR), so any timeline
information would help us plan.

Best regards,
[Nome]
Porto Space Team — ICARUS Solar Panel PCB
[Email]
```

## 5. Resumo do estado de validação externa

| Item | Confirmado | Pendente | Acção | Risco se mantido pendente |
|---|---|---|---|---|
| Molex P/N + pitch | ✅ Datasheet | Geometria pads | Comparar drawing oficial | Baixo (pitch certo) |
| Mecânica 1U geral | ✅ CDS Rev 14.1 | Padrão M3 chassis específico | Confirmar Porto Space | Baixo (move-se facilmente) |
| Cell TJ30SCA-4080 eléctrico | ✅ Datasheet | — | — | Nulo |
| Cell TJ30SCA-4080 assembly | ❌ | CIC drawing | **Email AzurSpace** | **Alto** (bloqueia fabrico) |

## 6. Estado para fabrico

A v0.7 **não é fabrication-ready** enquanto:

1. Drawing oficial Molex não for verificado contra footprint actual.
2. Padrão de furos M3 não for confirmado contra chassis Porto Space.
3. CIC Assembly Drawing da AzurSpace não for recebido e o footprint da célula actualizado.

Os 3 itens são independentes — podem avançar em paralelo. **#3 é o caminho crítico** (resposta AzurSpace tipicamente demora 2-4 semanas).

# Fabrication outputs — ICARUS v0.7 (DRAFT)

Data: 2026-05-28

> **⚠️ DRAFT — NOT FOR FABRICATION.** Estes outputs estão gerados como preparação técnica, mas **não devem ser enviados a um fabricante** sem antes fechar os bloqueadores de validação externa em [`docs/VALIDACAO_EXTERNOS_v0p7.md`](../../docs/VALIDACAO_EXTERNOS_v0p7.md).

## Conteúdo

```
fabrication/v0p7/
├── README.md                                           ← este ficheiro
├── ICARUS_1U_Solar_Panel_PCB_v0p7-bom.csv              ← Bill of Materials
├── ICARUS_1U_Solar_Panel_PCB_v0p7-pos.csv              ← Pick-and-place
├── gerbers/
│   ├── ICARUS_1U_Solar_Panel_PCB_v0p7-F_Cu.gtl         ← Top copper
│   ├── ICARUS_1U_Solar_Panel_PCB_v0p7-B_Cu.gbl         ← Bottom copper
│   ├── ICARUS_1U_Solar_Panel_PCB_v0p7-F_Mask.gts       ← Top solder mask
│   ├── ICARUS_1U_Solar_Panel_PCB_v0p7-B_Mask.gbs       ← Bottom solder mask
│   ├── ICARUS_1U_Solar_Panel_PCB_v0p7-F_Silkscreen.gto ← Top silkscreen
│   ├── ICARUS_1U_Solar_Panel_PCB_v0p7-B_Silkscreen.gbo ← Bottom silkscreen
│   ├── ICARUS_1U_Solar_Panel_PCB_v0p7-F_Paste.gtp      ← Top stencil
│   ├── ICARUS_1U_Solar_Panel_PCB_v0p7-B_Paste.gbp      ← Bottom stencil
│   ├── ICARUS_1U_Solar_Panel_PCB_v0p7-F_Fab.gbr        ← Top fab (info)
│   ├── ICARUS_1U_Solar_Panel_PCB_v0p7-B_Fab.gbr        ← Bottom fab (info)
│   ├── ICARUS_1U_Solar_Panel_PCB_v0p7-Edge_Cuts.gm1    ← Board outline
│   └── ICARUS_1U_Solar_Panel_PCB_v0p7-job.gbrjob       ← Gerber job file
└── drill/
    ├── ICARUS_1U_Solar_Panel_PCB_v0p7.drl              ← Excellon drill
    └── ICARUS_1U_Solar_Panel_PCB_v0p7-drl_map.pdf      ← Drill map for review
```

## Especificações de fabrico

| Parâmetro | Valor |
|---|---|
| Tamanho da PCB | 95 × 95 mm |
| Layers | 2 (F.Cu + B.Cu) |
| Espessura nominal | 1.6 mm (a confirmar com fabricante e especificação Porto Space) |
| Material | FR-4 standard (a confirmar — TG mínimo recomendado 150 °C para espaço) |
| Cor da máscara de solda | Verde (default — alterar se preferência diferente) |
| Cor do silkscreen | Branco (sobre máscara verde) |
| Acabamento de superfície | ENIG recomendado para espaço (HASL aceitável para ground testing) |
| Min. track width | 0.5 mm (corredores B.Cu) |
| Min. clearance | 0.2 mm |
| Min. hole | 0.5 mm (via) / 3.2 mm (M3 mounting) |
| Via type | Padrão through-hole (0.6 mm diameter, 0.3 mm drill) |
| Drill origin | Absoluta (canto inferior esquerdo do board area) |
| Units | mm |

## Conteúdo do BOM

```
J1   Molex_53398-0471        × 1   ← conector solar para EPS
SC1  TJ3xSCA-4080_Type32     × 2   ← células solares
SC2  TJ3xSCA-4080_Type32           (mesmo P/N, montadas em paralelo)
```

Componentes NÃO em BOM (mas no PCB): mounting holes (H1-H4), test points (TP1, TP3, TP4, TP5). São footprints sem componente físico para comprar — fabricam-se com o PCB.

## Pick-and-place

3 componentes:
- J1 (Molex) em `bottom` (B.Cu) — montar primeiro?
- SC1 em `top`, rotação 0°
- SC2 em `top`, rotação 180° (tabs viradas para fora, conforme silk)

**Atenção**: as células CIC NÃO são pick-and-place automático. Requerem bonding manual (epoxy condutivo no centro/verso + soldadura nas tabs). O pos.csv tem-nas listadas para documentação mas a assembly real é manual.

## Bloqueadores antes de enviar a fabricante

Conforme [`docs/VALIDACAO_EXTERNOS_v0p7.md`](../../docs/VALIDACAO_EXTERNOS_v0p7.md):

- [ ] **Molex 53398-0471**: confirmar geometria dos pads contra drawing oficial ou conector físico → remover `_PRELIMINARY` do nome do footprint
- [ ] **Mecânica**: confirmar padrão M3 (actual 85.0 mm c/c) contra chassis Porto Space específico
- [ ] **CIC Assembly Drawing AzurSpace**: confirmar geometria tabs/contacto verso → potencialmente actualizar footprint da célula
- [ ] **Revisão por segunda pessoa** (P3 #30) — sign-off do líder hardware Porto Space
- [ ] **DRC GUI** corrido localmente (P1 #23) — opcional, CLI já corrido limpo

Adicionalmente:
- [ ] **Escolha do fabricante**: confirmar PCB capabilities (track/space/drill mínimos) vs nossos requisitos
- [ ] **Especificação completa do PCB**: stack-up, materiais, finish, marcação
- [ ] **Spec de acceptance**: testes eléctricos do fabricante, NCR procedure

## Como regenerar

Após qualquer mudança ao PCB:

```bash
# Gerbers
"/c/Program Files/KiCad/10.0/bin/kicad-cli.exe" pcb export gerbers \
    --output fabrication/v0p7/gerbers/ \
    --layers F.Cu,B.Cu,F.Mask,B.Mask,F.SilkS,B.SilkS,F.Paste,B.Paste,Edge.Cuts,F.Fab,B.Fab \
    hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pcb

# Drill
"/c/Program Files/KiCad/10.0/bin/kicad-cli.exe" pcb export drill \
    --output fabrication/v0p7/drill/ \
    --format excellon --drill-origin absolute --excellon-units mm \
    --excellon-zeros-format decimal --generate-map --map-format pdf \
    hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pcb

# Pick-and-place
"/c/Program Files/KiCad/10.0/bin/kicad-cli.exe" pcb export pos \
    --output fabrication/v0p7/ICARUS_1U_Solar_Panel_PCB_v0p7-pos.csv \
    --format csv --units mm --side both \
    hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pcb

# BOM
"/c/Program Files/KiCad/10.0/bin/kicad-cli.exe" sch export bom \
    --output fabrication/v0p7/ICARUS_1U_Solar_Panel_PCB_v0p7-bom.csv \
    --fields "Reference,Value,Footprint,Quantity" \
    --group-by "Value,Footprint" \
    hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_sch
```

## Estado de geração

Gerado em 2026-05-28 a partir de `hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pcb` (commit `4aa482a` ou posterior). Re-gerar após cada commit que afecte o PCB ou schematic.

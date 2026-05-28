# CLAUDE.md — Notas para sessões futuras

Este ficheiro é o teu briefing rápido (Claude) ao retomares trabalho neste repositório. Lê isto **primeiro**, depois confirma o estado actual com `git log` antes de assumir qualquer coisa.

## 1. O projecto numa frase

PCB do painel solar 1U para o CubeSat **ICARUS** da **Porto Space Team** (universidade do Porto), em KiCad 10.0. Utilizador é o **Nuno** (pt-PT), trabalha solo, lidera-o um team lead (chat por Telegram). Interface com o **EnduroSat EPS I** via conector Molex 53398-0471.

## 2. Versão actual e topologia

**v0.7** — `main` (não há PRs neste repo, faz-se merge direto).

- **Topologia: 1S2P paralelo** (duas células TJ30SCA-4080 Type 32 em paralelo). Era 2S1P em v0.6 e foi mudada por instrução do líder em 2026-05-28; ver §4 abaixo.
- **J1 (Molex 53398-0471)**: na face **inferior (B.Cu)** — instrução do líder.
- **Pinout J1**: pins 1, 2 = `PV-`; pins 3, 4 = `PV+` (matches EPS I Tabela 8).
- **D1** (TVS): **removido** — paralelo elimina risco de Voc OVP, EPS tem OVP integrada acima de 5.5 V (citação directa do manual). Não voltar a adicionar sem motivo novo.
- **TP2** (`SERIES_MID` test point): **removido** — net já não existe.
- **Test points actuais**: TP1 (PANEL_NEG), TP3 (PANEL_POS), TP4 (BENCH_ONLY_PANEL_NEG, 2.5mm), TP5 (BENCH_ONLY_PANEL_POS, 2.5mm).
- **Mounting holes**: 4× M3 nos cantos a 85.0 mm c/c (vai precisar de confirmação contra chassis Porto Space).

## 3. Estado de validação em 1 tabela

| Item | Estado | Próximo passo | Quem |
|---|---|---|---|
| ERC parity (CLI) | ✅ 0 erros, 3 warnings (lib CLI only) | — | — |
| DRC parity (CLI) | ✅ 0 erros, 3 warnings | — | — |
| Schematic v0.7 1S2P | ✅ correcto | — | — |
| PCB layout v0.7 | ✅ DRC clean, J1 em B.Cu | Revisão visual GUI feita | — |
| Footprint Molex | ⚠️ P/N + pitch OK; pad geometry preliminary | Drawing oficial OU paquímetro | Nuno |
| Mecânica 1U | ⚠️ Dentro de CDS Rev 14.1; M3 pattern preliminary | Confirmar chassis Porto Space | Líder mecânico |
| Footprint célula CIC | ⚠️ Eléctrico OK; assembly drawing pendente | Email AzurSpace (já redigido) | Nuno enviar |
| Continuidade harness | 🔴 Não testado | Hardware real | Nuno |
| Bench tests eléctricos | 🔴 Não testado | Hardware real | Nuno |
| Revisão 2ª pessoa | 🔴 Não feita | Apresentar ao líder | Nuno |
| Fabrication outputs | ✅ Gerados DRAFT em `fabrication/v0p7/` | Não enviar a fab até resolver acima | — |

## 4. Decisões importantes (ordem cronológica resumida)

1. **v0.6 → v0.7 — mudança de topologia para paralelo (2026-05-28).** Líder pediu via Telegram: "faz em paralelo", "molex tem de ser na parte inferior", "dois negativos e dois positivos no Molex como no EPS". A leitura cuidada da Tabela 12 do EPS I + secção MPPT confirmou (a) 5.5 V é trigger OVP integrada (não destrutivo), (b) UVLO ~0.78 V (boost MPPT aceita Vmp baixo), o que torna paralelo electricamente excelente.
2. **D1 (TVS) removido na v0.7** — em paralelo, `Voc_1S` max cold ≈ 3.33 V, muito abaixo dos 5.5 V. TVS torna-se desnecessário. NÃO voltar a colocar a menos que haja evidência nova (ex: resposta EnduroSat dizer que 5.5 V é destrutivo, o que contradiz o próprio manual).
3. **Footprints TestPoint/MountingHole sincronizados com lib KiCad 10.0 stdlib** — eliminou todos os `lib_footprint_mismatch` warnings (10 → 0). Os 3 warnings restantes são `lib_footprint_issues` para libs locais ICARUS_Solar/ICARUS_Connector, que só aparecem no CLI (no GUI resolvem). NÃO tentar "arranjar" estes 3, é limitação fundamental do `kicad-cli` sobre `${KIPRJMOD}`.
4. **EnduroSat não foi contactada** — o líder pediu para não esperar (resposta deles é lenta). Análise técnica fez-se a partir do manual disponível, conclusão é defensável.

## 5. Ficheiros / pastas a conhecer

```
hardware/
  ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_sch    ← SCHEMATIC v0.7
  ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pcb    ← PCB v0.7 ← FONTE DA VERDADE
  ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pro    ← Project file
  ICARUS.kicad_sym                            ← Lib de símbolos custom
  sym-lib-table, fp-lib-table                 ← Lib tables locais
  footprints/
    ICARUS_Solar.pretty/TJ3xSCA_4080_CIC.kicad_mod    ← Footprint célula (preliminar)
    ICARUS_Connector.pretty/Molex_..._PRELIMINARY.kicad_mod ← Footprint J1 (preliminar)

docs/                                         ← TODOS os docs em pt-PT
  VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md        ← REFERÊNCIA v0.7 (lê primeiro)
  VALIDACAO_EXTERNOS_v0p7.md                  ← Estado vs Molex/CDS/AzurSpace + email pronto
  ANALISE_TERMICA_ORBITAL_v0p7.md             ← Análise 1D LEO
  VALIDACAO_DATASHEETS_v0p6.md                ← Cross-check eléctrico (continua válido com nota v0.7)
  ANALISE_TERMICA_VOC_v0p6.md                 ← SUPERSEDED (era para 2S1P)
  DECISAO_TVS_v0p7.md                         ← SUPERSEDED (D1 removido em paralelo)
  CORRECAO_TOPOLOGIA_SERIE_v0p6.md            ← SUPERSEDED (anti-série fix da v0.6)
  REVIEW_ACTIONS_v0p6.md                      ← Histórico v0.6
  VALIDACAO_LOCAL_v0p6.md                     ← Histórico v0.6
  CELL_UNDER_COPPER_RISK_v0p6.md              ← Histórico parcial (princípio continua válido)
  MECHANICAL_CELL_CLEARANCE_v0p6.md           ← Continua VÁLIDO em v0.7 (geometria inalterada)

reports/
  ERC_v0p7_kicad10_win.rpt    DRC_v0p7_kicad10_win.rpt
  v0p7_top.svg  v0p7_bottom.svg  v0p7_fab.svg     ← Plots actuais
  (v0p6_* idem como histórico)

fabrication/v0p7/             ← Gerbers + drill + BOM + POS DRAFT (não enviar a fab ainda)

test_logs/
  LIBRARY_WARNINGS_WAIVER_v0p6.md  ← Waiver formal (actualizado com nota v0.7)

EPS_I_User_Manual.pdf         ← Datasheet EPS (Tabela 12 = solar input limits)
TJ30SCA-4080V4.pdf            ← Datasheet célula (Type 32 column)
```

## 6. Comandos úteis (Windows PowerShell + Git Bash via Bash tool)

```bash
# KiCad 10 está em: /c/Program Files/KiCad/10.0/bin/
KICLI="/c/Program Files/KiCad/10.0/bin/kicad-cli.exe"

# ERC parity
"$KICLI" sch erc --severity-all \
  --output reports/ERC_v0p7_kicad10_win.rpt \
  hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_sch

# DRC parity
"$KICLI" pcb drc --schematic-parity --severity-all \
  --output reports/DRC_v0p7_kicad10_win.rpt \
  hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pcb

# Plots SVG
"$KICLI" pcb export svg --layers F.Cu,F.Mask,F.SilkS,Edge.Cuts \
  --output reports/v0p7_top.svg hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pcb
"$KICLI" pcb export svg --layers B.Cu,B.Mask,B.SilkS,Edge.Cuts \
  --output reports/v0p7_bottom.svg hardware/ICARUS_1U_Solar_Panel_PCB_v0p7.kicad_pcb

# Regenerar fabrication outputs — ver fabrication/v0p7/README.md
```

## 7. Ferramentas instaladas

- KiCad 10.0 + kicad-cli (em `C:\Program Files\KiCad\10.0\`)
- GitHub CLI `gh` (em `C:\Program Files\GitHub CLI\`) — **NÃO autenticada** (precisa de `gh auth login` interactivo). Em geral o Nuno prefere não usar PRs (trabalha solo, merge directo em main).
- `pdftotext` disponível via Git Bash MinGW.
- `winget` disponível para instalar coisas.

## 8. Convenções aceites

- **pt-PT** em docs e respostas ao Nuno.
- **Sem emojis** em código/ficheiros, só em respostas markdown se ajudar a legibilidade.
- **Sem PRs** — `git checkout main; git merge --no-ff <branch>; git push`. Branch nova só para mudanças grandes.
- **CRLF line endings** no Windows (KiCad files); ignorar warnings do git sobre conversão.
- **Commits** com `Co-Authored-By: Claude Opus 4.7 <noreply@anthropic.com>` e mensagem em inglês descritiva.
- **NÃO usar `git add -A` ou `git add .`** — adicionar ficheiros específicos para evitar incluir `.claude/`, `.history/`, etc.

## 9. O que NÃO fazer

- ❌ Voltar a colocar `D1` (TVS) sem motivo novo concreto.
- ❌ Voltar a 2S1P sem instrução explícita do líder.
- ❌ Mover J1 para F.Cu (líder pediu B.Cu).
- ❌ Mudar o pinout do J1 (1,2=PV-; 3,4=PV+ é instrução do líder).
- ❌ Esperar pela EnduroSat (líder disse para não esperar).
- ❌ Tentar autenticar `gh` em background — é interactivo, requer browser do Nuno.
- ❌ "Arranjar" os 3 warnings `lib_footprint_issues` que sobram — é limitação CLI.
- ❌ Enviar fabrication outputs a fabricantes sem confirmar os 3 bloqueadores externos.

## 10. Continuação esperada — quando o Nuno regressar (~3 semanas após 2026-05-28)

O que provavelmente vai trazer:
1. **Resposta da AzurSpace** com CIC assembly drawing → actualizar `TJ3xSCA_4080_CIC.kicad_mod`, remover "preliminary" da descrição, re-correr DRC, regenerar plots e fabrication outputs.
2. **Drawing/medições Molex** → actualizar `Molex_53398-0471_..._PRELIMINARY.kicad_mod`, renomear ficheiro sem `_PRELIMINARY`, idem.
3. **Spec do chassis** Porto Space → confirmar/mover os 4 mounting holes, idem.
4. **Sign-off do líder** ou feedback → endereçar comments.
5. Possíveis novas instruções (sempre via Telegram do líder, screenshots reenviados pelo Nuno).

Workflow ao retomar:
1. `git log --oneline -20` para ver o que mudou (eventualmente nada — o utilizador esteve fora).
2. Ler `docs/VALIDACAO_TOPOLOGIA_PARALELO_v0p7.md` e `docs/VALIDACAO_EXTERNOS_v0p7.md`.
3. Pedir ao Nuno qual dos 3 pontos pendentes tem informação nova.
4. Atacar isso, re-correr ERC/DRC, regenerar SVGs e fabrication, commit + push para `main`.

## 11. Estado do trabalho técnico autónomo

**Esgotado.** Tudo o que se podia fazer sem inputs externos está feito. Os bloqueadores são todos pessoas/hardware externos. **NÃO ficar a polir o que está pronto**; em vez disso esperar input do Nuno e atacar o que ele trouxer.

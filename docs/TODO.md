# TODO — Fluent-round-Dark: legibilidade (transparência) + acrílico Win + alt-tab nativo

> Escopo autorizado pelo DEV (2026-09-23): reduzir a transparência do GNOME Shell para legibilidade e
> aplicar blur acrílico estilo Windows, preservando a identidade Fluent round dark (cores, raios, layout).
> NO redesign. Requisito extra: alt+tab deve ficar NATIVO como o tema traz.
> Refs visuais: /home/quitto/Projects/winTransferLinux/config/WinReferece (11 PNGs).
> ESTADO: 2ª passada aplicada (2026-10-05): painel 0.85 + alfa/sombra por componente (busca, dash,
> switcher, menus/diálogos) — validação visual pendente (TASK-008).

## Human

- [x] [H][HIGH] TASK-004 — ✔ Aprovado (2026-09-23, via pergunta): acrílico Win11 canônico — painel ~0.85
      com blur; superfícies com texto ≥ 0.95 (menus/diálogos/notificações já estavam no alvo)
- [ ] [H][HIGH] TASK-005 — Decidir config do Blur My Shell. ATUAL (dconf pós-TASK-005A): panel { blur=true,
      sigma=44, brightness=0.8, override-background=FALSE (testado 2026-10-06),
      override-background-dynamically=true, unblur-in-overview=true }. Opções:
      (A) `override-background=false` → o tint rgba(0,0,0,0.85) do tema aparece SOBRE o blur
          (acrílico Win clássico — AGORA EM TESTE; ver TASK-005A). Comando:
          dconf write /org/gnome/shell/extensions/blur-my-shell/panel/override-background false
      (B) manter override e baixar brightness (~0.6) para escurecer o acrílico via BMS
      Revert (A): dconf write .../panel/override-background true
- [ ] [H][HIGH] TASK-011 — ALT-TAB NATIVO: a extensão `advanced-alt-tab@G-dH.github.com` está HABILITADA
      e substitui o switcher nativo do tema (UI/CSS próprios da extensão — o "feito em outro lugar").
      Para voltar ao alt-tab NATIVO Fluent (#222 sólido): `gnome-extensions disable
      advanced-alt-tab@G-dH.github.com` (decisão do DEV — muda comportamento também, não só visual;
      pode reativar com enable). Nota: [coverflow-alt-tab] no BMS é placeholder (não instalado).
- [ ] [H][MEDIUM] TASK-007 — Commit: baseline = commit 0f32920 (repo estava limpo); diff atual revertível
      com `git checkout -- gnome-shell/gnome-shell.css`. Commitar após validação do DEV.
- [x] [H][HIGH] TASK-013 — ✔ EXECUTADA (2026-10-05, por instrução do DEV na 3ª passada):
      dconf write /org/gnome/shell/extensions/dash-to-panel/trans-panel-opacity 0.85
      → verificado por leitura: 0.84999999999999998 (=0.85). Taskbar DTP agora usa a cor do tema
      a 85% (transparency.js aplica inline; cor vem do tema, tom preservado). Complementa TASK-005.

## Agent

- [x] [A][MECHANICAL] TASK-003 — ✔ 1ª passada: #panel rgba(0,0,0,0.5)→rgba(0,0,0,0.85) (L2155) +
      panel-corner (L2167). Nenhum seletor .switcher-* alterado. ATENÇÃO: enquanto BMS panel
      .override-background=true, a aparência do painel é dominada pelo BMS (ver TASK-005)
- [ ] [A][MECHANICAL] TASK-006 — Se o DEV estender o escopo a apps: replicar em gtk-3.0/gtk.css e
      gtk-4.0/gtk.css e sincronizar os gtk-dark.css (cópias idênticas linha-a-linha)
- [x] [A][LOW] TASK-009 — ✔ TODO atualizado (2026-09-23)
- [x] [A][MECHANICAL] TASK-012 — ✔ 2ª passada (2026-10-05) em gnome-shell/gnome-shell.css: alfa/sombra
      por componente. Novo `.switcher-list` (bg #222→rgba(34,34,34,0.92) !important + sombra 0.45/0.3);
      `.search-entry` 0.15→0.30 !important; `#overview .search-entry` 0.75→0.85 (hover 0.85→0.90,
      box-shadow none→0 2px 10px 0.2); `.search-section-content` 0.15→0.30 !important;
      `#dash .dash-background` 0.20→0.30 !important; sombras: .popup-menu-content 0.1→0.35,
      .modal-dialog 0.2→0.4, .arcmenu-menu .popup-menu-content 0.1→0.35, grupo L1973 0.35→0.4.
      `!important` onde BMS/AATWS precisam ser sobrepostos (regras deles não-important, mesma origem).
      bg NÃO alterado: menus 0.97 / diálogos 0.97 / OSD+grupo #222 (alvo TASK-004). Sintaxe OK
      (chaves balanceadas); validação visual = TASK-008.
- [x] [A][MECHANICAL] TASK-005A — ✔ Teste isolado "origem do bloco preto" (2026-10-06, ordem do DEV):
      ÚNICO valor alterado = dconf `blur-my-shell/panel/override-background` true→false (σ44, brightness 0.8,
      alpha DTP 0.70, pipeline radius, CSS 18+/12− e DTP 0.85-prescrito NÃO tocados; σ44/DTP0.70 já estavam
      assim antes = ajuste manual do DEV). ANTES: override-background=true. DEPOIS: =false (estado atual,
      mantido). Evidência A/B via portal screenshot (12s/lado, controle 0% diff): taskbar 0.00% de diff e
      luminância IDÊNTICA em true vs false → o toggle NÃO mudou a taskbar mensuravelmente (sem live-apply ou
      sem efeito aqui); par ab_antes/ab_depois contaminado (atividade do DEV na tela). Conclusão: NÃO há
      evidência de que override-background=true seja a origem da camada preta NA TASKBAR estática; origem do
      bloco segue INDETERMINADA. Itens 2/4/5 (menus/search/overview abertos) não capturáveis pelo agente
      (sem input injection) → re-login manual [DEV]: se o bloco sumir, manter false como preferido do Acrylic;
      senão, reverter `.../panel/override-background true`. Capturas em /tmp/opencode/ (r_c1/c2, r_true/false,
      t005a_*, ab_*).
- [x] [A][MECHANICAL] TASK-014 — ✔ 3ª passada (2026-10-05) "acrylic Win11" (instrução do DEV:
      alpha/sombra/blur; cores e geometria intocadas). gnome-shell/gnome-shell.css (só alpha, MESMO tom,
      tudo `!important`): `.search-entry` 0.30→0.45; `#overview .search-entry` 0.85→0.90 (hover 0.90→0.93,
      focus 0.95 fixo); `.search-section-content` 0.30→0.45; `#dash .dash-background` 0.30→0.45;
      `.switcher-list` 0.92→0.95. dconf: BMS panel sigma 30→45; BMS pipelines radius/unscaled_radius
      30→45 (3×; brightness 0.8/0.6 e corner 24 PRESERVADOS); DTP trans-panel-opacity 0.15→0.85
      (ver TASK-013). Validado: chaves 862/862; git diff acumulado (3 passadas) 1 arquivo 18+/12−.
      Revert: `git checkout -- gnome-shell/gnome-shell.css` + `dconf reset` (ver Notas técnicas).
      Alt+Tab do AATWS não tem blur de extensão (só alfa+sombra). Validar = TASK-008.

- [x] [A][MECHANICAL] TASK-015 — ✔ 4ª passada (2026-10-06) BORDER-RADIUS uniforme (instrução do DEV:
      "deixe o border radius igual... um pouco mais de 8px, sem exagerar"; única permissão desta tarefa).
      gnome-shell/gnome-shell.css, SÓ border-radius: 5px/7px/8px → **10px** (49 decl. + 9 cantos
      parciais: `.candidate-page-button-previous/next`, `.hotplug-notification-item`,
      `.quick-toggle-has-menu .quick-toggle:ltr/rtl`, `.quick-toggle-menu-button:ltr/rtl`) +
      `11px !important` (.datemenu-popover) e `9px` (screenshot-ui-shot-cast-button) → 10px.
      `-arrow-border-radius` 8→10 (boxpointers L595/L3251). Inclui `!important` e cantos
      (`5px 5px 0 0`/`0 0 5px 5px` → `10px 10px 0 0`/`0 0 10px 10px`).
      Resultado: 10px em todos os overlays (inclui grupo L1973 .switcher-list/.osd-window/.resize-popup);
      mantidos 0/2/3/6/12/15/17/20/24/30/32/33/52/99/100/9999px (micro-el. e containers grandes).
      AATWS `.item-box` já 10px → agora casado com o container (resolve o mismatch do alt-tab).
      Validação: chaves 862/862; git diff 120 linhas radius (todas desta passada) + 29 linhas
      alpha/sombra das passadas 1–3 (nenhuma cor/sombra nova). Só resta validar visualmente = TASK-008.

## Shared

- [x] [S][HIGH] TASK-001 — ✔ Auditoria de superfícies-chave completa (ver Baseline). Restante (clusters
      !important, overview/dash/thumbnails) só se a validação apontar
- [x] [S][HIGH] TASK-002 — ✔ Verificado pós-edição: alt-tab do tema é nativo e sólido (grupo L1973
      `#222222`; itens L2045–2121: outline 0.06 / selected #3281ea) e NENHUM seletor .switcher-* foi
      alterado; grupo L1973 intacto (OSD já sólido, override desnecessário)
- [ ] [S][HIGH] TASK-008 — Validação visual pós-mudança (re-login no Wayland): barra superior, popup do
      calendário, quick settings, notificações e alt-tab; conferir contra os 11 PNGs de WinReferece
- [ ] [S][LOW] TASK-010 — Avaliar uso do noise-texture.svg (em gnome-shell/assets, não referenciado) para
      textura acrílica estilo Win11

## Blocked / Infra

- [ ] [H][BLOCKED] codebase-explainer: model "9router/nvidia/deepseek-ai/deepseek-v4-flash-0731" não
      existe (config stale do subagent) — corrigir o model do agente para reusar
- [x] [H][INFO] Refs Win: modelo principal sem input de imagem (6 leituras falharam; 'explore' bloqueado
      por permission rules) → DEV escolheu "acrílico Win11 canônico" (2026-09-23); validação visual é
      do DEV, contra os screenshots
- [x] [H][INFO] task-scheduler executou mas não devolveu plano (saída vazia) — TODO escrito manualmente

## Baseline — audit gnome-shell/gnome-shell.css (5042 linhas)

| Superfície | Seletor (linha) | Baseline | Ação | Atual |
|---|---|---|---|---|
| Popup menus | .popup-menu-content (L40) | rgba(32,32,32,0.97) | — | ✔ no alvo |
| Diálogos | .modal-dialog (L47) | rgba(32,32,32,0.97) | — | ✔ no alvo |
| Barra superior | #panel (L2148) | rgba(0,0,0,0.5) | 0.5→0.85 | ✔ aplicado |
| Panel-corner | #panel .panel-corner (L2165) | rgba(0,0,0,0.5) | 0.5→0.85 | ✔ aplicado |
| Painel overview/lock | #panel:overview etc. (L2276) | transparent | mantido (identidade; BMS unblur-in-overview=true) | ✔ |
| Mensagens | .message (L1291) | rgba(32,32,32,0.95) | — | ✔ no alvo |
| Msg em menus | .popup-menu .message (L1309) | #272727 sólido | — | ✔ |
| OSD / Alt-tab (grupo) | L1973 (inclui .switcher-list) | #222222 sólido | NÃO TOUCH | ✔ nativo |
| Alt-tab itens | .switcher-list .item-box (L2045–2072) | 0.06 / #3281ea | NÃO TOUCH | ✔ nativo |
| Texto raiz | stage (L5) | rgba(255,255,255,0.9) | — | ✔ |
| Busca (base) | .search-entry (L2460) | 0.15 (BMS vencia → transparente) | 0.15→0.30 !important | ✔ 2ª passada |
| Busca overview | #overview .search-entry (L2501) | 0.75 | 0.90 (+sombra 0.2) | ✔ 3ª passada |
| Resultados busca | .search-section-content (L2557) | 0.15 (BMS vencia → transparente) | 0.45 !important | ✔ 3ª passada |
| Dash overview | #dash .dash-background (L2620) | 0.20 (BMS vencia → transparente) | 0.45 !important | ✔ 3ª passada |
| Alt-tab switcher | .switcher-list NOVA regra (pós-L1980) | — | rgba(34,34,34,0.95) !important + sombra | ✔ 3ª passada |
| Sombras | popup/modal/arcmenu/grupo L1973 | 0.1 / 0.2 / 0.1 / 0.35 | → 0.35 / 0.4 / 0.35 / 0.4 | ✔ 2ª passada |

## Notas técnicas

- L1 do CSS: "This stylesheet is generated, DO NOT EDIT" — gerado upstream; o DEV já sincroniza a cópia
  local com o estado live (commit "Sync gnome-shell.css with live state")
- 540 rgba(α<1) no shell; 0 `blur` em todo o CSS do tema → blur só via Blur My Shell
  (blur-my-shell@aunetx + user-theme instalados)
- Blur My Shell (dconf /org/gnome/shell/extensions/blur-my-shell/) — 3ª passada (TASK-014):
  pipelines: default = gaussian radius 45 / brightness 0.8 ("Task Bar"); default_rounded = radius 45 /
  brightness 0.6 + corner 24 (usado no overview); panel { sigma 45, brightness 0.8, override-background
  true/dynamic, unblur-in-overview true }; overview { style-components 3 }; applications { blur=false,
  whitelist=[org.gnome.Ptyxis] }
- Revert dconf 3ª passada: `dconf reset -f /org/gnome/shell/extensions/blur-my-shell/panel/sigma`;
  pipelines: `dconf reset -f /org/gnome/shell/extensions/blur-my-shell/pipelines` (APAGA pipelines custom
  — preferir re-setValue 30/24 se houver custom); DTP: `dconf write
  /org/gnome/shell/extensions/dash-to-panel/trans-panel-opacity 0.15`
- Alt-tab: `advanced-alt-tab@G-dH.github.com` HABILITADO — substitui o switcher nativo (ver TASK-011)
- st-mix acrílico experimental em L3598/3603; clusters !important do DEV: 644–723, 972–984, 2193–2323,
  3493–3588, 4673–4777, 4862–5040
- GTK (apps, fora do escopo "Shell"): janelas GTK4 com rgba(32,32,32,0.5) em gtk-4.0/gtk.css L2482/2486 —
  decidir depois (apps não têm blur: BMS applications.blur=false)
- Repo: main · origin git@github.com:QuittoGames/Fluent-gtk-theme-modified.git · baseline limpo (0f32920)
  antes das edições

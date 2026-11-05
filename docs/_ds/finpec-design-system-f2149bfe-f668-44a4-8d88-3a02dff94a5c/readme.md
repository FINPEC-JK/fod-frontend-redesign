# FINPEC Design System

> Uma única direção de design para todos os sistemas legados e microssistemas da FINPEC — foco exclusivo em **Web** e **Web Responsive**. Sóbrio, limpo, objetivo, feito para estruturas de dados densas e componentes complexos.

---

## 1 · Contexto do produto

A **FINPEC** é uma fintech de **crédito rural / agronegócio**. A operação gira em torno da emissão e gestão de **CPRFs** (Cédulas de Produto Rural Financeiras), integração com a **B3** (registro de contratos), e acompanhamento de **indicadores do agro** (Boi Gordo, CDI, IPCA). O público interno são analistas e administradores que lidam com carteiras grandes (milhares de cédulas), prazos críticos e fluxos de assinatura/registro.

Existem dois produtos de referência, ambos considerados satisfatórios pelo time e usados como ponto de partida deste sistema:

| Produto | O que é | Arquivo de origem |
|---|---|---|
| **FOXE IA** | Plataforma de IA para contagem/auditoria de gado (visão computacional, filas de inferência, revisão humana) | `uploads/Foxe IA - Redesign.dc.html` |
| **Painel do Investidor / Admin** | Painel administrativo de crédito rural — CPRFs, B3, clientes, filiais | `uploads/Finpec Painel Adm - Redesign (standalone).html` |

Este design system é uma **mescla das duas linguagens**, agora **unificadas** sob a mesma marca: a sobriedade "papel & tinta" do Painel com o rigor de dados/monoespaçado do FOXE. Ambos os produtos compartilham o accent laranja-queimado da marca e as superfícies de papel creme quente; o FOXE preserva o tema escuro e oferece accents alternativos (verde, âmbar, azul, terra) para contextos operacionais.

### Fontes fornecidas (guarde, mesmo que o leitor não tenha acesso)

- **Repositório GitHub (fonte deste projeto):** `FINPEC-JK/webresp_design-system` (branch `main`)
  → https://github.com/FINPEC-JK/webresp_design-system
  Este projeto foi importado desse repositório (tokens, componentes, guidelines, UI kits, template). Repos relacionados da org: `FINPEC-JK/web-webresp_gen_des_system`, `FINPEC-JK/foxe_ai`.
- **Uploads:** logotipos (`FINPEC - LOGOTIPO.png`, `LOGO FINPEC - PRETO.png`, `LOGO FINPEC MONOGRAMA.png`), paleta em PDF (`FINPEC - LOGOTIPO TODAS AS CORES.pdf`), foto de jornada (`finpec_640_whatsapp copy.jpg`), e os dois redesigns acima.

> **Recomendação ao leitor:** explore o repositório GitHub e os dois arquivos de redesign para reproduzir interfaces FINPEC com maior fidelidade. Eles são a fonte-verdade desta linguagem.

---

## 2 · Fundamentos de conteúdo (Content fundamentals)

**Idioma:** Português do Brasil, sempre. Termos técnicos do mercado (CPRF, B3, CDI, IPCA, Boi Gordo, antecipação, resgate) são usados sem tradução nem explicação — o público é especialista.

**Tom:** sóbrio, direto, operacional. Frases curtas. Sem marketing, sem entusiasmo artificial, sem exclamação. A interface fala como um analista experiente fala com outro: objetivo e factual.

**Pessoa:** trata o usuário por "você" de forma implícita e usa a primeira pessoa só em saudações ("Bem-vindo, João"). Rótulos e ações são impessoais e no infinitivo/imperativo curto: "Emitir CPRF", "Exportar", "Filtrar por produtor ou número…".

**Caixa (casing):**
- Títulos de página: **Capitalização de frase** ou nome próprio — "Visão Geral", "Nova CPRF", "CPRFs Ativas".
- Rótulos de seção e cabeçalhos de tabela: **MAIÚSCULAS** com tracking largo — "GERAL", "CPRFS", "B3 & INDICADORES".
- Status/pills: capitalização de frase — "Ativa", "Em revisão", "Pendente assinatura". Estados técnicos do sistema podem aparecer em `snake_case` mono — "needs_review".

**Números e dinheiro:** padrão BR. Moeda "R$ 1.240.000" (ponto de milhar, vírgula decimal). Percentuais com vírgula "+8,2%". Datas "12/08/2026". Valores grandes abreviados com unidade separada: "R$ 1,84 **bi**", "R$ 214,7 **mi**". Tudo alinhado à direita e tabular em tabelas.

**Máscaras de entrada (regras firmes):**
- **Campos de valor** usam a máscara `R$0,00`. O preenchimento é **da direita para a esquerda**, sempre respeitando as casas decimais — digitar `1`,`8`,`4` produz `R$0,01 → R$0,18 → R$1,84`. Componente: `MoneyInput mask="currency"`.
- **Campos de percentual** usam `0,00%`, mesmo preenchimento da direita para a esquerda, decimais respeitados. Componente: `MoneyInput mask="percent"`.
- **Exibição monetária (somente leitura)** — em KPIs, cards e tabelas — leva o `R$` **na frente**, colado ao número, e a unidade abreviada com respiro depois: `R$1,84 bi`. No `KpiCard`, use `prefix="R$"` + `unit="bi"` (o prefixo cola no valor; a unidade ganha espaçamento maior). Nunca use um `MoneyInput` para exibir — ele é campo de entrada.

**Mensagens:**
- Confirmação (toast): curta, no particípio — "Correção salva", "CPRF criada e enviada para assinatura".
- Atenção/alerta: nomeia o número e a consequência — "5 CPRFs com prazo crítico · Assinatura vence em menos de 48h."
- Estado vazio: constata o fato, sem dramatizar — "Nenhuma divergência · Todas as contagens estão conferidas."

**Emoji:** **nunca.** Não faz parte da marca. Ícones de linha cumprem esse papel.

---

## 3 · Fundamentos visuais (Visual foundations)

A metáfora central é **"papel & tinta"**: superfícies de papel creme quente e tinta marrom-quase-preta. A **espinha interativa é um azul royal calmo** — e o laranja da marca fica reservado ao logotipo e a chamadas de atenção. Nada de cinza frio, nada de azul-arroxeado, nada de gradientes decorativos.

**Cor — papel dos canais (regra firme)**
- **Azul royal (`--primary #2860C6`)** é a cor **primária e de interação**: botões primary, links, estados ativos (nav, seleção), foco, **iconografia** e **gráficos**. Hover `#204FA6`, press `#1A4488`, soft `#E8EEFB`, ink `#1B4C92`.
- **Laranja-queimado (`--accent #DC4E25`)** é **só a marca** (logotipo, wordmark) e **indicadores de atenção** (badges "hot", sinais de prazo). **Não** é mais a cor de ação primária.
- **Verde pastel (`--ok #4C9A5E`, soft `#E9F3E5`)** para positivo/sucesso — tom suave, conforme referência, sem competir com o azul.
- **Vermelho (`--danger #C8382B`)** é **exclusivo de perigo/alerta**: indicadores críticos, chamadas de atenção severas, ações destrutivas confirmadas. **Não** use vermelho em botões de "Cancelar" (esses são `ghost`/`secondary` neutros) nem em estados comuns.
- Canvas creme quente (`--bg #F4F2EE`), cards brancos (`--surface`), recessos creme (`--surface-2`), bordas hairline quentes (`--border #E7E3DB`).
- **Chrome escuro quente** para a sidebar escura e arte de login (`--side #181612`) — preto-marrom, nunca preto puro nem cinza-azulado.
- Texto em 4 tons quentes: `--ink` → `--ink-2` → `--muted` → `--faint`.
- Semânticos em quatro canais (success/warn/danger/info), cada um com tripla solid / soft / ink.

**Tipografia**
- **Display — Schibsted Grotesk:** títulos, valores de KPI, wordmark. Tracking apertado (`-0.02em`), pesos bold/extrabold. É o que "lê como número/figura".
- **UI — Hanken Grotesk:** corpo, rótulos, controles. A voz cotidiana da interface.
- **Mono — IBM Plex Mono:** números tabulares, IDs (`#2740`, `0008_CP_FSTAMON`), códigos e metadados técnicos. `tabular-nums` sempre.
- Base 14px; tabelas 13px; nunca abaixo de ~10,5px (rótulos de nav em maiúscula).

**Espaçamento & layout**
- Ritmo base **4px** (escala `--sp-1`…`--sp-16`).
- Shell fixo: sidebar 264px (**recolhível a 76px** via botão sanduíche), topbar 68px, conteúdo centralizado com `max-width 1320px`. **Sidebar e conteúdo têm scroll independente** — cada um rola dentro de si (`min-height:0` + `overflow:auto` + `overscroll-behavior:contain`); a página nunca rola como um todo. Quando **recolhida**, só os ícones aparecem (labels, badges e textos ocultos) e cada ícone com pendências mostra um pequeno ponto indicador no canto.
- KPIs em grid de 4 colunas; cards de conteúdo em grids assimétricos (ex.: 1.6fr / 1fr).

**Cantos (radii)**
- `--r-sm 7px` (botões, inputs, nav, chips) · `--r 10px` (campos de busca, toasts) · `--r-lg 14px` (cards, KPIs) · `--r-xl 20px` (superfícies grandes) · pill 100px. Suave, mas **nunca "blobby"**.

**Sombras & elevação**
- Sombras **quentes** (tingidas de `rgba(28,24,16,…)`), nunca cinza neutro. Três níveis: `--shadow-sm` (cards em repouso), `--shadow` (hover/flutuante), `--shadow-lg` (modais/toasts). Cards usam sombra mínima + borda hairline — a borda faz o trabalho, a sombra só dá leveza.
- Foco: anel suave de 3px em `--primary-soft` (`--focus-ring`), nunca outline azul do browser.

**Como são os cards**
Superfície branca, borda hairline `1px var(--border)`, raio 14px, `--shadow-sm`. Cabeçalho opcional com borda inferior, título 15px semibold, pill de contagem e slot de ações à direita. Corpo com 20px de padding (ou `bodyless` para tabelas full-bleed).

**Animação**
- Discreta e funcional. Transições de 0,14s em estados de hover/focus; entrada de conteúdo com `fpFadeIn` (fade + 6px de subida, 0,28s ease). Toasts entram de baixo (`fpToastIn`).
- **Sem** bounce, sem loops decorativos infinitos, sem parallax. O easing padrão é `ease` / `cubic-bezier(.4,0,.2,1)` para o colapso da sidebar.

**Estados de interação**
- **Hover:** botões primary escurecem (`--primary-hover`); superfícies neutras vão para `--surface-2`; linhas de tabela ganham fundo `--surface-2`; bordas passam de `--border-strong` para `--faint`.
- **Press:** `translateY(.5px)` + cor mais escura (`--primary-press`). Sutil, tátil.
- **Active (nav):** a linha ativa lê `--nav-active-bg` / `--nav-active-fg` (custom properties na raiz da sidebar) + barra azul de 3px na borda esquerda. Na variante **escura** o fundo ativo é `--side-active-bg` com texto branco; nas variantes **clara (papel)** e **branca (lúcida)** vira `--primary-soft` + `--primary-ink`. A `Sidebar` aceita `variant="dark" | "light" | "white"` — veja as três opções lado a lado em `ui_kits/painel/menu-opcoes.html`. A variante **branca** ainda tinge os ícones de azul.

**Imagens & gráficos**
- Gráficos **sóbrios e limpos**: trilho em `--accent-soft`, preenchimento sólido `--accent`, tooltip nativo no hover. Sem eixos pesados, sem grid 3D, sem múltiplas cores berrantes. O dado fala; o gráfico cala.
- Fotografia (quando houver) é quente e terrosa, alinhada ao campo/pecuária. Sem filtros frios, sem preto-e-branco.

**Transparência & blur:** praticamente não usados. A linguagem é opaca e impressa. Overlays de modal usam véu escuro simples, sem vidro fosco.

---

## 4 · Iconografia (Iconography)

- **Sistema:** ícones de linha estilo **Lucide** — traço de ~1,9px, cantos arredondados (`stroke-linecap/linejoin: round`), grade 24px, renderizados a 18–20px na UI e 15–16px em ações de linha. **Cor padrão de ícone é o azul primário (`--primary`)** sempre que houver ênfase interativa; em contextos neutros herdam `currentColor` (tom do texto). Reserve laranja/vermelho no ícone apenas para marca e alerta.
- **Formato:** **SVG inline**, não PNG nem fonte de ícone. No design system eles vivem como pequenas funções helper (`Icons.*` em `ui_kits/painel/painel-shared.jsx`) que emitem o `<svg>` — fácil de copiar para qualquer artefato. Os dois redesigns originais usam exatamente esse padrão.
- **Substituição sinalizada:** não havia um arquivo de fonte/sprite de ícones no repositório (estava vazio), então adotamos o set **Lucide** como o mais próximo do que os redesigns já usavam (mesmo peso de traço e estilo). Se a FINPEC tiver um set proprietário, ele substitui este — **sinalizar ao time**.
- **Emoji:** nunca. **Unicode como ícone:** apenas setas de tendência (▲ ▼) dentro de chips de KPI e chevrons de paginação (‹ ›) — caso isolado e intencional.
- **Logo & marca:** ver `assets/` — monograma (chifres de boi estilizados em laranja-queimado) e wordmark "FINPEC" com tracking largo (`--ls-brand 0.18em`).

---

## 5 · Índice / Manifesto do projeto

**Raiz**
- `styles.css` — ponto de entrada único (só `@import`s). Consumidores linkam **este** arquivo.
- `readme.md` — este guia.
- `SKILL.md` — wrapper compatível com Agent Skills (Claude Code).
- `_ds_bundle.js`, `_ds_manifest.json`, `_adherence.oxlintrc.json` — **gerados automaticamente**, não editar.

**`tokens/`** — custom properties por concern, todas alcançadas via `styles.css`:
`fonts.css` (webfonts), `colors.css`, `typography.css`, `spacing.css` (espaçamento + radii + sombras + dims do shell), `base.css` (reset).

**`components/`** — primitivos React (`export function Name`), com `.d.ts`, `.prompt.md` e um card por pasta. Namespace de runtime: `window.FINPECDesignSystem_f2149b`.
- `core/` — **Button**, **IconButton**, **Card**, **Pill**, **Tag**, **Avatar**
- `forms/` — **Field**, **Input**, **Select**, **Checkbox**, **RadioCard**, **MoneyInput** (máscara valor/percentual)
- `data/` — **KpiCard** (com `prefix` monetário; tom padrão azul `primary`), **DataTable**, **BarChart** (azul), **Pager**
- `feedback/` — **Toast**, **EmptyState**
- `navigation/` — **Sidebar** (variantes `dark`, `light`, `white`; recolhível via `collapsed` + `onToggleCollapse`; indicadores por item via `badge`/`tone`/`dot`), **Topbar**
- `media/` — **ImageThumb** + **ImageThumbGrid** (thumbnails com legenda e badge), **ImageViewer** (zoom/pan + bounding boxes), **Lightbox** (visualização em tela cheia com navegação prev/next)
- `finpec-components.css` — camada de classes (`.fp-*`) por trás dos componentes; importada pelo `styles.css`.

**`guidelines/`** — cards de especímen (aba Design System): cores (marca, papel, tinta, semântica, chrome escuro), tipografia (display, UI, mono, escala), espaçamento (radii, sombras, escala), marca (logo, monograma).

**`ui_kits/`** — recriações navegáveis dos produtos:
- `painel/` — **Painel do Investidor/Admin**: `index.html` (app completo: login → dashboard → CPRFs → nova emissão), `painel-shared.jsx` (ícones + dados mock), `painel-screens.jsx` (telas).
- `foxe/` — **FOXEN-AI**: `index.html` (Início, Contagens IA, Revisão, Usuários; tema claro/escuro + accents), `foxe-theme.js`, `foxe-shared.jsx`, `foxe-screens.jsx`.
- `painel/menu-opcoes.html` — as três variantes de Sidebar lado a lado.

**`templates/painel-dashboard/`** — template DC copiável do dashboard do Painel.

**`assets/`** — `finpec-mark.png` (monograma), `finpec-logo.png`, `finpec-logo-preto.png`, `finpec-jornada-credito.jpg`.

---

## 6 · Como usar

1. Linke `styles.css` — tokens, fontes, reset e a camada `.fp-*` vêm juntos.
2. Carregue `_ds_bundle.js` e leia os componentes de `window.FINPECDesignSystem_f2149b`.
3. Componha telas com os primitivos (não reimplemente Button/Card dentro de uma tela). Veja `ui_kits/painel/` como referência de composição.
4. Ícones: copie os helpers `Icons.*` de `painel-shared.jsx` ou use SVGs Lucide com traço 1,9px.

> **Explore o repositório** https://github.com/FINPEC-JK/webresp_design-system (e o arquivo `uploads/Foxe IA - Redesign.dc.html`) para recriar telas FINPEC com mais fidelidade.

> **Substituições sinalizadas:** (a) fontes carregadas via Google Fonts (Schibsted Grotesk / Hanken Grotesk / IBM Plex Mono) — se a FINPEC tiver arquivos próprios, troque em `tokens/fonts.css`; (b) ícones Lucide no lugar de um set proprietário inexistente no repo.

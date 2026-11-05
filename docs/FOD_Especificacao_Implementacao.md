# FOD — Especificação de implementação do novo front-end

**Sistema:** FinPec On Demand (FOD) · **Versão funcional de referência:** Especificação V1 (16/10/2026)
**Referência visual:** `FOD Redesign.dc.html` (artboards `1a`–`1r`) · **Design system:** FINPEC Design System (`_ds/finpec-design-system-…/`)
**Data:** 28/10/2026 · **Uso interno FINPEC**

---

## 0. Como usar este documento

- Cada requisito tem um ID estável `FOD-IMP-NNNN`. Cite o ID em commits, PRs e testes.
- **Nível:** `DEVE` = obrigatório para aceite · `DEVERIA` = esperado, desvio precisa de justificativa · `PODE` = opcional.
- **Fonte** remete à ficha funcional (FOD 01–14) ou à seção comum da Especificação V1. **Artboard** remete à tela no arquivo de design.
- **Regra de ouro (FOD-IMP-0001):** esta refatoração muda só a interface. Nenhuma regra de negócio, campo, validação, permissão, cálculo ou rota pode ser alterada. Em caso de conflito entre este documento e a Especificação V1, vale a V1 e o conflito DEVE ser reportado.

---

## 1. Fundamentos

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0001 | DEVE | Preservar integralmente regras, campos, comandos, permissões, rotas (`/login`, `/dashboard`, `/bi`, `/demandas`, `/minhas-demandas`, `/demandas/nova`, `/demandas/{id}`, `/aprovacao-ceo`, `/demandas/exportar-priorizacao`, `/arquivo`, `/ranking`, `/minha-conta`, `/admin`) e verificações no servidor. |
| FOD-IMP-0002 | DEVE | Carregar `styles.css` do FINPEC DS (tokens de cor, tipografia, espaçamento, reset e camada `.fp-*`). Proibido definir cores, fontes, raios ou sombras fora dos tokens. |
| FOD-IMP-0003 | DEVE | Usar os primitivos do DS: `Button`, `IconButton`, `Card`, `Pill`, `Tag`, `Avatar`, `Field`, `Input`, `Select`, `Checkbox`, `RadioCard`, `KpiCard`, `DataTable`, `Pager`, `EmptyState`, `Toast`, `Sidebar`, `Topbar`. Não reimplementar equivalentes. |
| FOD-IMP-0004 | DEVE | Papel das cores: azul `--primary` = ação, link, seleção, foco, ícones; laranja `--accent` = marca e atenção (badge da fila de aprovação); vermelho `--danger` = só farol vermelho e ação destrutiva confirmada; verde `--ok` = sucesso/farol verde; âmbar `--warn` = farol amarelo, espera, avisos. Botões de "Cancelar" nunca vermelhos. |
| FOD-IMP-0005 | DEVE | Tipografia: Schibsted Grotesk (títulos, valores KPI), Hanken Grotesk (UI, corpo), IBM Plex Mono com `tabular-nums` (datas, aging, contagens, IDs, prioridades P0/P1/P2). Base 14px; tabelas 13px; mínimo 10,5px. |
| FOD-IMP-0006 | DEVE | Ícones SVG inline estilo Lucide, traço 1,9px, 18–20px na navegação e 14–16px em ações. Sem emoji, sem fonte de ícones. |
| FOD-IMP-0007 | DEVE | Português do Brasil. Números BR: `64,5%`, `5,8 dias`, `19,3`, datas `dd/mm/aaaa`, horas `HH:MM:SS`. Corrigir textos atuais sem acento ("Descricao", "Comentarios", "homologacao", "usuarios"). |
| FOD-IMP-0008 | DEVE | Rótulos de seção e cabeçalhos de tabela em MAIÚSCULAS com tracking largo; títulos de página em capitalização de frase. |
| FOD-IMP-0009 | DEVE | Substituir o avatar "FP" e a imagem quebrada do cabeçalho atual pelo monograma `assets/finpec-mark.png`. |
| FOD-IMP-0010 | DEVERIA | Animações apenas funcionais: 0,14s em hover/foco, `fpFadeIn` na entrada de conteúdo, `fpToastIn` em toasts. Sem bounce, sem parallax. |

## 2. Shell e navegação

Artboards: `1a` (opção A, recomendada), `1b` (opção B), `1d` (recolhida), `1e` (celular).

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0020 | DEVE | Shell em grade: sidebar escura (`Sidebar variant="dark"`) 264px + área principal (topbar 68px + conteúdo). Sidebar e conteúdo rolam de forma independente; a página nunca rola como um todo (`min-height:0`, `overflow:auto`, `overscroll-behavior:contain`). |
| FOD-IMP-0021 | DEVE | Grupos da sidebar por perfil (o servidor continua sendo a autoridade de acesso — Fonte: FOD 01, regras):<br>**CEO:** Visão executiva (Dashboard, BI, Ranking) · Demandas (Kanban geral, Minhas demandas, Arquivo) · Decisão CEO (Aprovação CEO, Priorizar em planilha) · Configurações (Usuários e diretorias, Minha conta).<br>**Diretor:** Visão executiva · Demandas · Ferramentas (Priorizar em planilha, só se habilitado) · Configurações (Minha conta).<br>**Colaborador:** Demandas · Indicadores (Ranking) · Configurações (Minha conta); Priorizar em planilha só se habilitado. |
| FOD-IMP-0028 | DEVE | O botão claro/escuro do rodapé do menu aplica o tema ao **sistema inteiro** (menu, topbar, conteúdo, cards, tabelas, formulários, Kanban, gráficos, painéis laterais, toasts), não só ao menu. Claro: tokens padrão do DS + sidebar branca. Escuro: sobrescrever os tokens de cor e sombra na raiz (`[data-theme="dark"]` em `<html>`) com a paleta escura quente (`--bg #12110E`, `--surface #1C1A16`, `--surface-2 #23211C`, `--border #2E2B25`, `--ink #F2EFE9`, `--muted #A8A194`, `--primary #4F82E0`, semânticos clareados) + sidebar escura. Nenhuma cor de interface fixa em hex fora da tela de login; séries dos gráficos trocam para as variantes claras. Preferência salva em `localStorage` (`fod-theme`) e aplicada antes da primeira pintura. A tela de login é sempre escura. |
| FOD-IMP-0027 | DEVE | Tudo o que for configuração (usuários, diretorias, perfis/permissões, conta) fica como subitem de um item "Configurações" do menu lateral, expansível/retrátil (`Sidebar` item com `children`), nunca no rodapé. |
| FOD-IMP-0022 | DEVE | Badges: Kanban geral = total de abertas visíveis ao usuário; Minhas demandas = total da lista; Aprovação CEO = fila pendente com tom `hot` (laranja). Badge some quando zero. Com a sidebar recolhida, itens com badge mostram o ponto indicador. |
| FOD-IMP-0023 | DEVE | Rodapé do menu lateral (regra FINPEC DS, `CLAUDE.md`): (1) toggle Claro/Escuro (só dois temas; lua = escuro, sol = claro) + data e hora ("29 de out. de 2026 · 02:31", atualiza a cada 30 s); (2) avatar com iniciais, nome, perfil · diretoria e botão **Sair**; (3) código do release, **só a versão** (ex.: `1.0.0`, mono 11,5px). Usar `Sidebar` com `theme`, `onToggleTheme`, `onLogout`, `showClock`, `release` na versão atual do DS. Recolhida: tema, avatar e Sair empilhados. |
| FOD-IMP-0024 | DEVE | Topbar com breadcrumb `On Demand / <página>` (detalhe: `On Demand / Kanban geral / #id`), botão primário **Nova demanda** (abre `/demandas/nova`). Sair fica no rodapé da sidebar (FOD-IMP-0023). "Criar demanda" deixa de ser item de menu. |
| FOD-IMP-0025 | DEVE | Sidebar recolhível via botão sanduíche (76px, só ícones, labels em `title`). Estado persistido em `localStorage`. |
| FOD-IMP-0026 | DEVE | Se a opção B (`1b`) for escolhida: topbar clara com Dashboard, BI, Demandas ▾ (Kanban geral, Minhas demandas, Arquivo), Ranking, Decisão CEO ▾ (Aprovação, Planilha); menu do avatar com Minha conta, Usuários e diretorias, Sair. As regras 0021–0024 valem com a mesma filtragem por perfil. |

## 2a. Layouts de entrega

O FOD tem **3 layouts**, todos do mesmo código e das mesmas regras:

| Layout | Onde roda | Navegação | Protótipo |
|---|---|---|---|
| **Web** | Navegador em tela cheia (monitor/notebook) | Menu lateral | Layouts → Web |
| **PWA** | App instalado na tela inicial, sem barra do navegador | Barra de abas inferior + painel "Mais" | Layouts → PWA |
| **Web responsive** | Navegador em qualquer largura | Desktop 1440: menu aberto · Notebook 1280 e Tablet horizontal 1180: menu recolhido (76px) · Tablet 834: menu recolhido · Celular 390: topo escuro + menu sanduíche (Mobile-web-responsive) | Layouts → Web responsive + largura |

Web e Web responsive são **o mesmo site**; a troca de navegação é automática pela largura (media query), nunca por detecção de aparelho. **Mobile-web-responsive** é a faixa < 768px do Web responsive. O PWA usa o mesmo código e troca a navegação pela barra de abas quando aberto instalado (`display-mode: standalone`).

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0290 | DEVE | Detectar o modo instalado por `@media (display-mode: standalone)` / `navigator.standalone` e aplicar o layout PWA; no navegador, aplicar Web ou Mobile-web-responsive pela largura. |
| FOD-IMP-0291 | DEVE | Mobile-web-responsive (Web responsive < 768px) permanece como hoje: topo escuro 56px com menu (gaveta 300px com rodapé padrão), título e botão Nova demanda; sem barra de abas e sem banner próprio de instalação. A instalação usa o aviso nativo do navegador. |
| FOD-IMP-0292 | DEVE | Web Manifest: `name` "FinPec On Demand", `short_name` "FOD", `start_url` "/", `display` "standalone", `orientation` "portrait" (exceto gráfico expandido), `background_color` "#0F0F0E", `theme_color` acompanhando o tema (claro `#FFFFFF`, escuro `#1C1A16`), ícones 192/512px e maskable gerados do monograma FINPEC sobre `#0F0F0E`. |
| FOD-IMP-0293 | DEVE | Splash: fundo `#0F0F0E`, monograma, "FOD" e "FINPEC ON DEMAND" (gerado do manifest; no iOS, `apple-touch-startup-image`). |
| FOD-IMP-0294 | DEVE | Respeitar áreas seguras (`env(safe-area-inset-*)`, `viewport-fit=cover`): status bar na cor do topo do app, barra de abas acima do indicador de início. |
| FOD-IMP-0295 | DEVE | Topo PWA 52px em `--surface`: Voltar (só em Detalhe e Nova demanda), título da tela, indicador "Sincronizado"/"Offline" e avatar (abre Minha conta). |
| FOD-IMP-0296 | DEVE | Barra de abas inferior (alvos ≥ 44px; item ativo com cápsula `--primary-soft` e cor `--primary`) por perfil — CEO: Início, Kanban, Aprovação (badge `--accent` com a fila), Minhas, Mais · Diretor: Início, Kanban, Minhas, BI, Mais · Colaborador: Kanban, Minhas, Ranking, Mais. |
| FOD-IMP-0297 | DEVE | "Mais" abre painel inferior com as demais telas permitidas e o rodapé padrão (tema + data/hora, usuário + Sair, release). Fecha arrastando para baixo, tocando no véu ou ao navegar. |
| FOD-IMP-0298 | DEVE | Botão flutuante **Nova demanda** (56px, raio 16px, `--primary`) acima da barra de abas em Início, Kanban e Minhas. |
| FOD-IMP-0299 | DEVE | Service worker: cache do shell (HTML, CSS, JS, fontes, ícones) e da última leitura das listas; dados sempre buscados da rede primeiro. Sem conexão: faixa `--warn-soft` "Sem conexão · exibindo dados salvos às HH:MM. Ações que gravam ficam indisponíveis.", indicador "Offline" no topo e ações de gravação bloqueadas com aviso (nenhuma fila de envio offline na V1). |
| FOD-IMP-0300 | DEVERIA | Notificações push (com permissão do usuário) para: nova demanda aguardando aprovação (CEO), demanda delegada (colaborador), solicitação ao demandante e devolução. Depende de back-end. |
| FOD-IMP-0301 | DEVE | Ao publicar nova versão, avisar "Nova versão disponível · Atualizar" e recarregar sob demanda; o código do release no rodapé reflete a versão ativa. |

## 3. Responsividade

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0030 | DEVE | Breakpoints: **XL** ≥1440 · **L** 1280–1439 · **M** 1024–1279 · **S** 768–1023 · **XS** <768. |
| FOD-IMP-0031 | DEVE | XL: sidebar aberta por padrão. L e M: sidebar **recolhida por padrão** (o usuário pode abrir; a preferência salva prevalece). S: sidebar recolhida. XS: layout Mobile-web-responsive (topo escuro + gaveta). Instalado: layout PWA (seção 2a). |
| FOD-IMP-0032 | DEVE | Conteúdo com `max-width` (1320px listas/painéis; 1180px formulários de duas colunas; 820px Minha conta), centralizado. Nunca largura fixa em px para containers de texto. |
| FOD-IMP-0033 | DEVE | Grades com `repeat(auto-fit, minmax(…, 1fr))` ou `minmax(0,1fr)`; nenhum card com altura fixa que contenha texto. |
| FOD-IMP-0034 | DEVE | Layouts de duas colunas (Detalhe, Aprovação, Nova demanda) empilham abaixo de 1024px: coluna de ações vai para baixo do resumo no Detalhe; a fila vai para cima da decisão na Aprovação. |
| FOD-IMP-0035 | DEVE | Tabelas largas (Ranking, Admin) rolam horizontalmente dentro do card abaixo de M, com a primeira coluna fixa (`position:sticky; left:0`). |
| FOD-IMP-0036 | DEVE | Alvos de toque ≥44px em S e XS. |

## 4. Componentes de domínio (compostos sobre o DS)

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0040 | DEVE | **StatusPill** (Pill) — mapeamento fixo de etapa → tom: Aguardando aprovação CEO `neutral` · Aprovada / Pendente `info` · Em andamento `info` com ponto · Aguardando terceiros `warn` · Concluída `ok` · Recusada `danger` · Cancelada `neutral`. Cor de ponto da coluna no Kanban: faint, info, primary, warn, ok, danger, faint. |
| FOD-IMP-0041 | DEVE | **FarolPill** (Pill com `dot`) — Verde `ok`, Amarelo `warn`, Vermelho `danger`. O cálculo do farol permanece no servidor (P0 ≤14 / 15–21 / >21; P1 ≤30 / 31–45 / >45; P2 ≤45 / 46–75 / >75; farol legado P0 antes de 17/07/2026). |
| FOD-IMP-0042 | DEVE | **PrioridadeChip** — texto mono branco sobre `--ink`, raio 5px: `P0`, `P1`, `P2`. |
| FOD-IMP-0043 | DEVE | **DemandCard** (Kanban) — ver FOD-IMP-0102. |
| FOD-IMP-0044 | DEVE | **PageHeader** — título 27px display, subtítulo 14px `--muted`, slot de ações à direita alinhado à base; quebra para baixo em S/XS. |
| FOD-IMP-0045 | DEVE | **ChartCard** — título, subtítulo, ações Copiar gráfico / Baixar PNG (ícone + texto em cards largos, só ícone com `aria-label` e `title` em cards de meia largura), linha de período + legenda com valor atual, gráfico SVG, rodapé "Ver dados da série" (expande tabela). |
| FOD-IMP-0046 | DEVE | **SidePanel** (gaveta à direita, 480px, véu `rgba(24,22,18,.42)`, `--shadow-lg`) — cabeçalho com título e fechar, corpo rolável, rodapé fixo com ação destrutiva à esquerda e Cancelar/Salvar à direita. Fecha com Esc e clique no véu; foco preso dentro do painel. |
| FOD-IMP-0047 | DEVE | **DangerConfirm** — bloco com borda `--danger-soft`, texto explicando a consequência, campo de confirmação e botão `danger` desabilitado até a frase exata. |
| FOD-IMP-0048 | DEVE | **EmptyState** do DS para listas vazias, com texto factual (ex.: "Nenhuma demanda aguardando aprovação."). |
| FOD-IMP-0050 | DEVE | **FlowStepper** ("Etapas da demanda") — card com título à esquerda e etiqueta contornada em `--primary` com a etapa atual à direita. Trilha horizontal: Aguardando aprovação CEO → Aprovada / Pendente → Em andamento → Concluída, mais o terminal Recusada / Cancelada (×) ligado por conector tracejado. Etapa concluída: círculo 26px `--primary` com ✓ branco e conector sólido `--primary`; atual: círculo 28px branco, borda 2,5px `--primary`, número mono e halo `--primary-soft`, rótulo em negrito `--primary-ink`; futura: círculo 22px com borda `--border-strong` e número `--muted`. Sob cada etapa: rótulo, data/hora (mono, `dd/mm, HH:MM`) e autor da transição, ou "—". Esperas por terceiros aparecem como nota âmbar sob Em andamento ("2 esperas por terceiros"). Rodapé opcional com as transições permitidas: secundária, primária (avanço) e destrutiva em texto `--danger`. Abaixo de 768px vira lista vertical. |
| FOD-IMP-0049 | DEVE | Toasts (DS `Toast`) para confirmação no particípio: "Demanda criada e enviada para aprovação", "Alterações salvas", "Demanda delegada", "Envio ao FES autorizado", "Gráfico copiado". Erros de validação aparecem no campo, não em toast. |

## 5. Telas

### FOD 01 · Acesso — `/login` · protótipo (tela inicial)

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0060 | DEVE | Usar a estrutura padrão de login FINPEC (`LoginScreen` do DS): painel escuro `#0F0F0E` de 500px à esquerda (sempre escuro, independente do tema) + imagem full-bleed à direita (foto institucional FINPEC fornecida; na implementação, servir como asset local `assets/login-fod.jpg`, sem depender de URL externa). Abaixo de 768px, só o painel em largura total. |
| FOD-IMP-0061 | DEVE | Painel: logo branco FINPEC 104px (`assets/finpec-logo-branco.png`) + "FOD" (26px display) e "FINPEC ON DEMAND" (13px, maiúsculas); título 38px "Sistema institucional de demandas"; descrição 16px; bloco de acesso no rodapé do painel com "Acesse sua conta" + linha de apoio. |
| FOD-IMP-0062 | DEVE | `methods="both"`: botão branco **Entrar com Microsoft** (logo Microsoft, largura total, 68px) no topo; divisor "OU"; depois E-mail e Senha (inputs escuros 48px, `#1A1917`, borda `#34322D`) + Mostrar/Ocultar + **Entrar** `--primary` 52px. Falha mantém na tela com erro acima do botão. O SSO depende de integração com Microsoft Entra ID no back-end. |
| FOD-IMP-0063 | DEVE | Aba vertical "Release notes" presa à borda esquerda da imagem (40×400px, branca, borda 2px `--primary`, texto "{versão} Release notes" girado); clique expande painel 400×400 "Release notes — Build {versão}" com seções `<details>`: Correções de validação, Evolução da plataforma, Requisitos funcionais, Requisitos não funcionais. Aviso de homologação só fora de produção. |

### FOD 02 · Dashboard — `/dashboard` · artboard `1a`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0070 | DEVE | Acesso CEO e diretores. Filtro Diretoria (Select) + **Aplicar filtro** no PageHeader. |
| FOD-IMP-0071 | DEVE | Faixa "Volume" em um único card com 5 células (Criadas, Aprovadas, Recusadas, Concluídas, Abertas), cada uma com link "Ver demandas →" para a lista filtrada correspondente (mesmo destino atual). |
| FOD-IMP-0072 | DEVE | 5 `KpiCard`: Aging médio aberto (dias), Tempo médio de resolução (dias), % concluído, % concluídas no verde, % abertas no vermelho — com linha de rodapé de contexto. Clique mantém a navegação atual. |
| FOD-IMP-0073 | DEVE | Card **Aguardando terceiros** em largura total: cabeçalho com nota "aging da diretoria fica pausado" + Pill `warn` "Aging pausado"; faixa com 3 células (Demandas aguardando, Aging médio do demandante, Maior espera atual em `--warn-ink`), cada uma com "Ver demandas →"; abaixo, duas quebras lado a lado: **Demandas por demandante** e **Aging médio por demandante** (top 5, ordenadas desc.). |
| FOD-IMP-0076 | DEVE | **BreakdownList** — lista de quebra usada no Dashboard: linha com rótulo (reticenciado), barra horizontal 6px proporcional ao maior valor da lista (trilho `--surface-2`) e valor mono à direita; total no cabeçalho do card quando fizer sentido. Cor da barra por semântica: volume `--primary`, espera `--warn`, vencidas `--danger`, resolução `--ok`. Clique na linha leva à lista filtrada correspondente. |
| FOD-IMP-0077 | DEVE | Seção **Distribuição** (grade 3 colunas, `auto-fit minmax(300px,1fr)`): Demandas por diretoria, Demandas por prioridade (P0, P1, P2, Aguardando prioridade), Backlog por diretoria. |
| FOD-IMP-0078 | DEVE | Seção **Prazo e resolução** (mesma grade): Vencidas por prioridade, Vencidas por diretoria, Resolução média por diretoria (dias, uma casa decimal com vírgula). |
| FOD-IMP-0074 | DEVE | Última linha em grade 1,4fr / 1fr: card **Abertas no vermelho** (até 5 linhas: demanda, etapa, aging; link "Abrir no Kanban →") e card **Ranking de execução** resumido (posição, diretoria com % concluído · % verde · aging em mono, score com barra, vermelhas; link "Ver ranking →"). |
| FOD-IMP-0075 | DEVE | Manter as regras: base histórica completa; Kanban retira concluídas após 7 dias e recusadas após 3 dias, portanto contagens podem divergir; aging desconta espera. |

### FOD 03 · BI — `/bi` · artboard `1l`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0080 | DEVE | PageHeader com sobretítulo "VISÃO EXECUTIVA" (azul), Tag "Últimas 12 semanas", filtro Diretoria + Aplicar filtro. |
| FOD-IMP-0081 | DEVE | Aviso `warn-soft` quando houver histórico incompleto/estimado, citando semanas seg–dom em Brasília e semana atual parcial. |
| FOD-IMP-0082 | DEVE | 5 `KpiCard`: Estoque atual, Aging médio aberto, Percentual entregue, Criadas nesta semana, Entregues nesta semana, com comparação ao fechamento anterior. |
| FOD-IMP-0083 | DEVE | 6 ChartCards na ordem: Criadas × entregues (largura total), Aging + Estoque (meia), Percentual entregue (total), Criadas na semana + Entregues na semana (meia). Abaixo de M, todos em largura total. |
| FOD-IMP-0084 | DEVE | Cores de série: Criadas `#2860C6` (primary, linha contínua/barras), Entregues `#4C9A5E` (ok, tracejada no comparativo/barras), Aging `#B07206` (warn, área 8%), Estoque `#4A453D` (ink-2, área 8%), Percentual `#4C9A5E` (área 8%). Grade tracejada `--border`, rótulos mono `--faint`. |
| FOD-IMP-0085 | DEVE | Semana atual: faixa `#F1EEE8` atrás do último ponto, rótulo "Atual" em negrito `--ink`, barra com 45% de opacidade. Sem uso de laranja nos eixos. |
| FOD-IMP-0086 | DEVE | Consulta de pontos por mouse, toque e setas do teclado (gráfico focável, `role="img"` + `aria-label` com resumo); tooltip escuro `--ink` com semana e valores. "Ver dados da série" expande `DataTable` dense. |
| FOD-IMP-0089 | DEVE | Todo gráfico tem botão-ícone **Expandir** (`aria-label="Expandir gráfico"`) no cabeçalho do card. Web: abre em modal grande (até 1200×640px) com título, período, Baixar PNG e Fechar; Esc e clique no véu fecham. Mobile: abre em tela cheia **na horizontal** (painel girado 90° ocupando a tela; usar `screen.orientation.lock('landscape')` quando disponível), com o gráfico redesenhado na largura maior. |
| FOD-IMP-0087 | DEVE | Copiar gráfico gera imagem com período e área; se a cópia não for suportada, oferecer download. Baixar PNG em todas as 6 séries. |
| FOD-IMP-0088 | DEVE | Card "Como ler estes indicadores" com as 6 definições da V1 (Estoque, Aging médio aberto, Percentual entregue, Criadas na semana, Entregues na semana, Criadas × entregues). Aberto no primeiro acesso, estado lembrado. |

### FOD 04 · Kanban geral — `/demandas` · artboards `1c`, `1d`, `1e`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0100 | DEVE | Etapas na ordem: Aguardando aprovação CEO, Aprovada / Pendente, Em andamento, Aguardando terceiros, Concluída, Recusada, Cancelada — respeitando a visibilidade de cada perfil. |
| FOD-IMP-0101 | DEVE | **Colunas com largura fixa de 296px** (`flex:0 0 296px`); o quadro rola na horizontal e cada coluna rola na vertical dentro da altura disponível (`height: calc(100vh - topbar - cabeçalho)`). Proibido reduzir a largura do card para caber na tela. |
| FOD-IMP-0102 | DEVE | Card: linha 1 = posição na fila (mono, "1º na fila") + FarolPill à direita; linha 2 = título completo (13,5px semibold, quebra livre, `overflow-wrap:anywhere`); linha 3 = "Diretoria · Solicitante" (12px muted); rodapé com divisória = um de: "Aging N dias" (ícone relógio) · "Pausado · demandante N dias" (Aguardando terceiros) · "Concluída em dd/mm/aaaa" (verde). Prioridade não se repete no card (está no cabeçalho do grupo). Status não se repete (está na coluna). |
| FOD-IMP-0103 | DEVE | Dentro de cada coluna, cards agrupados por prioridade com cabeçalho "P0 · 11 na fila" (PrioridadeChip + contagem). |
| FOD-IMP-0104 | DEVE | **Etapas recolhíveis/expansíveis.** Padrão: etapa **sem demandas vem recolhida** (trilho de 44px com botão expandir »·, ponto da etapa, contagem e nome vertical); etapa com demandas vem expandida. Clicar no trilho expande a coluna (296px); o botão « no cabeçalho da coluna a recolhe. Coluna vazia expandida mostra "Nenhuma demanda nesta etapa.". A escolha do usuário vale para a sessão e volta ao padrão quando a contagem muda de zero para positivo. `aria-expanded` no controle. |
| FOD-IMP-0105 | DEVE | Barra de etapas acima do quadro: chips com ponto, nome e contagem; clique rola o quadro até a coluna (`scrollTo`, não `scrollIntoView`). Nota "Concluídas saem após 7 dias e recusadas após 3 dias · Arquivo" à direita. |
| FOD-IMP-0106 | DEVE | Busca por coluna: ícone lupa no cabeçalho abre campo "Filtrar por título nesta etapa" com foco; Esc ou × limpa e fecha. Filtrar não altera status. |
| FOD-IMP-0107 | DEVE | Ordenação (só usuários habilitados): alça de arraste e botões ↑/↓ aparecem no hover/foco do card, no rodapé à direita; ↑ desabilitado no primeiro, ↓ no último. Movimento restrito à mesma etapa e prioridade; não altera prioridade oficial. Para não habilitados, nenhum controle de ordenação é renderizado e o indicador "Ordenação habilitada" no cabeçalho some. |
| FOD-IMP-0108 | DEVE | Arraste com teclado: foco na alça + Espaço para pegar, ↑/↓ para mover, Espaço para soltar, Esc cancela; anunciar posição via `aria-live`. |
| FOD-IMP-0109 | DEVE | Legenda do farol (Verde, Amarelo, Vermelho) no PageHeader. Clique no card abre `/demandas/{id}`. |
| FOD-IMP-0110 | DEVE | L/M (`1d`): sidebar recolhida, cabeçalho compacto de uma linha (título + "N abertas · N no vermelho"), card sem a linha de diretoria (solicitante à esquerda, aging mono à direita), colunas permanecem 296px. |
| FOD-IMP-0111 | DEVE | XS (Mobile-web-responsive e PWA): uma etapa por vez — botões ‹ › (44px) + seletor da etapa com contagem + carrossel horizontal de chips. **Ao trocar a etapa (setas, seletor, chip ou swipe), o chip ativo é centralizado no carrossel** com rolagem suave (`scrollTo`, nunca `scrollIntoView`); ao abrir a tela, centraliza sem animação. Cards em largura total; etapa vazia mostra EmptyState. |
| FOD-IMP-0112 | DEVERIA | Coluna com mais de 30 cards renderiza virtualizada. |

### FOD 05 · Minhas demandas — `/minhas-demandas` · artboard `1f`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0120 | DEVE | Alternador Quadro / Lista no PageHeader; padrão **Lista**, preferência salva. Quadro reutiliza o Kanban (FOD-IMP-0100–0111) com a carteira do usuário. |
| FOD-IMP-0121 | DEVE | Abas: Todas · Que solicitei · Atribuídas a mim, com contagens. |
| FOD-IMP-0122 | DEVE | Lista com colunas: Demanda (título + diretoria), Seu papel (Tag Solicitante/Executor), Etapa (StatusPill), Prior., Aging · farol, Responsável. Linha inteira clicável para o detalhe. |
| FOD-IMP-0123 | DEVE | Nota de rodapé: devolução de Aguardando terceiros é feita no detalhe; concluídas saem após 7 dias (link para Arquivo). |

### FOD 06 · Cadastro de demanda — `/demandas/nova` · artboard `1g`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0130 | DEVE | Diretoria solicitada como grupo de `RadioCard` (nome + diretor associado ou "Sem diretor associado"), 2 colunas; obrigatória. Com mais de 6 diretorias, usar Select. |
| FOD-IMP-0131 | DEVE | Título (obrigatório, mín. 3) e Descrição / motivo (obrigatória, mín. 10, contador de caracteres que fica verde ao atingir o mínimo). Comentários complementares opcional. |
| FOD-IMP-0132 | DEVE | Aviso fixo "O envio de anexos ainda não está disponível." |
| FOD-IMP-0133 | DEVE | Rodapé: Cancelar (ghost, volta) e **Criar demanda** (primary), desabilitado até o formulário ser válido. Sucesso → toast e redireciona para o detalhe. |
| FOD-IMP-0134 | DEVE | FlowStepper "Caminho da demanda" acima do formulário, com Aguardando aprovação CEO como etapa atual ("ao criar") e as demais futuras; sem ações. Formulário em coluna única (máx. 980px). |
| FOD-IMP-0135 | DEVE | Sem seleção de executor nem de prioridade pelo solicitante. |

### FOD 07 · Detalhe e operação — `/demandas/{id}` · artboard `1h`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0140 | DEVE | Cabeçalho: StatusPill, PrioridadeChip, FarolPill com aging ("Vermelho · 34 dias"), situação FES em texto; título 26px; "Diretoria · solicitada por X"; ação **Editar demanda** (secondary) quando autorizado. |
| FOD-IMP-0141 | DEVE | Grade em 2 colunas: leitura (`minmax(0,1fr)`) + ações (380px, `position:sticky; top:16px`). |
| FOD-IMP-0142 | DEVE | Card Resumo em grade 4×2: Responsável, Aging da diretoria, Criada em, Aprovada em, Espera acumulada, Resolução, Concluída em, referência do farol. Valores ausentes como "—" em `--faint`. |
| FOD-IMP-0143 | DEVE | Card Descrição / motivo + Comentários complementares (texto, não campo). |
| FOD-IMP-0144 | DEVE | Card Comentários: lista cronológica com avatar, autor, data/hora mono e Pill de tipo quando o comentário vier de transição ("Solicitação ao demandante" `warn`, "Devolução do demandante" `info`, "Auditoria de edição" `neutral`). Compositor com textarea + Enviar (desabilitado vazio). Só aparece para quem pode comentar. |
| FOD-IMP-0145 | DEVE | FlowStepper (FOD-IMP-0050) em largura total entre o cabeçalho e as colunas, com as transições permitidas ao usuário no rodapé (ex.: Iniciar execução, Solicitar dados ao demandante, Finalizar entrega; CEO: Cancelar demanda em texto `--danger`, com justificativa mín. 5). "Solicitar dados" abre campo de motivo (mín. 5). Para o solicitante em Aguardando terceiros: campo de resposta (mín. 3) + **Devolver**. |
| FOD-IMP-0146 | DEVE | Card **Delegação** (CEO e diretor da diretoria; etapas Aprovada/Pendente, Em andamento, Aguardando terceiros): Select de colaboradores ativos da mesma diretoria com e-mail + **Delegar**; **Retornar ao diretor** quando delegada. Conflito concorrente → mensagem "A demanda foi alterada. Atualize a página e tente novamente." |
| FOD-IMP-0147 | DEVE | Card **Integração com FES**: Pill de situação (Não sincronizada `neutral`, Enviada `info`, Erro `danger`), texto do que será enviado e **Autorizar envio ao FES** (só CEO, só Em andamento); "Tentar novamente" quando permitido. |
| FOD-IMP-0148 | DEVE | Card **Controles do CEO**: segmento P0/P1/P2 + Alterar (habilita ao mudar) e DangerConfirm "Excluir demanda" exigindo exatamente `EXCLUIR DEMANDA`. |
| FOD-IMP-0149 | DEVE | Card **Histórico**: linha do tempo (mais recente no topo) com ponto na cor da etapa, etapa, autor e data/hora; inclui delegações (anterior → novo, quem atribuiu). |
| FOD-IMP-0150 | DEVE | Cada card de ação só é renderizado se o usuário tiver a autorização correspondente (FOD permissões). |

### FOD 08 · Edição de demanda — formulário em `/demandas/{id}` · artboard `1i`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0160 | DEVE | **Editar demanda** substitui, no mesmo lugar, o card de Descrição por um card de edição com borda `--primary` + anel de foco; foco vai para o Título. |
| FOD-IMP-0161 | DEVE | Campos: Título (mín. 3), Descrição / motivo (mín. 10), Comentários complementares, Diretoria solicitada (editável só pelo CEO ou solicitante antes da aprovação; desabilitada com hint para diretor/executor). |
| FOD-IMP-0162 | DEVE | Campos alterados recebem selo "Alterado" (`warn-soft`); cabeçalho mostra "N campos alterados". |
| FOD-IMP-0163 | DEVE | Rodapé com nota de auditoria e Cancelar / **Salvar alterações**. Cancelar com alterações pede confirmação. Ações da coluna direita ficam inativas durante a edição. |

### FOD 09 · Aprovação do CEO — `/aprovacao-ceo` · artboard `1j`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0170 | DEVE | Exclusivo do CEO. Layout fila (380px) + painel de decisão. Item selecionado com fundo `--primary-soft` e barra azul de 3px à esquerda. O painel abre com o FlowStepper (FOD-IMP-0050) da demanda selecionada, etapa atual Aguardando aprovação CEO, sem ações (a decisão fica no rodapé do painel). |
| FOD-IMP-0171 | DEVE | Painel: StatusPill, título, solicitante, data/hora, diretoria proposta, descrição; Diretoria de destino (Select, ajustável); Prioridade por 3 `RadioCard` com os limites do farol; Justificativa (textarea, mín. 5). |
| FOD-IMP-0172 | DEVE | Rodapé: Cancelar (ghost) e Recusar (secondary) habilitam só com justificativa válida; **Aprovar como Pn** (primary) habilita só com prioridade escolhida. Após decidir, o próximo item da fila é selecionado automaticamente. |
| FOD-IMP-0173 | DEVE | Fila vazia: EmptyState "Nenhuma demanda aguardando aprovação." e badge da sidebar oculto. |

### FOD 10 · Planilha para priorização — `/demandas/exportar-priorizacao` · artboard `1k`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0180 | DEVE | Dois cards numerados: **1 Gerar planilha** (Diretoria obrigatória, data inicial opcional `dd/mm/aaaa`, **Baixar planilha para Excel**) e **2 Registrar devolução jurídica**. Cada card só aparece com a liberação correspondente. |
| FOD-IMP-0181 | DEVE | Devolução com área de soltar arquivo (aceita `.xlsx`; clique abre seletor), nome do arquivo após seleção e **Registrar devolução** habilitado só com arquivo. |
| FOD-IMP-0182 | DEVE | Regras visíveis antes do envio: rodada gerada pelo FOD, mesma diretoria, sem duplicidade; grava documentação, ordem, situação jurídica e data sem alterar status/prioridade; abas anteriores preservadas. Erros de validação listados por linha da planilha. |
| FOD-IMP-0183 | DEVERIA | Exibir contagem prévia "N demandas abertas em <diretoria>" ao escolher a diretoria. |

### FOD 11 · Arquivo de concluídas — `/arquivo` · artboard `1n`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0190 | DEVE | Tabela (não grade de cartões): Demanda (uma linha com reticências + `title`), Diretoria, Solicitante, Prior., Resolução, Concluída em (ordenação padrão decrescente). Linha clicável para o detalhe. |
| FOD-IMP-0191 | DEVE | Paginação com `Pager` do DS (25 por página). Critério: status Concluída; recusadas e canceladas não entram. |

### FOD 12 · Ranking — `/ranking` · artboard `1m`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0200 | DEVE | Ranking em **tiles** (não tabela), ordenados do 1º ao último, em grade `repeat(auto-fill, minmax(min(100%,360px),1fr))`. Cada tile: posição (quadrado 48px; 1º em `--primary` com texto branco e borda do tile `--primary`), diretoria + diretor, Score (28px display) com barra 0–100, e grade 3 colunas com % concluído, % no verde, Aging aberto, Vermelhas, P0 vermelhas (`--danger-ink` quando > 0). |
| FOD-IMP-0201 | DEVE | Card "Como a pontuação é calculada" com a fórmula da V1 em mono e a nota "Resultado mínimo zero". Sem filtro de diretoria. |
| FOD-IMP-0202 | DEVE | Formatação BR em todos os números (19,3 · 12,8% · 22,5 dias). |

### FOD 13 · Minha conta — `/minha-conta` · artboard `1p`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0210 | DEVE | Card Identificação: Nome (único editável, obrigatório); E-mail, Perfil e Diretoria como texto somente leitura em bloco `--surface-2`, com nota "mantidos pela administração". |
| FOD-IMP-0211 | DEVE | Card Alterar senha (opcional): Nova senha + Confirmar, cada um com Mostrar; mín. 8 e iguais; ambos vazios mantêm a senha. Erros inline. |
| FOD-IMP-0212 | DEVE | **Salvar** único; toast "Alterações salvas". Não solicitar senha atual (comportamento publicado). |

### FOD 14 · Usuários e diretorias — `/admin` · artboards `1q`, `1r`

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0220 | DEVE | Exclusivo do CEO. PageHeader com **Nova diretoria** (secondary) e **Novo usuário** (primary). Remover a frase "Exclusão não é exposta na interface principal" (inconsistente com a V1). |
| FOD-IMP-0221 | DEVE | Tabela Usuários: Usuário (avatar, nome, e-mail), Perfil (Pill: CEO `accent`, Diretor `info`, Colaborador `neutral`; valores internos CEO/DIRECTOR/EMPLOYEE), Diretoria, Status, Planilha, Devolução, Ordenação (✓ em `--primary-soft` ou "—"), ações Editar + menu "…" (Excluir). Nenhuma coluna cortada: rolagem horizontal interna se necessário. |
| FOD-IMP-0222 | DEVE | Tabela Diretorias: Diretoria, Diretor associado, Situação, Vínculo FES, Editar. |
| FOD-IMP-0223 | DEVE | Criar/editar usuário em SidePanel: Nome, E-mail, Senha inicial (obrigatória na criação; "Redefinir senha" opcional na edição), Perfil (3 RadioCards), Diretoria (Select), Permissões operacionais (3 Checkbox independentes: Gerar planilha de priorização, Registrar devolução jurídica, Ordenar o Kanban), Conta ativa (Checkbox). |
| FOD-IMP-0224 | DEVE | Criar/editar diretoria em SidePanel: Nome, Diretor associado (Select com "Sem diretor associado"), Situação, vínculo FES quando disponível. |
| FOD-IMP-0225 | DEVE | Excluir usuário/diretoria no rodapé do painel (texto `--danger`) e no menu "…", sempre com confirmação. Ocultar para a própria conta do CEO e para registros com vínculos; exibir motivo em tooltip. |

## 6. Estados, carregamento e erros

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0240 | DEVE | Carregamento: skeletons com a forma final (cards, linhas de tabela, colunas do Kanban); nunca spinner de página inteira. |
| FOD-IMP-0241 | DEVE | Erro de rede: card com mensagem factual e "Tentar novamente"; dados já exibidos permanecem. |
| FOD-IMP-0242 | DEVE | Botões de submissão mostram estado ocupado e ficam desabilitados durante a requisição; impedir duplo envio. |
| FOD-IMP-0243 | DEVE | Validação inline no `Field` (hint vira erro em `--danger-ink`) ao sair do campo e ao submeter; foco no primeiro campo inválido. |
| FOD-IMP-0244 | DEVE | 403 em rota interna: tela "Você não tem acesso a esta página" com link para o Kanban; não depender só do menu (Fonte: FOD 01). |

## 7. Acessibilidade

| ID | Nível | Requisito |
|---|---|---|
| FOD-IMP-0260 | DEVE | Contraste mínimo 4,5:1 em texto; farol nunca só por cor (sempre com rótulo "Verde/Amarelo/Vermelho"). |
| FOD-IMP-0261 | DEVE | Foco visível com `--focus-ring` em todos os interativos; ordem de tabulação segue a leitura (leitura → ações no Detalhe). |
| FOD-IMP-0262 | DEVE | `aria-label` em botões só de ícone (lupa da coluna, ↑/↓, copiar, baixar, fechar, "…"). |
| FOD-IMP-0263 | DEVE | Sidebar como `nav` com `aria-current="page"` no item ativo; badges com texto acessível ("42 abertas"). |
| FOD-IMP-0264 | DEVE | Gráficos com alternativa em tabela ("Ver dados da série") e navegação por teclado (FOD-IMP-0086). |

## 8. Critérios de aceite gerais

1. Em 1280×720 e 1366×768, nenhum card do Kanban tem menos de 296px de largura e nenhum título é cortado; etapas vazias abrem recolhidas.
2. Em 1440×900, o Dashboard mostra Volume, KPIs e Aguardando terceiros sem rolagem horizontal.
3. Em 390×844 (Mobile-web-responsive e PWA), todas as telas são operáveis sem zoom; o Kanban mostra uma etapa por vez e centraliza o chip ativo ao trocar de etapa.
4. Em 834×1112 (tablet), o menu abre recolhido e as tabelas largas rolam dentro do card, sem quebrar o layout.
5. Trocar o perfil (CEO, Diretor, Colaborador) altera menu, abas do PWA e cards de ação conforme a tabela de permissões da V1; rotas não autorizadas retornam FOD-IMP-0244.
6. O modo escuro se aplica a todas as telas (exceto o login, que é sempre escuro) e a preferência persiste após recarregar.
7. Todo gráfico abre expandido; no celular, na horizontal.
8. O PWA instala com nome, ícone e splash corretos, abre sem a barra do navegador e, sem conexão, exibe o aviso e bloqueia as gravações.
9. Nenhuma cor fora dos tokens do DS no CSS final (verificável por lint).
10. Testes de regressão funcional da V1 passam sem alteração.

## 9. Rastreabilidade

| Ficha V1 | Rota | Artboard / protótipo | Requisitos |
|---|---|---|---|
| FOD 01 Acesso | `/login` | 1o · protótipo (tela inicial) | 0060–0063 |
| FOD 02 Dashboard | `/dashboard` | 1a (1b) | 0070–0078 |
| FOD 03 BI | `/bi` | 1l | 0080–0089 |
| FOD 04 Kanban geral | `/demandas` | 1c, 1d, 1e | 0100–0112 |
| FOD 05 Minhas demandas | `/minhas-demandas` | 1f | 0120–0123 |
| FOD 06 Cadastro | `/demandas/nova` | 1g | 0130–0135 |
| FOD 07 Detalhe | `/demandas/{id}` | 1h | 0140–0150 |
| FOD 08 Edição | `/demandas/{id}` | 1i | 0160–0163 |
| FOD 09 Aprovação CEO | `/aprovacao-ceo` | 1j | 0170–0173 |
| FOD 10 Planilha | `/demandas/exportar-priorizacao` | 1k | 0180–0183 |
| FOD 11 Arquivo | `/arquivo` | 1n | 0190–0191 |
| FOD 12 Ranking | `/ranking` | 1m · protótipo (tiles) | 0200–0202 |
| FOD 13 Minha conta | `/minha-conta` | 1p | 0210–0212 |
| FOD 14 Administração | `/admin` | 1q, 1r | 0220–0225 |
| Shell, menu, tema, Configurações | — | todos | 0020–0028 |
| Layouts Web · PWA · Web responsive | — | protótipo (barra de tweaks) | 0030–0036, 0290–0301 |
| Comum (fundamentos, componentes, estados, acessibilidade) | — | todos | 0001–0010, 0040–0050, 0240–0264 |

## 10. Pendências para decisão

- **Navegação:** opção A (sidebar, `1a`) ou B (topbar, `1b`). O protótipo e este documento assumem A; FOD-IMP-0026 cobre B.
- **Versão do release:** `1.0.0` é provisória (rodapé do menu e aba Release notes).
- **Foto do login:** vem de URL externa no protótipo; na implementação, salvar como `assets/login-fod.jpg` (FOD-IMP-0060).
- **SSO Microsoft:** depende da integração com Microsoft Entra ID no back-end (FOD-IMP-0062).
- **Push no PWA:** depende do back-end (FOD-IMP-0300).
- **Configurações expansível:** exige a versão atual do `Sidebar` do DS (itens com `children`); a cópia em `_ds/` está defasada.
- **Tabelas no tablet:** hoje rolam na horizontal (FOD-IMP-0035); avaliar a versão em cartões para 768–1023px.
- **Dados ilustrativos:** a fila de Aprovação, parte dos cards do Kanban e as quebras por demandante do Dashboard foram preenchidas para ilustrar. Os demais números vêm das capturas de 16/10/2026.
- **Arquivo:** a V1 não prevê busca nem filtros; não foram adicionados.
- **Fora do escopo deste documento:** contratos de API, desempenho (metas de carregamento), telemetria e plano de testes automatizados. Abrir documento próprio se necessário.

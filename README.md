# Diário de Bordo — Projeto Flutter (Faculdade)

Este repositório é o registro de tudo que vamos fazendo no projeto Flutter da
faculdade (`flutter_dart`): decisões tomadas, comandos rodados, prompts usados
com IA e prints das telas. A ideia é ir anotando aqui no dia a dia; depois dá
pra transformar isso num template mais bonito.

Cada entrada nova fica em ordem cronológica (mais recente por último), com
data, o que foi feito, como foi feito (incluindo uso de IA) e, quando possível,
imagem.

---

## 25/08 e 26/08/2026

**O que foi feito:** criação do repositório `flutter_dart` no GitHub e
configuração da proteção da branch `main`, antes de qualquer código entrar
no projeto.

**Como foi feito:**
- Criei o repositório `flutter_dart` na conta `Pedro-Martins-Nascimento`
  (público, sem README/gitignore automáticos).
- Fiz o primeiro commit do projeto pelo VS Code ("setup inicial do
  projeto Flutter").
- Configurei o `.gitignore` pra não versionar arquivos sensíveis do
  Firebase (`android/app/google-services.json`,
  `ios/Runner/GoogleService-Info.plist`, `lib/firebase_options.dart`),
  já preparando pra quando a integração com Firebase entrar na N2.
- Criei uma **ruleset** (`main-protection`) pra branch `main`, em vez da
  branch protection clássica:
  - exige Pull Request antes de qualquer merge (sem push direto);
  - exige 1 aprovação de review antes de mergear;
  - permite só **squash merge** (desabilita merge commit e rebase);
  - bloqueia force push e restringe quem pode apagar a branch.

**Resultado:** repositório criado e protegido desde o início — toda
mudança na `main` passa obrigatoriamente por PR revisada. Essa regra
acabou travando alguns merges nos dias seguintes (ver entrada de
04/09/2026), mas era exatamente o comportamento configurado aqui.

**Imagens:**

![Criação do repositório no GitHub](imagens/2026-08-25/01-criacao-repositorio.jpeg)
*Criação do repositório no GitHub*

![Primeiro commit pelo VS Code](imagens/2026-08-26/01-primeiro-commit-vscode.jpeg)
*Primeiro commit pelo VS Code*

![.gitignore com exclusão da config sensível do Firebase](imagens/2026-08-26/02-gitignore-firebase.jpeg)
*`.gitignore` com exclusão da config sensível do Firebase*

![Criação da ruleset main-protection: nome e status](imagens/2026-08-26/03-ruleset-criacao-nome.jpeg)
![Criação da ruleset main-protection: branch rules](imagens/2026-08-26/04-ruleset-branch-rules.jpeg)
![Criação da ruleset main-protection: aprovações obrigatórias](imagens/2026-08-26/05-ruleset-approvals-e-squash.jpeg)
![Criação da ruleset main-protection: squash-only e code owners](imagens/2026-08-26/06-ruleset-squash-e-code-owners.jpeg)
![Criação da ruleset main-protection: bloqueio de force push](imagens/2026-08-26/07-ruleset-force-push-e-create.jpeg)
![Ruleset main-protection criada e ativa](imagens/2026-08-26/08-ruleset-criado-ativo.jpeg)
*Passo a passo da criação da ruleset `main-protection` (PR obrigatória, 1
aprovação, squash-only, bloqueio de force push)*

---

## 29/08/2026

**O que foi feito:** limpeza geral do projeto Flutter recém-criado
(`flutter_dart`), que estava com a estrutura padrão gerada pelo
`flutter create`.

**Como foi feito:**
- Usei o Claude Code (assistente de IA) para revisar o repositório.
- Rodei `flutter analyze` e `flutter test` pra confirmar que o projeto padrão
  não tinha nenhum erro antes de mexer em qualquer coisa.
- Pedi para remover os comentários de tutorial que o Flutter gera
  automaticamente em `lib/main.dart`, `test/widget_test.dart` e
  `pubspec.yaml`, mantendo só o código funcional.
- Atualizei a descrição do `pubspec.yaml` e o `README.md` do projeto
  (`flutter_dart`) para refletir que é um projeto da faculdade.
- Rodei `flutter analyze` e `flutter test` de novo depois das mudanças pra
  garantir que nada quebrou.

**Resultado:** projeto `flutter_dart` mais limpo, sem comentários de
boilerplate, com `flutter analyze` e `flutter test` passando 100%.

**Imagens:**

![PR #2 mergeada](imagens/2026-09-02/01-pr2-merged.jpeg)
![Descrição e reviewers da PR #2](imagens/2026-09-02/02-pr2-descricao-reviewers.jpeg)
*Merge da PR #2, que entregou essa limpeza (os prints são de 02/09; o
trabalho local foi feito em 29/08, mas a PR só foi mergeada no GitHub
alguns dias depois)*

---

## 03/09/2026

**O que foi feito:** análise das Pull Requests abertas no repositório
`flutter_dart` para decidir a estratégia de merge (mergear tudo e depois
padronizar componentes, ou pedir pra cada pessoa arrumar antes de mergear).
Só análise — nenhuma PR foi mergeada ou alterada.

**Como foi feito:**
- Usei o Claude Code (assistente de IA) para listar e inspecionar as PRs
  abertas com `gh pr list` e `gh pr view <n> --json ...`.
- Comparei os branches com `git log origin/main..origin/<branch> --oneline`
  pra entender a cadeia de dependência entre as PRs.
- Simulei os merges com `git merge-tree $(git merge-base origin/main
  origin/feature/tela-provas) origin/main origin/feature/tela-provas` pra
  achar a causa exata do conflito, sem precisar fazer checkout de nada.

**Evidência (logs do git):**
```
$ gh pr list --state open
#5  Feature/main shell   feature/main-shell -> feature/tela-login   MERGEABLE
#4  Tela-Login           feature/tela-login -> feature/tela-provas  MERGEABLE
#3  banco de questoes... feature/questoes   -> main                CONFLICTING
#1  feat(provas)...      feature/tela-provas -> main               CONFLICTING

$ git log origin/main..origin/feature/tela-provas --oneline
29cb9dc feat(provas): fluxo completo de Criar Prova, Gerar Provas e exportação em PDF

$ git log origin/feature/tela-provas..origin/feature/questoes --oneline
38c9244 feat(questoes): banco de questoes com alternativas e gabarito

$ git log origin/feature/tela-provas..origin/feature/tela-login --oneline
8e93729 feat: added login
ce7f49a added new route
f5ed203 remove debug and update comment

$ git log origin/feature/tela-login..origin/feature/main-shell --oneline
256bd32 feat: added em breve
7336950 feat: added bottom nav
0ad62d5 feat: added main shell
daa19df feat: new routes
```
O `git merge-tree` mostrou que o conflito de #1 e #3 é só em `lib/main.dart`:
a limpeza de comentários já feita em `main` (commit `4c98a3a`) mexeu no mesmo
trecho que a #1 reescreveu para usar `MaterialApp.router`.

**Resultado / decisão:** as PRs não são independentes, formam uma cadeia:
`main` ← `tela-provas` (#1) ← `tela-login` (#4) ← `main-shell` (#5), com
`questoes` (#3) derivada de `tela-provas` mas mirando `main` direto (por
isso já carrega o commit da #1 dentro dela). Também achei uma branch órfã
`feature/tela-turmas` sem PR aberta.

Decidido: mergear na ordem certa primeiro (resolvendo o único conflito
pontual do `main.dart`) e só depois abrir uma tarefa de padronização de
componentes (tema, cards, navegação) — em vez de pedir pra cada autor
arrumar em paralelo, o que geraria mais rebase e mais conflito com os
branches se movendo. Ordem definida:
1. Resolver conflito de `main.dart` e mergear #1 (`tela-provas`).
2. Reapontar #3 (`questoes`) pra `main` e mergear.
3. Reapontar #4 (`tela-login`) pra `main` e mergear.
4. Reapontar #5 (`main-shell`) pra `main` e mergear.
5. Abrir PR única de padronização em cima do que já estiver consolidado.

**Também nesse dia:** aprovada a PR #6 (`docs: register project decisions
and notes`), que organizou a documentação do projeto em
`docs/correcao-de-provas-docs.md` — levantamento do cliente, RFs/RNFs,
fases N1/N2/N3 e o protótipo visual em HTML (`docs/appProvas.html`) — e
que serviu de base pra este diário de bordo.

**Imagens:**

![PR #6 aprovada por KelciaAntiuk](imagens/2026-09-03/01-pr6-aprovada-kelcia.jpeg)
*PR #6 aprovada por `KelciaAntiuk`, pronta pra squash merge*

---

## 04/09/2026

**O que foi feito:** merge das PRs abertas do `flutter_dart` (banco de
questões, login e main-shell) seguindo a ordem decidida no dia 03/09,
resolvendo os conflitos reais encontrados no caminho.

**Como foi feito:**
- Usei o Claude Code para trocar a conta ativa do GitHub CLI
  (`gh auth switch`) para a conta dona do repositório
  (`Pedro-Martins-Nascimento`) antes de mexer nas PRs.
- Mergeei a PR #4 (Tela-Login) com `gh pr merge --merge`. Só depois percebi
  que a PR estava aberta contra `feature/tela-provas` como base (não
  `main`) — o merge foi real, mas não chegou na `main` diretamente. Isso
  também fechou a PR #5 sozinha, porque o GitHub fecha automaticamente
  uma PR quando a branch base dela é apagada (e o `--delete-branch` do
  merge da #4 apagou `feature/tela-login`, que era a base da #5).
- Recriei a PR #5 como uma nova PR (#7), agora com base certa (`main`).
  Como a branch `feature/main-shell` já continha os commits do login (era
  empilhada em cima de `feature/tela-login`), o conteúdo do login acabou
  chegando na `main` por essa PR mesmo assim.
- Resolvido o conflito de merge entre `feature/main-shell` e `main` em
  `lib/main.dart` e `lib/router/app_router.dart` — histórico divergente,
  já que `feature/main-shell` foi criada antes do squash-merge da PR #1.
  Mantida a versão mais completa (login + shell com bottom nav + rotas de
  provas).
- Atualizei o smoke test (`test/widget_test.dart`), que checava a tela de
  Criar Prova como tela inicial — ele já tinha um TODO anotado pra isso,
  então troquei pra verificar a tela de Login.
- Resolvido o conflito da PR #3 (banco de questões) em 5 arquivos
  (`app_router.dart`, `criar_prova_screen.dart`, `gerar_provas_screen.dart`,
  `preview_layout_screen.dart`, `pdf_service.dart`). Comparei os dois lados
  do conflito com `git diff origin/main:<arquivo> feature/questoes:<arquivo>`
  e confirmei que a versão da branch `feature/questoes` era uma evolução
  da que já estava na `main` (trocava os dados mock pelo modelo `Questao`
  real, com alternativas e gabarito) — então mantive essa versão.
- Rodei `flutter analyze` e `flutter test` depois de cada merge de
  conflito, antes de subir (`git push`), pra garantir que nada quebrou.
- Descobri que o repositório tem uma **ruleset** na `main` exigindo 1
  review aprovada e permitindo só squash merge — nem `gh pr merge --admin`
  contorna isso. Pedi review pra `KelciaAntiuk` nas PRs #7 e #3.
- Cheguei a criar uma PR de diário de bordo dentro do próprio repositório
  `flutter_dart` (`docs/diario-de-bordo.md`), mas o diário de bordo é essa
  pasta local mesmo — fechei a PR e apaguei o arquivo/branch de lá.

**Resultado:** PRs #7 (main-shell + login) e #3 (banco de questões)
prontas e validadas (`flutter analyze` sem apontamentos, `flutter test`
passando), aguardando review aprovada pra mergear com squash. PR #4 ficou
registrada como "merged" no GitHub, mas mergeada em `feature/tela-provas`
em vez de `main` — sem efeito prático, porque o conteúdo dela chega na
`main` de qualquer forma pela #7, mas fica esse detalhe no histórico do
GitHub pra quem for conferir depois.

**Pendente:**
- Aguardar aprovação de `KelciaAntiuk` e mergear `#7` e depois `#3` (nessa
  ordem, via squash).
- Revalidar `#3` contra a `main` depois que `#7` for mergeada (as duas
  mexem em `app_router.dart`, pode surgir conflito novo).

**Imagens:** _(adicionar prints em `imagens/2026-09-04/` quando tiver)_

---

## 08/09/2026

**O que foi feito:** troca da conta ativa do GitHub CLI, merge da PR #9
(`feature/tela-provas`) na `main`, e início da análise da PR #3 (banco de
questões), que foi interrompida por ter um conflito de lógica de negócio
real — decisão de como resolver ficou pendente.

**Como foi feito:**
- Usei o Claude Code para trocar a conta ativa do `gh` de `pedron-martins`
  para `Pedro-Martins-Nascimento` (`gh auth switch`), dona do repositório
  `flutter_dart`.
- Revisei a PR #9 (`feature/tela-provas` → `main`, autora `KarenAmancio`):
  fiz checkout, mergeei `main` na branch e resolvi um conflito pontual em
  `lib/router/app_router.dart` (a branch ainda tinha rotas soltas de
  `/criar-prova` e `/gerar-provas` fora do shell; a `main` já tinha
  migrado essas rotas pra dentro da `StatefulShellRoute` com bottom nav —
  mantive a estrutura de shell da `main` e preservei o tratamento de
  `ProvaGerada`/histórico que a PR trazia).
- Rodei `flutter analyze` (sem apontamentos) e `flutter test` (1/1
  passando) depois do merge, antes de subir.
- Tentei `gh pr merge --merge` e esbarrei de novo na ruleset da `main`
  (ver 25/08): exige review aprovada. Diferente do dia 04/09, dessa vez
  aprovei a PR eu mesmo pelo `gh pr review --approve` (com o resultado da
  validação no corpo da aprovação) antes de mergear.
- `gh pr merge --merge` falhou de novo, mas com um motivo novo: "Merge
  commits are not allowed on this repository" — a ruleset permite **só
  squash merge** (como já estava documentado na entrada de 25/08, mas eu
  tinha esquecido na hora). Troquei pra `gh pr merge --squash` e mergeou.
- Comecei a mesma sequência na PR #3 (`feature/questoes` → `main`, autor
  `Eduardo`): atualizei a `main` local (trouxe o squash da #9) e dei
  `gh pr checkout 3` + merge da `main` na branch.
- O merge da PR #3 gerou conflito real (não só cosmético) em 5 arquivos:
  `app_router.dart`, `criar_prova_screen.dart`, `gerar_provas_screen.dart`,
  `preview_layout_screen.dart` e `pdf_service.dart`. A causa: a `main`
  (via PR #9) evoluiu "Criar Prova" com nome da prova, seleção de turma e
  histórico de provas geradas (`DadosProva`), enquanto a `feature/questoes`
  trocou o banco mock simples por um modelo real em `lib/models/questao.dart`
  (`Questao` com `alternativas` e `respostaCorreta`, banco com 8 matérias e
  48 questões) e passou a montar o PDF com o conteúdo real das questões em
  vez do texto placeholder que a `main` ainda usava.
- Cheguei a montar uma proposta de resolução (unir o modelo real de
  questões da #3 com a UI de turma/nome/histórico da #9), mas fui
  interrompido antes de aplicar — recebi instrução pra não mexer mais na
  PR #3 por agora e priorizar esse registro no diário.
- Abortei o merge em andamento (`git merge --abort`) na branch
  `feature/questoes` pra não deixar a branch remota nem o repositório
  `flutter_dart` com nada pela metade.

**Resultado:** PR #9 mergeada na `main` (squash). PR #3 segue aberta e
sem alterações — o merge de teste foi desfeito, o conflito real entre os
dois modelos de questões (mock simples com turma/nome vs. modelo real com
alternativas/gabarito) ainda precisa ser decidido antes de retomar.

**Pendente:**
- Decidir e aplicar a resolução do conflito da PR #3 (levar o modelo real
  de `models/questao.dart` para dentro da tela que já tem nome da
  prova/turma/histórico, sem perder nem o banco de questões novo nem a UI
  já consolidada).
- Revalidar com `flutter analyze` e `flutter test` depois da resolução.
- Só commitar/mergear a #3 depois dessa decisão — sem abrir PR nova, como
  combinado.

**Imagens:** _(adicionar prints em `imagens/2026-09-08/` quando tiver)_

---

## 10/09/2026

**O que foi feito:** troca da conta ativa do GitHub CLI e merge da PR #11
(`feature/tela-turmas`, autora Letícia Parpineli — telas de Turmas) na
`main`, sem abrir PR nova.

**Como foi feito:**
- Usei o Claude Code para trocar a conta ativa do `gh` de `pedron-martins`
  para `Pedro-Martins-Nascimento` (`gh auth switch`), dona do repositório.
- Revisei a PR #11 (`feature/tela-turmas` → `main`): checkout via
  `gh pr checkout 11`. A branch já estava sincronizada com a `main` (merge
  anterior no commit `b286c55`), então não houve conflito nenhum pra
  resolver dessa vez.
- Entrega da PR: telas de listar/criar/visualizar turma
  (`criar_turma_screen.dart`, `visualizar_turma_screen.dart`) e importação
  de lista de alunos via CSV/XLSX (`importar_alunos_screen.dart`, com os
  pacotes novos `file_picker` e `excel`), além das rotas correspondentes
  em `app_router.dart` — cobre RF03, RF04 e RF05 do escopo da N1, ainda
  com dados mock (sem persistência real), como esperado nessa fase.
- Rodei `flutter pub get`, `flutter analyze` (sem apontamentos) e
  `flutter test` (1/1 passando) antes de decidir mergear.
- Como não havia review na PR, aprovei eu mesmo com `gh pr review
  --approve` (registrando o resultado da validação no corpo), do mesmo
  jeito que na entrada de 08/09 — a ruleset da `main` exige 1 aprovação
  antes do merge.
- `gh pr merge --squash` (a ruleset só permite squash merge, como já
  registrado em 25/08 e 08/09) — mergeou de primeira dessa vez.
- No VS Code local sobrou uma alteração não commitada em
  `macos/Flutter/GeneratedPluginRegistrant.swift` (arquivo que o Flutter
  regenera sozinho a cada `pub get` quando há dependência nova). Conferi
  com `git diff` e era só troca de fim de linha (LF → CRLF, efeito do
  ambiente Windows) — descartei com `git restore` em vez de commitar.

**Resultado:** PR #11 mergeada na `main` (squash), sem precisar de PR
nova. `main` local atualizada via fast-forward.

**Também nesse dia — resolução da PR #3 (banco de questões, autor Eduardo):**
esse era o conflito real deixado pendente desde 08/09 (ver entrada acima).

- Fiz checkout da PR #3 (`gh pr checkout 3`) e trouxe a `main` atualizada
  (já com #7, #9 e #11) pra dentro da branch com `git merge origin/main`.
- O merge reabriu o mesmo conflito de 5 arquivos já mapeado em 08/09:
  `app_router.dart`, `criar_prova_screen.dart`, `gerar_provas_screen.dart`,
  `preview_layout_screen.dart`, `pdf_service.dart`. Causa: o modelo de
  questões da PR (`Questao` com alternativas e gabarito, banco com 48
  questões/8 matérias, em `lib/models/questao.dart`) e a evolução de
  "Criar Prova"/"Gerar Provas" que a PR #9 já tinha trazido pra `main`
  (nome da prova, seleção de turma, histórico via `ProvasRepository`)
  editavam os mesmos pontos de formas incompatíveis.
- Decisão de integração (a que ficou em aberto em 08/09): manter a
  estrutura de tela mais evoluída da `main` (nome da prova, turma,
  histórico) e enxertar nela o banco real de questões da PR. Na prática:
  - `criar_prova_screen.dart` passou a puxar matérias/questões de
    `lib/models/questao.dart` em vez do mock local simples (sem
    alternativas) que a `main` ainda tinha.
  - Aproveitei pra unificar o modelo de `Turma` local (duplicado nessa
    tela) com o `Turma` real do módulo de turmas (`criar_turma_screen.dart`,
    da PR #11) — turma criada ali passa a aparecer em "Minhas Turmas"
    também, em vez de sumir numa lista mock isolada.
  - `gerar_provas_screen.dart` ganhou de volta a lógica de embaralhamento
    por versão que só existia na PR (`QuestaoNaVersao`: alternativas
    reordenadas e `respostaCorreta` recalculado por versão — o gabarito
    de cada versão), mantendo o cabeçalho completo (nome/turma/professor/
    histórico) que a `main` já tinha.
  - `pdf_service.dart` e `preview_layout_screen.dart`: mantidos os
    controles de layout (fonte/espaçamento/margem) da `main`, mas o PDF
    passou a imprimir o enunciado e as alternativas de texto reais de
    cada questão — antes disso o PDF da `main` só tinha um enunciado mock
    fixo com bolinhas vazias A/B/C/D, sem gabarito de verdade nenhum.
- Rodei `flutter analyze` (sem apontamentos) e `flutter test` (1/1)
  depois de resolver os 5 arquivos.
- Testei o fluxo completo manualmente rodando o app no navegador
  (`flutter run -d web-server`, controlado via extensão Claude em Chrome):
  criei uma prova de Matemática vinculada à turma real "3º ano B —
  Matutino", selecionei 3 questões do banco, gerei 3 versões e abri o
  Editor de layout — o PDF gerado mostrou o enunciado e as alternativas
  reais de cada questão, com a ordem das alternativas (e portanto o
  gabarito) diferente entre versões, confirmando o embaralhamento.
- Como não havia review, aprovei eu mesmo (`gh pr review --approve`) com
  um relatório detalhado da resolução do conflito no corpo — mesmo com
  autonomia total pra decidir, deixei a decisão de design registrada na
  PR pro Eduardo revisar depois. Mergeei com `gh pr merge --squash`
  (mesma ruleset das outras), sem abrir PR nova.

**Resultado:** PR #3 mergeada na `main` (squash). O banco real de
questões (alternativas + gabarito) está integrado ao fluxo de Criar
Prova → Gerar Provas → Exportar PDF, com gabarito recalculado por versão
e turma unificada com o módulo de Turmas. `main` local atualizada via
fast-forward.

**Também nesse dia — revisão e merge da PR #12 (`feat/corrigir`, autora
KelciaAntiuk):** entrega grande, sem conflito de merge com a `main`
(a branch já estava sincronizada), mas que precisou de uma correção antes
de mergear.

- Fiz checkout da PR #12 (`gh pr checkout 12`); `git merge origin/main`
  não teve nada a fazer — a branch já estava com #3, #7, #9 e #11
  incorporados.
- Escopo entregue: banco de questões com CRUD completo (Matérias →
  Questões → Formulário de questão, cobrindo RF06/RF07 que ainda
  faltavam), dashboard real na tela de Início (corrigidas/pendentes/
  média, erro por matéria, últimas provas — usando `ChangeNotifier` no
  `ProvasRepository`), tela de Corrigir com leitura de QR via câmera
  (pacote `mobile_scanner`, RF13/RF14), e navegação responsiva (sidebar
  em telas largas, bottom nav em estreitas). Também relaxou o modelo
  `Questao` pra aceitar de 2 a 8 alternativas (antes travado em
  exatamente 4), pra combinar com o novo formulário.
- **Achado sério antes de validar:** um dos commits da própria PR
  (`chore(testes): remove os arquivos de teste`) apagava o único arquivo
  de teste do projeto (`test/widget_test.dart`) inteiro, sem nenhum
  substituto e sem justificativa no corpo do commit. Recuperei o
  conteúdo de antes desse commit (`git show <commit>~1:test/widget_test.dart`)
  e restaurei o arquivo — o smoke test da tela de Login continuou
  passando normalmente mesmo depois de todas as mudanças da PR.
- Revisei manualmente o código novo (não só rodei analyze/test): as 3
  telas do banco de questões, o router, `main.dart`, `login_screen.dart`
  (validação de e-mail/senha de verdade) e `provas_repository.dart`.
  Achado menor, não bloqueante: `_totalFolhas` (em `corrigir_screen.dart`
  e `inicio_screen.dart`) usa `turmasMock.first.qtdAlunos` — quebra com
  `StateError` se todas as turmas forem excluídas (caso extremo,
  registrado no comentário da PR pra próxima iteração, não corrigido
  agora).
- Rodei `flutter analyze` (sem apontamentos) e `flutter test` (com o
  teste restaurado, passando) depois da correção.
- Testei manualmente rodando o app no navegador: login com validação
  real, dashboard de Início, criei uma questão nova no banco (matéria
  Matemática) e confirmei que apareceu na listagem, e abri a tela de
  Corrigir (câmera solicitada corretamente, contador de folhas
  funcionando).
- Aprovei (`gh pr review --approve`) com relatório detalhado no corpo —
  citando explicitamente a remoção do teste corrigida e o achado menor
  pendente — e mergeei com `gh pr merge --squash`, sem abrir PR nova.

**Resultado:** PR #12 mergeada na `main` (squash), com o smoke test
restaurado. App ganhou banco de questões com CRUD completo, dashboard de
Início, correção por QR code (câmera) e navegação responsiva. `main`
local atualizada via fast-forward.

**Pendente:** tratar o caso de `turmasMock` vazio em `_totalFolhas`
(`corrigir_screen.dart` e `inicio_screen.dart`) com um fallback, em vez
de acessar `.first` direto.

**Imagens:** _(adicionar prints em `imagens/2026-09-10/` quando tiver)_

---

<!--
Modelo para novas entradas — copiar e preencher:

## DD/MM/AAAA

**O que foi feito:**

**Como foi feito:**

**Resultado:**

**Imagens:**
-->

O que é o Git Flow (versão pocket)

Modelo de branching criado pelo Vincent Driessen. A ideia é separar desenvolvimento contínuo de linhas estáveis com um conjunto fixo de branches:

main (ou master): código pronto pra produção. Cada commit aqui deveria ter tag de versão.

develop: integração do que será o próximo release.

Branches temporárias:

feature/*: novas features saem de develop, voltam pra develop.

release/*: preparação do lançamento (fixes finais, version bump). Saem de develop, voltam pra main e develop.

hotfix/*: correção urgente em produção. Saem de main, voltam pra main e develop.

vibe: previsível, organizado, ótimo pra times que fazem releases “fechados” (ex: versão 2.4, 2.5…).

Setup: duas formas de usar
1) Usando a extensão git-flow (mais fácil)

Instala:

macOS: brew install git-flow-avh

Debian/Ubuntu: sudo apt-get install git-flow

Windows: via Git for Windows (tem ports) ou choco: choco install gitflow-avh

Inicializa no repositório:

git flow init
# ele vai perguntar nomes dos branches; aceita os defaults:
# main: main (ou master), develop: develop, prefixes: feature/, release/, hotfix/


Ciclo completo:

# começar uma feature
git flow feature start minha-feature

# trabalhar, commitar
git add .
git commit -m "feat: implementa X"

# terminar e mesclar na develop
git flow feature finish minha-feature
# (opcionalmente abre PR, se seu time preferir PRs antes de finish)

# preparar um release
git flow release start 1.2.0
# faz ajustes, bump de versão, changelog...
git commit -m "chore: bump para 1.2.0"
git flow release finish 1.2.0
# isso: mergeia em main + develop, cria tag v1.2.0

# hotfix (bug em prod)
git flow hotfix start 1.2.1
# faz o fix
git commit -m "fix: corrige crash no login"
git flow hotfix finish 1.2.1
# merge em main + develop e tag v1.2.1

2) Só no Git “puro” (sem extensão)

Mesma lógica, comandos explícitos:

Feature
git checkout develop
git pull
git checkout -b feature/minha-feature
# ... commits ...
git push -u origin feature/minha-feature
# abre PR -> base: develop
# depois do review:
git checkout develop
git pull
git merge --no-ff feature/minha-feature
git tag -a v1.2.0-rc1 -m "rc opcional" # se quiser
git branch -d feature/minha-feature
git push origin :feature/minha-feature

Release
git checkout develop
git pull
git checkout -b release/1.2.0
# ajusta versão/changelog
git commit -m "chore: bump 1.2.0"
# QA/fixes...
git checkout main
git merge --no-ff release/1.2.0
git tag -a v1.2.0 -m "release 1.2.0"
git checkout develop
git merge --no-ff release/1.2.0
git branch -d release/1.2.0
git push --follow-tags

Hotfix
git checkout main
git pull
git checkout -b hotfix/1.2.1
# faz o fix
git commit -m "fix: xyz"
git checkout main
git merge --no-ff hotfix/1.2.1
git tag -a v1.2.1 -m "hotfix 1.2.1"
git checkout develop
git merge --no-ff hotfix/1.2.1
git branch -d hotfix/1.2.1
git push --follow-tags


Flag --no-ff: preserva o commit de merge, deixando o histórico dos tópicos explícito.

Convenções que salvam sua sanidade

Prefixos: feature/, bugfix/, chore/, release/, hotfix/.

Nomes: kebab-case curto: feature/cadastro-usuario.

Commits: Conventional Commits ajuda MUITO: feat:, fix:, chore:, refactor:, docs: etc.

Tags: vX.Y.Z (SemVer).

Proteções:

main e develop protegidos (branch protection).

PR obrigatório com 1–2 reviews.

CI obrigatória passando antes do merge.

Passo a passo “do zero”

Cria main e develop:

git init
git add .
git commit -m "chore: initial"
git branch -M main
git checkout -b develop
git push -u origin main
git push -u origin develop


Configura CI pra rodar em PRs pra develop e em tags para main.

Define SemVer + changelog (ex: Keep a Changelog).

Acordo no time: quando abrir release? quando usar hotfix? política de versionamento.

Integração com CI/CD (esqueleto mental)

PR pra develop → roda testes/lint/build.

Merge em main com tag → pipeline de release:

build versionado

publicar pacote/imagem

gerar release notes automático (a partir dos commits/labels)

Release candidates: usar release/x.y.z pra QA com builds pré-release.

Cheatsheet rápido
# extensão
git flow init
git flow feature start <nome>
git flow feature finish <nome>
git flow release start <versao>
git flow release finish <versao>
git flow hotfix start <versao>
git flow hotfix finish <versao>

# git puro (essência)
git checkout -b feature/<nome> develop
git merge --no-ff feature/<nome>
git checkout -b release/<versao> develop
git tag -a v<versao> -m "release <versao>"
git checkout -b hotfix/<versao> main
git push --follow-tags

Quando usar Git Flow

Produto com lançamentos por versão (mobile, on-prem, SDKs).

Times que querem janela de estabilização (branch release).

Ambientes com mudanças auditáveis e tags de produção.

Quando não usar (e alternativas)

Deploy contínuo? Vai de GitHub Flow (só main + feature branches) ou Trunk-Based (branch curta e merge rápido).

Vários ambientes e múltiplas linhas de produto? GitLab Flow (ambiente-centrado) pode ser melhor.

Erros comuns (e como driblar)

Features longas: fatiar em PRs menores; sinalizar “WIP” cedo.

Divergência entre develop e main: sempre mergear release/hotfix de volta nos dois.

Conflitos eternos: rebase frequente da feature com develop ajuda.

Tag faltando: automatiza tag ao fechar release/hotfix.

Release vira dumping ground: só fix e polish; features novas ficam fora.

Diquinhas de ouro

Habilita git config --global pull.rebase false se quer manter merges explícitos (estilo Git Flow).

Usa templates de PR e labels para gerar release notes automáticas.

Garante que version bump e CHANGELOG acontecem no release/* (não em develop direto).

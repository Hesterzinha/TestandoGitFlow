🚀 Estrutura de Branches
Tipo de Branch	Origem	Destino	Função
main	—	—	Contém o código de produção
develop	main	main	Integra as features em desenvolvimento
feature/*	develop	develop	Novas funcionalidades
release/*	develop	main e develop	Preparação de uma nova versão
hotfix/*	main	main e develop	Correções urgentes em produção
⚙️ Inicialização
Usando a extensão git-flow
# instalação
brew install git-flow-avh       # macOS
sudo apt-get install git-flow   # Linux (Debian/Ubuntu)
choco install gitflow-avh       # Windows

# inicialização
git flow init
# aceita os nomes padrão:
# main, develop, feature/, release/, hotfix/

💡 Fluxo de Trabalho
Features
git flow feature start minha-feature
# trabalha, commita...
git flow feature finish minha-feature

Release
git flow release start 1.2.0
# ajusta versão, changelog, testes
git flow release finish 1.2.0
# cria tag v1.2.0 e mergeia em main + develop

Hotfix
git flow hotfix start 1.2.1
# corrige bug crítico
git flow hotfix finish 1.2.1
# cria tag v1.2.1 e mergeia em main + develop

🧠 Git Flow “Manual” (sem extensão)
Criando uma Feature
git checkout develop
git checkout -b feature/nova-funcionalidade
# ... commits ...
git checkout develop
git merge --no-ff feature/nova-funcionalidade
git branch -d feature/nova-funcionalidade

Criando um Release
git checkout develop
git checkout -b release/1.2.0
# ajustes e testes
git checkout main
git merge --no-ff release/1.2.0
git tag -a v1.2.0 -m "release 1.2.0"
git checkout develop
git merge --no-ff release/1.2.0
git branch -d release/1.2.0

Criando um Hotfix
git checkout main
git checkout -b hotfix/1.2.1
# faz o fix
git checkout main
git merge --no-ff hotfix/1.2.1
git tag -a v1.2.1 -m "hotfix 1.2.1"
git checkout develop
git merge --no-ff hotfix/1.2.1
git branch -d hotfix/1.2.1

🏷️ Convenções

Prefixos: feature/, bugfix/, release/, hotfix/

Nomes curtos: feature/login-usuario

Commits: Conventional Commits

Tags: vX.Y.Z (SemVer)

Proteções:

main e develop bloqueadas

PR + revisão obrigatória

CI rodando antes do merge

🔁 Integração com CI/CD

PR → develop: executa testes/lint/build

Merge → main (com tag): gera build e faz deploy

Hotfixes: deploy imediato após merge na main

🧾 Cheatsheet Rápido
git flow init
git flow feature start <nome>
git flow feature finish <nome>
git flow release start <versao>
git flow release finish <versao>
git flow hotfix start <versao>
git flow hotfix finish <versao>

🧩 Quando Usar

✅ Ideal para:

Projetos com releases por versão

Times com testes e validações manuais

Ambientes que exigem controle e rastreabilidade

❌ Evite se:

Você faz deploy contínuo

Precisa de branches curtas e merges rápidos
(nesses casos, prefira GitHub Flow ou Trunk-Based Development)

⚠️ Erros Comuns

Features longas → Quebre em partes menores

Divergência entre main e develop → Sempre mergeie hotfixes nos dois

Falta de tag → Automatize via CI

Releases bagunçadas → Use changelog e version bump com cuidado

🪄 Dicas Extras

Mantenha histórico com --no-ff

Use templates de PR e labels pra gerar changelog automático

Automatize versão + changelog no release/*

Configure hooks pra validar commits antes de subir

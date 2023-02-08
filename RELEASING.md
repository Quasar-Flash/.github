# Releasing

O objetivo do documento é definir um padrão de versionamento e logs de alterações.

## Requisitos

- Todo projeto deve ter um arquivo `.version` para armazenar a versão mais atual do sistema. Seguir o padrão estabelecido no documento [Semantic Versioning 2.0](VERSIONING.md);
- Todo projeto deve conter um arquivo `CHANGELOG.md` e um `README.md`;
- O arquivo `README.md` deve ter uma breve descrição sobre para qual finalidade foi criado o repositório e como usar;
- O arquivo `CHANGELOG.md` deve ser escrito seguindo o padrão estabelecido pelo [Keep a Changelog](https://keepachangelog.com/en/1.0.0/). Exemplo:

```md
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.0]

### Added 

- [QISSO-1](https://link.jira) Implementa uma coisa bem legal por @jhonnymunis #1

### Changed

- [QISSO-2](https://link.jira) Refatora uma coisa bem legal por @jhonnymunis #2

### Fixed

- [QISSO-3](https://link.jira) Corrige uma coisa bem legal por @jhonnymunis #3
```
- `Added` - tag destinada à inclusão de novas funcionalides;
- `Changed` - tag destinada a refatorações e atualizações;
- `Fixed` - tag destinada a correções e hotfixes;
- A tag `unreleased` serve para documentar PR's e commits pendentes de entrega (não foram ainda empacotados em uma release);
- É importante que todos as PR's mergeadas em dev sejam documentadas nessa nova entrada do changelog.
- É importante que a branch `master` nunca esteja a frente da branch `dev`, e a documentação seja feita através de commit na dev;
- É importante que contenha os IDs do Jira referentes às issues mergeadas para a entrega para rápido tracking;
- O arquivo `.version` deve ser incluído para dentro do container para posteriormente ser usado em programas python/ruby/etc;
- O workflow de deployment e release segue o padrão:

```yml
name: Deploy Production
on:
  workflow_dispatch:
  push:
    branches:
      - master
jobs:
  Deploying:
    uses: Quasar-Flash/workflow-dpl-action/.github/workflows/deployment.yml@master
    with:
      environment: production
      environment_url: https://link
      release: true # obrigatorio, para gerar nova release e tag
    secrets: inherit
```

- Por fim, para gerar uma nova entrega, basta transportar as alterações da `dev` para a `master`. Essa tarefa fica sob responsabilidade dos tech leads dos respectivos projetos;

## Commits

Padrões de commits ~ainda~ não são exigências, porém, aconselhamos a seguir algo próximo da convenção o máximo possível. Você pode encontrar informações sobre isso no arquivo [COMMITTING.md](COMMITTING.md).

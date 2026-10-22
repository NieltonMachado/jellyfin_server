# Instruções Git para garantir que o commit vai para o repositório privado

Este repositório é um fork de outro repositório. Para evitar que o `git push` vá para o projeto original, é importante separar claramente:

- `origin`: o seu repositório privado (destino principal dos seus commits)
- `upstream`: o repositório original de onde veio o fork

## Repositório privado definido

URL do repositório privado:

https://github.com/NieltonMachado/jellyfin_server.git

## Verificar os remotes atuais

```bash
git remote -v
```

Se o resultado mostrar que o `origin` aponta para o repositório original, o próximo passo é corrigir isso.

## Passo 1: guardar o repositório original como upstream

Se o fork original já estiver configurado como `origin`, faça:

```bash
git remote rename origin upstream
```

Depois, adicione o seu repositório privado como `origin`:

```bash
git remote add origin https://github.com/NieltonMachado/jellyfin_server.git
```

Se preferir usar SSH:

```bash
git remote add origin git@github.com:NieltonMachado/jellyfin_server.git
```

## Passo 2: confirmar a configuração

```bash
git remote -v
```

Deve aparecer algo semelhante a:

```bash
origin  https://github.com/NieltonMachado/jellyfin_server.git (fetch)
origin  https://github.com/NieltonMachado/jellyfin_server.git (push)
upstream  https://github.com/<usuario-ou-org>/<repositorio-original>.git (fetch)
upstream  https://github.com/<usuario-ou-org>/<repositorio-original>.git (push)
```

## Passo 3: sincronizar o fork com o projeto original

```bash
git fetch upstream
git checkout master
```

Se a branch principal do projeto original for `main`, use `main` em vez de `master`:

```bash
git checkout main
```

Depois, atualize a sua branch com o código do projeto original:

```bash
git merge upstream/master
```

ou, se preferir:

```bash
git rebase upstream/master
```

Se o projeto usar `main`:

```bash
git merge upstream/main
```

## Passo 4: fazer o commit local

```bash
git add .
git commit -m "Descrição do commit"
```

## Passo 5: enviar para o repositório privado

```bash
git push -u origin master
```

Se a branch principal for `main`:

```bash
git push -u origin main
```

## Importante

- `git commit` apenas grava alterações localmente.
- `git push` é o comando que envia para o `origin`.
- Se o `origin` estiver apontando corretamente para o seu repositório privado, os commits vão para lá.
- O `upstream` serve apenas para receber atualizações do projeto original.

## Fluxo recomendado para um fork

```bash
git remote -v
git fetch upstream
git checkout master
git merge upstream/master
git add .
git commit -m "Implementa ajuste no fork"
git push -u origin master
```

## Remove origin Fork

Se quiser reverter a configuração do fork e voltar a um repositório normal, pode remover os remotes e deixar apenas um `origin` apontando para o seu repositório privado.

### Remover os remotes

```bash
git remote remove origin
git remote remove upstream
```

### Recriar apenas um remote principal

```bash
git remote add origin https://github.com/NieltonMachado/jellyfin_server.git
```

Se preferir usar SSH:

```bash
git remote add origin git@github.com:NieltonMachado/jellyfin_server.git
```

### Verificar resultado

```bash
git remote -v
```

Depois disso, o `origin` será o único remote e o projeto deixará de ter a estrutura de fork.

### Se apenas quiser trocar a URL do remote atual

```bash
git remote set-url origin https://github.com/NieltonMachado/jellyfin_server.git
```

## Checklist final

- [ ] `origin` aponta para o seu repositório privado
- [ ] `upstream` aponta para o repositório original
- [ ] `git push` usa `origin`
- [ ] a branch correta está ativa (`master` ou `main`)
- [ ] os commits não são enviados para o repositório original por acidente

Se o seu objetivo é manter este fork sincronizado com um projeto externo e enviar apenas as alterações para o seu repositório privado, esta configuração é a forma correta de garantir isso.

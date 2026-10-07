# Passo a passo · Avaliação Docker ViaSerra

## 0. Antes de começar

A matrícula é `26174748`. Os dois últimos dígitos são `48`:

- Portal: `8000 + 48 = 8048`
- Manutenção: `7000 + 48 = 7048`

Descubra seu **username público do Docker Hub**. Ele não é o e-mail. Depois substitua `SEU_USUARIO_DOCKERHUB` em `.env`, `docker-compose.yml` e `respostas.md`.

No VS Code, abra a pasta `avaliacao-docker-viaserra` e abra um terminal nessa pasta.

## 1. Dockerfile do portal

O arquivo `portal/Dockerfile` usa `nginx:1.27-alpine`, adiciona LABELs, copia `portal/html/` para `/usr/share/nginx/html/` e documenta a porta 80 com `EXPOSE 80`.

Teste primeiro localmente, substituindo `USUARIO` pelo seu username do Docker Hub:

```bash
docker build -t USUARIO/viaserra-portal:1.0-26174748 ./portal
```

Confira a imagem:

```bash
docker images
```

Anote o tamanho mostrado, pois ele deve entrar na resposta 1 do `respostas.md`.

Execute o portal:

```bash
docker run -d --name viaserra-portal -p 8048:80 USUARIO/viaserra-portal:1.0-26174748
```

Abra `http://localhost:8048` no navegador.

Confira os arquivos dentro do container:

```bash
docker exec -it viaserra-portal ls -l /usr/share/nginx/html
```

Depois pare e remova o teste para evitar conflito de nomes/portas com o Compose:

```bash
docker rm -f viaserra-portal
```

## 2. Proteger o .env e iniciar o Git

O `.gitignore` contém `.env`, então suas variáveis locais não devem ser versionadas. O `.env.example` deve continuar no Git.

Confira:

```bash
git status
git check-ignore .env
```

Se o repositório ainda não tiver Git:

```bash
git init
git add .
git commit -m "Parte 1: configura portal Docker"
```

Crie um repositório no GitHub e conecte-o. Exemplo:

```bash
git branch -M main
git remote add origin URL_DO_SEU_REPOSITORIO
git push -u origin main
```

O verificador exige um remoto do GitHub e pelo menos 4 commits. Faça commits separados ao longo da atividade.

## 3. Publicar no Docker Hub

Faça login sem colocar sua senha em arquivos do projeto:

```bash
docker login
```

Envie a imagem:

```bash
docker push USUARIO/viaserra-portal:1.0-26174748
```

O repositório/tag precisa estar público para o verificador conseguir consultá-lo.

Faça outro commit:

```bash
git add .
git commit -m "Parte 2: prepara publicacao do portal"
git push
```

## 4. Corrigir a manutenção

O Dockerfile antigo tinha três problemas:

1. `COPY pagina/ .` apontava para uma pasta inexistente. A pasta correta é `site/`.
2. O diretório usado não era o diretório padrão do conteúdo web do Nginx. O correto é `/usr/share/nginx/html`.
3. `CMD ["nginx"]` não mantinha o Nginx em foreground. Foi alterado para `nginx -g 'daemon off;'`.

Teste:

```bash
docker build -t viaserra-manutencao ./manutencao
docker run -d --name viaserra-manutencao -p 7048:80 viaserra-manutencao
```

Abra `http://localhost:7048`. Deve aparecer **Voltamos em breve**.

Remova o container de teste:

```bash
docker rm -f viaserra-manutencao
```

Faça o terceiro commit:

```bash
git add manutencao/Dockerfile respostas.md
git commit -m "Parte 3: corrige pagina de manutencao"
git push
```

## 5. Docker Compose

Depois de substituir `SEU_USUARIO_DOCKERHUB`, o Compose sobe os dois serviços juntos:

```bash
docker compose up -d
```

Confira:

```bash
docker compose ps
```

Teste:

- Portal: `http://localhost:8048`
- Manutenção: `http://localhost:7048`

Para desligar os dois:

```bash
docker compose down
```

Para ligá-los novamente antes do verificador:

```bash
docker compose up -d
```

Faça o quarto commit:

```bash
git add docker-compose.yml respostas.md
git commit -m "Parte 4: configura docker compose"
git push
```

## 6. Rodar o verificador

Com o Compose no ar, no Windows PowerShell:

```powershell
powershell -ExecutionPolicy Bypass -File scripts/verificar.ps1
```

Se estiver usando Git Bash:

```bash
bash scripts/verificar.sh
```

O objetivo é obter todas as verificações como `[ OK ]`. Quando aparecer o **Código de conclusão**, copie exatamente para a questão 9 de `respostas.md`.

Depois faça o commit final:

```bash
git add respostas.md
git commit -m "Finaliza avaliacao e registra codigo de conclusao"
git push
```

## 7. O que cada instrução principal significa

- `FROM`: escolhe a imagem base.
- `LABEL`: adiciona metadados à imagem.
- `COPY`: copia arquivos do projeto para a imagem.
- `EXPOSE 80`: documenta que o serviço usa a porta 80 dentro do container.
- `docker build`: constrói uma imagem a partir de um Dockerfile.
- `docker run`: cria e inicia um container.
- `-d`: executa em segundo plano.
- `-p 8048:80`: liga a porta 8048 do computador à 80 do container.
- `docker push`: envia uma imagem ao Docker Hub.
- `docker compose up -d`: cria/inicia os serviços definidos no YAML.
- `docker compose down`: remove os containers e a rede criados pelo Compose.
- `restart: unless-stopped`: reinicia o container automaticamente, exceto quando você o interrompe manualmente.

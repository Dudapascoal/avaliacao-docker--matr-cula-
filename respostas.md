# Respostas · Avaliação Prática de Docker · ViaSerra Transportes (Turma C)

Nome: Maria Eduarda Moreira Pascoal
Matrícula: 26174748
Usuário do GitHub: PREENCHER_COM_SEU_USUARIO_GITHUB
Usuário do Docker Hub: mariapascoal

## Parte 1 · Dockerfile do portal

1. Usei a imagem base `nginx:1.27-alpine`, por ser uma imagem leve do Nginx e possuir uma versão definida. O tamanho final deve ser preenchido depois de executar `docker images mariapascoal/viaserra-portal:1.0-26174748` na minha máquina.

2. O Nginx procura os arquivos do site em `/usr/share/nginx/html`. Para conferir o arquivo dentro do container, usei:
   `docker exec viaserra-portal ls -l /usr/share/nginx/html/index.html`

## Parte 2 · Docker Hub

3. Imagem publicada: `mariapascoal/viaserra-portal:1.0-26174748`.
   Repositório: `https://hub.docker.com/r/mariapascoal/viaserra-portal`

4. Depois de alterar o HTML, preciso reconstruir a imagem e enviá-la novamente:
   `docker build -t mariapascoal/viaserra-portal:1.0-26174748 ./portal`
   `docker push mariapascoal/viaserra-portal:1.0-26174748`
   Depois, recrio o serviço com `docker compose up -d --force-recreate portal`.

## Parte 3 · Página de manutenção

5.

| # | Instrução | O que estava errado | O que você viu acontecer | Como corrigiu |
|---|---|---|---|---|
| 1 | `COPY pagina/ .` | A pasta `pagina/` não existe; os arquivos estão em `site/`. | O build não encontra o diretório de origem e falha. | Troquei para `COPY site/ .`. |
| 2 | `WORKDIR /usr/share/nginx` | Os arquivos eram copiados fora da raiz padrão servida pelo Nginx. | A página correta não seria servida em `/`. | Alterei para `WORKDIR /usr/share/nginx/html`. |
| 3 | `CMD ["nginx"]` | O Nginx iniciava como daemon e o processo principal do container terminava. | O container encerrava em vez de permanecer em execução. | Usei `CMD ["nginx", "-g", "daemon off;"]`. |

6. Em `-p 7048:80`, a porta 7048 é a porta do computador (host) e a porta 80 é a porta do container. Em `-p 80:7048`, a porta 80 seria a do host e a 7048 seria a do container. Como o Nginx escuta na porta 80 dentro do container, o correto neste projeto é `-p 7048:80`.

## Parte 4 · Primeiro docker-compose

7. Os comandos equivalentes são:
   `docker run -d --name portal -p 8048:80 --restart unless-stopped mariapascoal/viaserra-portal:1.0-26174748`
   `docker run -d --name manutencao -p 7048:80 --restart unless-stopped viaserra-manutencao`
   Observação: antes do segundo comando, a imagem de manutenção pode ser criada com `docker build -t viaserra-manutencao ./manutencao`.

8. O comando que derruba os dois containers criados pelo Compose de uma vez é:
   `docker compose down`

## Verificador

9. Código de conclusão impresso pelo verificador:

```
PREENCHER_APOS_RODAR_O_VERIFICADOR
```

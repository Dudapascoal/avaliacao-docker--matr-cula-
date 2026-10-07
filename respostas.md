# \# Respostas · Avaliação Prática de Docker · ViaSerra Transportes (Turma C)

# 

# \*\*Nome:\*\* Maria Eduarda Moreira Pascoal  

# \*\*Matrícula:\*\* 26174748  

# \*\*Usuário do GitHub:\*\* Dudapascoal  

# \*\*Usuário do Docker Hub:\*\* mariapascoal  

# 

# \---

# 

# \## Parte 1 · Dockerfile do portal

# 

# Usei a imagem base `nginx:1.27-alpine`, por ser uma imagem leve do Nginx e possuir uma versão definida.

# 

# Após a construção, a imagem `mariapascoal/viaserra-portal:1.0-26174748` apresentou \*\*73,6 MB de uso em disco (Disk Usage)\*\* e \*\*21 MB de tamanho de conteúdo (Content Size)\*\*.

# 

# O Nginx procura os arquivos do site no diretório:

# 

# `/usr/share/nginx/html`

# 

# Para conferir se o arquivo `index.html` estava corretamente dentro do container, utilizei o comando:

# 

# `docker exec viaserra-portal ls -l /usr/share/nginx/html/index.html`

# 

# \---

# 

# \## Parte 2 · Docker Hub

# 

# A imagem publicada no Docker Hub foi:

# 

# `mariapascoal/viaserra-portal:1.0-26174748`

# 

# Repositório no Docker Hub:

# 

# `https://hub.docker.com/r/mariapascoal/viaserra-portal`

# 

# Depois de realizar uma alteração no HTML do portal, é necessário reconstruir a imagem Docker:

# 

# `docker build -t mariapascoal/viaserra-portal:1.0-26174748 ./portal`

# 

# Em seguida, a nova versão da imagem deve ser enviada para o Docker Hub:

# 

# `docker push mariapascoal/viaserra-portal:1.0-26174748`

# 

# Depois disso, o serviço pode ser recriado utilizando:

# 

# `docker compose up -d --force-recreate portal`

# 

# \---

# 

# \## Parte 3 · Página de manutenção

# 

# | # | Instrução | O que estava errado | O que aconteceu | Como foi corrigido |

# |---|---|---|---|---|

# | 1 | `COPY pagina/ .` | A pasta `pagina/` não existe. Os arquivos da página de manutenção estão na pasta `site/`. | O Docker não encontraria o diretório de origem durante o build, causando uma falha. | Alterei para `COPY site/ .`. |

# | 2 | `WORKDIR /usr/share/nginx` | Os arquivos seriam copiados fora da raiz padrão utilizada pelo Nginx para servir páginas web. | A página de manutenção não seria corretamente disponibilizada na raiz `/`. | Alterei para `WORKDIR /usr/share/nginx/html`. |

# | 3 | `CMD \["nginx"]` | O Nginx poderia iniciar como daemon e o processo principal do container seria encerrado. | O container encerraria em vez de permanecer em execução. | Alterei para `CMD \["nginx", "-g", "daemon off;"]`. |

# 

# No mapeamento:

# 

# `-p 7048:80`

# 

# a porta \*\*7048\*\* corresponde à porta do computador (host), enquanto a porta \*\*80\*\* corresponde à porta utilizada pelo Nginx dentro do container.

# 

# Se fosse utilizado:

# 

# `-p 80:7048`

# 

# a porta \*\*80\*\* seria a porta do computador e a porta \*\*7048\*\* seria a porta interna do container.

# 

# Como o Nginx está configurado para escutar na porta \*\*80 dentro do container\*\*, o mapeamento correto para este projeto é:

# 

# `-p 7048:80`

# 

# \---

# 

# \## Parte 4 · Primeiro docker-compose

# 

# Os comandos `docker run` equivalentes aos serviços configurados no Docker Compose são:

# 

# \### Portal

# 

# `docker run -d --name portal -p 8048:80 --restart unless-stopped mariapascoal/viaserra-portal:1.0-26174748`

# 

# \### Manutenção

# 

# `docker run -d --name manutencao -p 7048:80 --restart unless-stopped viaserra-manutencao`

# 

# Antes de executar o container de manutenção separadamente, sua imagem pode ser construída utilizando:

# 

# `docker build -t viaserra-manutencao ./manutencao`

# 

# Para encerrar e remover os containers criados pelo Docker Compose de uma única vez, utilizei:

# 

# `docker compose down`

# 

# \---

# 

# \## Verificador

# 

# O verificador da avaliação apresentou:

# 

# \*\*Resultado: 9/9 verificações\*\*

# 

# \*\*Código de conclusão:\*\* `VIASERRA-26174748-5D4384A9`


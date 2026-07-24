# Seu site de catálogo com orçamento

Este pacote tem 3 arquivos que trabalham juntos:

- **index.html** — o site que os clientes veem: catálogo de produtos + orçamento automático.
- **admin.html** — o painel visual onde você cadastra e edita produtos.
- **products.json** — o "banco de dados" simples com a lista de produtos.

Não existe servidor nem banco de dados de verdade por trás — é um site 100% estático,
então não tem mensalidade de sistema, mas o processo de atualizar produtos tem uma
etapinha manual (explicada abaixo).

## 1. Como testar antes de publicar

Se você só der duplo clique no `index.html`, o navegador pode bloquear o carregamento
do `products.json` (isso é uma proteção de segurança do navegador para arquivos locais,
chamada CORS). Para testar direitinho no seu computador, mais fácil é:

- Se usar VS Code: instale a extensão "Live Server" e clique em "Go Live".
- Ou, com Python instalado, abra um terminal na pasta do site e rode:
  `python3 -m http.server 8000` e acesse `http://localhost:8000` no navegador.

Depois de publicado em uma hospedagem de verdade (veja abaixo), isso não é mais problema.

## 2. Como publicar o site

Você precisa de uma hospedagem que sirva arquivos estáticos. Algumas opções simples e
gratuitas para começar:

- **Netlify** (netlify.com) — arraste a pasta inteira do site no painel deles.
- **GitHub Pages** — suba os arquivos em um repositório e ative o Pages nas configurações.
- Hospedagem paga comum (Hostgator, KingHost, Hostinger etc.) — envie os 3 arquivos pelo
  Gerenciador de Arquivos ou FTP para a pasta pública do site (geralmente `public_html`).

Os três arquivos (`index.html`, `admin.html`, `products.json`) devem ficar **na mesma pasta**.

## 3. Configurações rápidas

Abra o `index.html` em um editor de texto e procure o bloco `const CONFIG = { ... }`
perto do final do arquivo. Lá você troca:

- `siteName` — o nome da sua loja/negócio
- `tagline` — a frase curta ao lado do nome
- `ownerEmail` — **o e-mail para onde os pedidos de orçamento serão enviados**
- `contactLine` — texto de contato do rodapé

Cores do site: no topo do `<style>` do `index.html` e do `admin.html` tem um bloco
`:root { --bg: ...; --accent: ...; }` — são as cores principais, dá pra trocar os
códigos de cor (hexadecimais) livremente.

## 4. Como funciona o orçamento automático

Cada produto tem um preço (`preco`) e uma unidade (`unidade`, ex: "un", "kg", "m²").
O cliente escolhe a quantidade de cada produto, e o site multiplica automaticamente
(quantidade × preço) e soma tudo no total. Como você me disse que ainda vai definir a
lógica de preço, os valores que estão no `products.json` agora são só exemplos —
edite-os no painel (`admin.html`) com os preços reais assim que decidir.

Quando o cliente clica em **"Solicitar orçamento"**, ele preenche nome e telefone, e o
site abre o aplicativo de e-mail dele já com o pedido formatado, pronto pra enviar para
o seu e-mail (`ownerEmail`). Também tem um botão "Copiar resumo", caso o cliente prefira
colar o pedido no WhatsApp em vez de e-mail.

## 5. Como usar o painel de produtos (admin.html)

1. Acesse `seusite.com/admin.html`.
2. Digite a senha (o padrão é `trocar123` — **troque isso antes de publicar**, veja a
   dica de segurança abaixo).
3. Clique em **"+ Novo produto"** para cadastrar, ou em **"editar"** na tabela para mudar
   um produto existente.
4. Preencha nome, categoria, descrição, preço e (opcional) o link de uma foto.
5. Quando terminar todas as edições do dia, clique em **"⬇ Baixar products.json"**.
6. Envie esse arquivo baixado para a hospedagem do site, substituindo o `products.json`
   antigo (mesma pasta do `index.html`). Pronto — o catálogo atualizado aparece pra
   todo mundo que visitar o site.

### Sobre a senha do painel

A senha do `admin.html` (variável `ADMIN_PASSWORD` no começo do `<script>`) é uma trava
simples para afastar visitantes curiosos — **não é uma segurança real**, porque qualquer
pessoa que veja o código-fonte da página consegue encontrá-la. Não cadastre nada
sigiloso no painel. Se no futuro você quiser um login de verdade (com banco de dados,
múltiplos usuários, etc.), aí já é um projeto com um pouco mais de estrutura — posso te
ajudar a montar isso depois, se fizer sentido pro seu negócio.

## 6. Fotos dos produtos

Enquanto você não coloca uma foto real, o site gera automaticamente uma imagem de
exemplo colorida com as iniciais do produto. Para usar fotos de verdade: suba a imagem
em algum lugar (imgur.com é gratuito e simples) e cole o link no campo "URL da imagem"
no painel.

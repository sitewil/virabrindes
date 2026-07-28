# Seu site de catálogo com orçamento

Este pacote tem estes arquivos/pastas, que trabalham juntos:

- **index.html** — a página inicial (home): banner, categorias em destaque, "como funciona".
  É a porta de entrada do site.
- **produtos.html** — o catálogo completo com o orçamento automático, acessado pelo menu
  "Produtos" (ou clicando em uma categoria na home).
- **admin.html** — o painel visual onde você cadastra e edita produtos, e também edita
  os banners da home (aba "Banners da home" dentro do painel).
- **products.json** — o "banco de dados" simples com a lista de produtos.
- **banners.json** — os textos e categorias dos 4 blocos de banner da home.
- **css/style.css** — as cores, fontes e estilos compartilhados por todas as páginas.
- **images/** — as fotos dos produtos (miniaturas) e **images/full/** — versões maiores,
  usadas quando o cliente clica numa foto para ver em tamanho grande.

Todos esses arquivos e pastas precisam ser publicados **juntos, mantendo essa mesma
estrutura** (não mude nomes nem tire nada de dentro de subpastas).

⚠️ **Importante:** o nome da loja, e-mail de contato e textos do banner estão configurados
em **dois lugares** — no bloco `CONFIG` do `index.html` e no bloco `CONFIG` do
`produtos.html`. Ao trocar essas informações, edite os dois arquivos para manter tudo
consistente.

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

Abra o `index.html` **e também** o `produtos.html` em um editor de texto e procure o
bloco `const CONFIG = { ... }` perto do final de cada arquivo (troque nos dois). Lá
você troca:

- `siteName` — o nome da sua loja/negócio
- `tagline` — a frase curta ao lado do nome
- `ownerEmail` — **o e-mail para onde os pedidos de orçamento serão enviados** (só existe no `produtos.html`, é de lá que o pedido é enviado)
- `contactLine` — texto de contato do rodapé
- `heroTitle` e `heroLead` — o título e o texto do banner da home (só existem no `index.html`)

Cores do site: o arquivo `css/style.css` tem um bloco `:root { --bg: ...; --accent: ...; }`
com as cores principais, usado por todas as páginas — troque os códigos de cor
(hexadecimais) ali. O `admin.html` tem seu próprio bloco de cores separado (é uma
página só sua, não precisa ficar visualmente igual ao site público).

## 4. Como funciona o pedido do cliente

Quando o cliente clica em **"Solicitar orçamento"**, ele preenche nome e telefone, e o
site tenta enviar o pedido automaticamente pelo e-mail configurado em `ownerEmail`
(dentro do `CONFIG`, no `produtos.html`). Se você não configurar o EmailJS (veja abaixo),
o site usa o método simples: abre o aplicativo de e-mail do próprio cliente com o pedido
já escrito, pronto pra enviar — mas isso só funciona se a pessoa tiver um app de e-mail
configurado no aparelho dela. Por isso também tem os botões **"Copiar resumo"** (cola no
WhatsApp) e **"Gerar PDF"** (abre a tela de impressão do navegador, o cliente escolhe
"Salvar como PDF") como alternativas que sempre funcionam.

### Envio de e-mail confiável (opcional, recomendado)

Pra o pedido chegar direto no seu e-mail sem depender do aparelho do cliente, configure o
**EmailJS** (grátis até 200 e-mails/mês):

1. Crie uma conta em **[emailjs.com](https://www.emailjs.com)**.
2. No painel, vá em **Email Services → Add New Service**, escolha **Outlook** e conecte
   a conta `virabrindes@outlook.com` (login normal da Microsoft). Copie o **Service ID**
   gerado (algo como `service_abc123`).
3. Vá em **Email Templates → Create New Template**. Configure o campo "To Email" como
   `{{to_email}}` e monte o corpo do e-mail usando estas variáveis:
   `{{nome_cliente}}`, `{{telefone}}`, `{{email_cliente}}`, `{{itens}}`,
   `{{observacoes}}`, `{{data_pedido}}`. Copie o **Template ID** (algo como `template_xyz789`).
4. Vá em **Account → General** e copie a **Public Key**.
5. Abra o `produtos.html`, procure o bloco `CONFIG.emailjs` e cole os 3 valores:
   ```js
   emailjs: {
     serviceId: "service_abc123",
     templateId: "template_xyz789",
     publicKey: "AbCdEfGhIjKlMnOp"
   }
   ```
6. Suba o `produtos.html` atualizado. Pronto — a partir daí, o botão "Enviar por e-mail"
   passa a enviar automaticamente, direto pro seu Outlook, sem depender do cliente.

Enquanto esses 3 campos estiverem em branco, o site continua funcionando normalmente
com o método simples (mailto) + os botões de PDF e copiar.

Quando o cliente clica em **"Solicitar orçamento"**, ele preenche nome e telefone, e o
site abre o aplicativo de e-mail dele já com o pedido formatado, pronto pra enviar para
o seu e-mail (`ownerEmail`). Também tem um botão "Copiar resumo", caso o cliente prefira
colar o pedido no WhatsApp em vez de e-mail.

## 5. Como usar o painel de produtos (admin.html)

1. Acesse `seusite.com/admin.html`.
2. Digite a senha (o padrão é `trocar123` — **troque isso antes de publicar**, veja a
   dica de segurança abaixo).
3. O painel tem duas abas: **Produtos** e **Banners da home**.

### Aba Produtos
- Use a busca e o filtro de categoria para encontrar produtos no meio dos quase mil
  cadastrados — a lista é paginada (50 por página) pra não travar o navegador.
- Clique em **"+ Novo produto"** para cadastrar, ou em **"editar"** na tabela para mudar
  um produto existente.
- Cada produto pode ter uma **foto principal** (a que aparece no card do catálogo) e
  **fotos extras** (uma galeria — o cliente navega entre elas ao clicar na foto no
  catálogo, com setas de "próxima/anterior"). Pra adicionar, clique no quadrado com "+"
  ao lado das fotos já adicionadas; pra remover, clique no "✕" vermelho em cima da foto.
- Quando terminar as edições do dia, clique em **"⬇ Baixar products.json"**.

### Aba Banners da home
- Edite o título, o texto do botão e a categoria de cada um dos 4 blocos do banner —
  tudo por formulário, sem precisar mexer em código.
- A foto de cada banner é escolhida automaticamente a partir da categoria selecionada
  (pega a foto do primeiro produto ativo dessa categoria).
- Quando terminar, clique em **"⬇ Baixar banners.json"**.

### Publicando as alterações
Depois de baixar os arquivos (`products.json` e/ou `banners.json`), envie-os para a
hospedagem do site, substituindo os arquivos antigos (mesma pasta do `index.html`).
Pronto — as mudanças aparecem pra todo mundo que visitar o site.

### Sobre a senha do painel

A senha do `admin.html` (variável `ADMIN_PASSWORD` no começo do `<script>`) é uma trava
simples para afastar visitantes curiosos — **não é uma segurança real**, porque qualquer
pessoa que veja o código-fonte da página consegue encontrá-la. Não cadastre nada
sigiloso no painel. Se no futuro você quiser um login de verdade (com banco de dados,
múltiplos usuários, etc.), aí já é um projeto com um pouco mais de estrutura — posso te
ajudar a montar isso depois, se fizer sentido pro seu negócio.

## 6. Fotos dos produtos

No painel (`admin.html`), cada produto tem um campo **"Foto do produto"** onde você
escolhe uma imagem direto do computador ou celular. A foto é automaticamente recortada
e redimensionada na proporção usada pelos cards do site (4:3), com uma prévia mostrando
exatamente como ela vai aparecer — não precisa se preocupar com o tamanho ou formato da
imagem original.

Essa foto fica guardada diretamente dentro do `products.json` (não depende de nenhum
site externo). Isso é ótimo para simplicidade, mas deixa o arquivo maior conforme você
adiciona fotos — para um catálogo pequeno/médio isso não é problema.

Se preferir usar uma imagem que já está hospedada em outro lugar (por exemplo, um link
do imgur.com), ainda dá para colar o link no campo **"Ou cole o link de uma imagem"**,
logo abaixo — nesse caso a foto não fica embutida no arquivo.

Enquanto um produto não tem nenhuma foto definida, o site gera automaticamente uma
imagem de exemplo colorida com as iniciais do produto.

# Memphis.std — Página de vendas

Site estático (HTML/CSS/JS puro, sem build), tema escuro sobre fundo preto, usando a paleta da identidade.

## Arquivos

```
index.html                → o site inteiro (HTML + CSS + JS em um arquivo só)
precos.json               → valores dos pacotes (é este arquivo que o Admin grava)
galeria.json              → lista de fotos da galeria (gerada pelo script)
atualizar-galeria.command → script que prepara as fotos da galeria
galeria-novas/            → onde você joga as fotos novas antes de rodar o script
assets/img/               → hero, logo, ícones e galeria
assets/fonts/             → Archivo Variable (usada nos cards de valor)
```

## Publicar no GitHub Pages

1. Suba `index.html`, `precos.json`, `galeria.json` e a pasta `assets/` para a raiz do repositório.
2. Em **Settings → Pages**, em "Source" escolha a branch (ex: `main`) e a pasta `/ (root)`.
3. A URL fica em `https://<seu-usuario>.github.io/<nome-do-repo>/`.

## Área Admin — alterar os valores

Botão **Admin** no canto direito do rodapé. Senha: `Oceans`.

Dentro do painel dá para editar, de cada pacote:

- **Título** — o texto grande no topo do card (sai em caixa alta)
- **Subtítulo** — a linha fina abaixo do valor (ex: "Fotografia")
- **Descrição** — o texto centralizado na parte escura; Enter quebra a linha
- **Preço** e **Parcelas** — o "em até Nx" ao lado do valor (0 esconde a linha)
- **★** — marca qual card leva o selo "Mais escolhido". Um por categoria: marcar
  outro card de Fotografia tira o selo do anterior, mas não mexe em Vídeo nem Combos.

Três formas de salvar:

| Botão | O que faz |
|---|---|
| **Salvar preview** | Aplica só no seu navegador. Serve para conferir antes de publicar. |
| **Publicar no site** | Grava o `precos.json` direto no repositório via API do GitHub. Vale para todos os visitantes assim que o Pages reconstrói (~1 min). |
| **Baixar precos.json** | Baixa o arquivo para você subir manualmente, se preferir. |

### Configurar o "Publicar no site" (uma vez só)

No painel, abra **Conexão com o GitHub** e preencha:

- **Usuário / organização** e **Repositório** — os do repo do site
- **Branch** — normalmente `main`
- **Caminho do arquivo** — `precos.json`
- **Token de acesso** — gere em GitHub → *Settings* → *Developer settings* →
  *Personal access tokens* → **Fine-grained token**, com acesso só a esse repositório
  e permissão **Contents: Read and write**

Os dados ficam salvos no `localStorage` do seu navegador — não vão para o código do site,
então quem visitar a página não tem acesso a eles. Ainda assim: **não use o Admin em
computador compartilhado**, e se o token vazar, revogue no GitHub.

> A senha `Oceans` fica no JavaScript do site, ou seja, é uma trava de conveniência, não
> de segurança — quem abrir o código-fonte consegue lê-la. A proteção real é o token do
> GitHub: sem ele, ninguém publica nada.

## Atualizar as fotos da galeria

1. Coloque as fotos escolhidas na pasta **`galeria-novas/`** — pode ser em tamanho cheio,
   direto do tratamento. Aceita jpg, png, webp, heic e tif.
2. Dê **dois cliques** em `atualizar-galeria.command`.
3. Suba `galeria.json` e a pasta `assets/img/gallery/` para o repositório.

O script corta tudo em 2:3, redimensiona para 1200×1800, otimiza, renomeia em sequência
(`gallery-01`, `gallery-02`, …) e reescreve o `galeria.json`. A galeria anterior **não é
apagada**: vai para `assets/img/_galeria-anterior-<data>/`, caso você queira voltar atrás.

**A ordem no site é a ordem alfabética dos arquivos.** Por isso vale numerar:
`01-retrato-corporativo.jpg`, `02-still-de-produto.jpg`, e assim por diante. O nome também
vira a legenda da foto (o número da frente é descartado, hífens e underscores viram
espaço) — o que ajuda no acessibilidade e no Google. Dá para ajustar qualquer legenda
depois editando o `galeria.json` na mão.

As **10 primeiras** aparecem de cara; o resto entra no botão "Ver mais fotos". Para mudar
esse número, altere `visiveis` no `galeria.json` (ou a variável `VISIVEIS` no topo do
script, se quiser que valha sempre).

> Por padrão o script usa o `sips`, que já vem no macOS, e gera **JPEG**. Se você rodar
> `brew install webp` uma vez, ele passa a gerar **WebP** automaticamente — mesma
> qualidade visual com arquivos ~40% menores. Vale a pena.

## Notas técnicas

- **Tipografia**: o site usa Plus Jakarta Sans (Google Fonts). Os **cards de valor** usam
  **Archivo Variable**, hospedada junto com o site em `assets/fonts/`. É uma variável com
  eixo de largura (`wdth` 62–125), o que permite reproduzir as larguras pedidas no layout:
  Black para o título, **Compressed Bold** no valor, Medium nas parcelas e na descrição, e
  **Expanded** no subtítulo, no botão e no selo — todos com tracking −30 (−0.03em), como
  especificado. As SF Pro/SF Compact da pasta *Identidade* não podem ser embutidas em site
  público por licença da Apple; a Archivo é a substituta mais próxima com esses eixos.
- **Proporções dos cards**: todas as medidas do card de valor (paddings, raios, tamanhos de
  fonte, botão) estão em `cqw` — porcentagem da largura do próprio card — calibradas sobre
  o mockup `Identidade/Valores.jpg`. O card mantém exatamente as mesmas proporções em
  qualquer largura de tela.
- **Paleta**: definida em variáveis CSS no `:root` — `--orange #F26A05`, `--cyan #05DBF2`,
  `--yellow #F6FF00`, `--mint #05F2DB`, `--cream #FCF8E6` sobre fundo `#000`.
  Para trocar qualquer cor do site, mude só ali.
- **Carga dos preços**: o site lê `precos.json` a cada visita (com cache desligado) e cai
  nos valores embutidos no `index.html` se o arquivo não existir. O preview local só
  prevalece enquanto for mais novo que o publicado.
- **Barra flutuante**: aparece só depois que o visitante seleciona o primeiro pacote,
  mostrando o total em tempo real. O botão "Ver seleção" rola até o card do orçamento.
- **Galeria**: o `index.html` não tem mais a lista de fotos escrita à mão — ele lê o
  `galeria.json` a cada visita e cai numa lista embutida se o arquivo não existir. Por isso
  adicionar ou remover fotos não exige mexer em HTML.
- **Hero**: `hero-foto.webp` (~600 KB) com fallback PNG.

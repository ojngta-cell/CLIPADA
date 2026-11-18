# CLIPADA

Robô de publicação das clipadas do JNGTA (Rádio Pirata 107,9). Começa só no Instagram
(Reels); Facebook e YouTube já funcionam se um dia forem ligados.

Cópia do robô do Sabia dessa? (`vitorjn09/jasabiadisso-midia`), com agenda,
biblioteca e cronograma vazios.

- `agenda/`: os posts agendados, um por pasta (veja `agenda/README.md`).
- `publicador/publicar.py`: publica pelas APIs oficiais da Meta e do YouTube.
- `.github/workflows/publicar.yml`: roda o publicador a cada 15 minutos.

O repositório precisa ser público: o Instagram baixa as imagens pelo link
`raw.githubusercontent.com`. Os vídeos são enviados direto pelo robô.

Segredos (Settings > Secrets and variables > Actions): `META_TOKEN`,
`FB_PAGE_ID`, `IG_USER_ID` (opcional), `YT_CLIENT_ID`, `YT_CLIENT_SECRET`,
`YT_REFRESH_TOKEN`, e `FB_PAGE_TOKEN` (opcional).

Teste manual: aba Actions > Publicar posts > Run workflow, com modo `conexao`
(confere os acessos e mostra com qual app o Facebook publica), `verificar`
(confere os posts sem publicar) ou `diagnostico` (só leitura: status e plays
dos últimos reels da página).

### Facebook (Reels)

- O app da Meta que publica no Facebook precisa estar **Ao vivo** em
  developers.facebook.com. Post feito por app em modo Desenvolvimento sai pela
  API, mas ninguém vê (0 views). Sem CNPJ: app sem portfólio empresarial, caso
  de uso "Gerenciar tudo na sua Página".
- `FB_PAGE_TOKEN`: token de PÁGINA desse app publicado. Sem ele, o robô tira o
  token da página do `META_TOKEN` (`GET /{FB_PAGE_ID}?fields=access_token`).
  Gerar: Graph API Explorer (app certo) > token de usuário com
  `pages_show_list`, `pages_read_engagement`, `pages_manage_posts`,
  `pages_read_user_content`, `pages_manage_metadata`, `read_insights` >
  Estender (60 dias) > `GET me/accounts` > `access_token` da página. O token
  cai se trocar a senha do Facebook ou sair de todas as sessões (erro 190).
- Reel: `video_reels` start > envio ao rupload > finish com `video_state=PUBLISHED`.
- Os segredos são lidos com `.strip()` e qualquer token é apagado das mensagens
  de erro antes de gravar no `estado.json` (o repositório é público).
- Antes de reenviar à mão, o workflow confere se já tem execução rodando ou na
  fila, e sempre lê a ponta do ramo, para não postar duplicado.
- Alcance: modo `diagnostico`, ou abrir
  `facebook.com/plugins/video.php?href=<link do reel>` sem login. Se aparecer
  "indisponível", o app não está publicado.

## Painel

`painel/index.html` é uma página para usar o robô sem mexer no GitHub: mostra
a agenda e o estado de cada post, deixa adicionar, editar e apagar posts e
rodar o robô (testar conexão, verificar, publicar) com confirmação antes.
Abra o arquivo no navegador, ou ative o GitHub Pages (Settings > Pages >
Deploy from a branch > `main` / root) e use
`https://ojngta-cell.github.io/CLIPADA/painel/`. Para mudar algo,
a página pede um token do GitHub (fine-grained, só este repositório, com
Contents e Actions em Read and write), que fica guardado só no navegador.

## Análises

`.github/workflows/analises.yml` roda `publicador/analises.py` uma vez por dia
(e pelo botão "Atualizar números" do painel). Ele busca seguidores, alcance,
visualizações, interações e os melhores posts do Instagram, a página do
Facebook e o canal do YouTube, e grava tudo cifrado em
`dados/analises.cifrado.json`. Crie o segredo `ANALISES_SENHA` com uma senha
e digite a mesma senha no painel (Conectar > Senha das análises). O token da
Meta precisa das permissões `instagram_manage_insights` e `read_insights`.

A mesma coleta lê os comentários de cada post (até 100 por post, mais
recentes primeiro) para a aba Análises mostrar, só para leitura. Guarda só o
texto, a data e as curtidas, sem o nome de quem comentou, e tira os
comentários do próprio perfil. O robô não responde comentários. Permissões:
`instagram_manage_comments` (Instagram), `pages_read_user_content` (Facebook)
e o escopo `youtube.force-ssl` no `YT_REFRESH_TOKEN` (YouTube). Se faltar
alguma, a rede aparece com o aviso no fim da aba e o resto continua.

## Biblioteca e cronograma

- `biblioteca/<nome>/`: ficha (`ficha.json`) e capa de cada vídeo pronto. O vídeo fica na Release
  `biblioteca`, fora do repositório. Para incluir um vídeo, envie `biblioteca/<nome>/video.mp4`,
  `capa.jpg` e `ficha.json` num ramo `entrada/<nome>`: o workflow `biblioteca.yml` sobe o vídeo
  para a Release, leva a ficha e a capa para a `main` e apaga o ramo.
- `cronograma/cronograma.json`: o plano de conteúdo, editado pela aba Cronograma do painel.
- `relogio.yml`: faz o robô publicar na hora certa, sem depender do cron do GitHub.

## Clipadas

- Na legenda de cada clipada, sempre o crédito do streamer: `🎥 Clipe: @canal (twitch.tv/canal)`.
- Corte de live de outro streamer só com autorização dele ou campanha aberta.
- Nada de gameplay vazada do GTA 6 nem música/rádio do jogo (derruba o post).

# TikTok pelo vidIQ

O robô do GitHub não posta no TikTok (o TikTok só deixa apps aprovados postarem
em público). Quem posta é o Claude, pelo conector do vidIQ, numa rotina diária.
O painel é onde você pede e acompanha.

## Como pedir

- **Biblioteca → vídeo → TikTok "Quero"**: entra na fila, sem horário (o Claude sugere um).
- **Agendar** com o chip **TikTok** marcado: entra na fila com o mesmo horário das outras redes.

## O que a rotina faz

1. Lê as fichas da biblioteca com `tiktok.status = "pedido"` (ou `"erro"`).
2. Sobe o vídeo no vidIQ e monta o rascunho (legenda da ficha, horário de `tiktok.quando`).
3. Pede sua aprovação no chat do projeto. Quem pode ver, comentários e música
   você escolhe em cada post (regra do TikTok).
4. Depois do seu ok, agenda no vidIQ e grava o andamento na ficha.
5. Uma vez por dia atualiza `dados/tiktok.json` (números públicos do perfil e dos
   vídeos) e os `numeros` de cada ficha postada.

## Campos na `ficha.json`

```json
"tiktok": {
  "status": "pedido | preparado | agendado | postado | erro",
  "quando": "2026-11-20T19:00:00-03:00",
  "em": "quando o status mudou",
  "post_id": "id do post no vidIQ",
  "link": "https://www.tiktok.com/@.../video/...",
  "motivo": "texto do erro",
  "manual": true,
  "numeros": { "views": 0, "curtidas": 0, "comentarios": 0, "reposts": 0, "atualizado": "..." }
}
```

`manual: true` = você marcou "Já está lá" (postou à mão).

## `dados/tiktok.json`

```json
{
  "atualizado": "2026-11-20T09:00:00-03:00",
  "conta": { "handle": "...", "seguidores": 0, "curtidas": 0, "videos": 0 },
  "historico": [{ "dia": "2026-11-20", "seguidores": 0 }],
  "videos": [{ "link": "...", "legenda": "...", "quando": "...", "imagem": "...",
               "views": 0, "curtidas": 0, "comentarios": 0, "compartilhamentos": 0, "biblioteca": "nome" }]
}
```

O painel mostra isso na aba Análises → TikTok.

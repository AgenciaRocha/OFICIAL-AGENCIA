# Agência Rocha — site oficial

Site institucional em **arquivo único**: todo o HTML, CSS, JavaScript e imagens
vivem dentro de `index.html`. O repositório tem apenas:

```
index.html          o site inteiro (CSS inline, imagens em base64/WebP)
robots.txt
sitemap.xml
assets/             banner-og.jpg e logo-agr.png (imagem de compartilhamento)
```

## Fluxo de trabalho (sempre)

A cada alteração aprovada, **atualizar o GitHub sem precisar ser lembrado**:

1. Commit na branch de trabalho
2. `git push -u origin <branch>`
3. Abrir PR para `main` e fazer o merge
4. Enviar o `index.html` atualizado para o usuário — a publicação na hospedagem
   é manual (ele sobe o arquivo), então o site no ar só muda depois disso

O GitHub Pages está configurado por workflow (`.github/workflows/deploy-pages.yml`),
mas falha até que alguém habilite `Settings → Pages → Source: GitHub Actions`.
O site em produção (agenciarocha.com) é servido por outra hospedagem.

## Como testar

Não existe build. Servir a pasta e abrir no Chromium (Playwright já está
disponível em `/opt/pw-browsers/chromium`):

```bash
python3 -m http.server 8791
```

Conferir sempre em **1440px, 768px e 360px** antes de commitar. Google Fonts e
GTM são bloqueados pelo proxy do ambiente — interceptar e abortar requisições
externas para medir a página sem esperar timeout.

## Convenções do projeto

- **Botões**: agendamento (Calendly) sempre `btn btn-primary` (azul), WhatsApp
  sempre `btn btn-wa` (verde). Todos com `min-height:56px` — a altura não muda
  se o rótulo quebrar em duas linhas.
- **Parágrafos de seção** usam a classe `.lead`. Não criar tamanho de fonte
  próprio por seção.
- **Blocos animados** têm a classe `.rv` e aparecem via IntersectionObserver;
  existe um fallback em `<noscript>` que os mantém visíveis.
- **Seções** têm `scroll-margin-top` para a âncora não parar sob o cabeçalho fixo.
- **Copy do banner (hero) não muda** a não ser que seja pedido explicitamente.
- **Nunca inventar números** (anos, clientes, resultados). Os blocos de prova e
  de números da agência ficam comentados no código até chegarem dados reais.

## Dados fixos

| | |
|---|---|
| WhatsApp | `5571992641675` |
| Instagram | `renatorochapro` |
| Agenda | `https://calendly.com/agenciarocha/45min` |
| GTM | `GTM-T5KZ9XP` |
| Endereço | Rua Maceió, Km 25 · Simões Filho — BA · 43705-570 |
| Horário | Seg a sáb, 8h às 18h |
| Desde | 2020 |
| Atendimento | Todo o Brasil (a copy também cita Portugal) |

Endereço, horário e ano de fundação aparecem no JSON-LD e numa linha discreta
do rodapé — o Google cruza esses dados com o perfil no Google Meu Negócio.

## Rastreamento

Os CTAs carregam `data-cta` e um listener delegado empurra para o `dataLayer`:

- `clique_whatsapp` — links `wa.me`
- `clique_agendar` — links do Calendly

Cada evento leva `cta_local` (ex.: `hero-whatsapp`) e `cta_texto`. Ao criar um
botão novo, incluir o `data-cta` seguindo o padrão `<seção>-<ação>`.

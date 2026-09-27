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
4. **Sincronizar a branch `add-chatwoot-widget` com a `main`** (fast-forward:
   `git push origin main:refs/heads/add-chatwoot-widget`) — ver aviso abaixo

### Publicação (Hostinger com deploy automático via GitHub)

Descoberto em 27/09/2026: a Hostinger está conectada ao GitHub com **implantação
automática**, mas configurada pra puxar da branch **`add-chatwoot-widget`**, não
da `main` (ficou assim porque essa foi a primeira branch criada quando a
integração GitHub↔Hostinger foi montada, no PR #9). Isso significa:

- Merge na `main` **não** publica sozinho. Só publica o que chega em
  `add-chatwoot-widget`.
- Até alguém trocar isso no painel da Hostinger (Sites → agênciarocha.com →
  Conectado com GitHub → trocar branch pra `main`), **todo PR mergeado precisa
  ser replicado manualmente pra `add-chatwoot-widget`** com o fast-forward do
  passo 4 acima, senão o site no ar fica desatualizado silenciosamente — não dá
  erro nenhum, só não muda nada.
- Pra confirmar que uma publicação realmente chegou: `curl -sI
  https://xn--agnciarocha-obb.com/ | grep -i last-modified` (esse é o domínio
  real, com acento — agenciarocha.com sem acento é outro domínio, parado no
  Squarespace, não confundir).

O GitHub Pages está configurado por workflow (`.github/workflows/deploy-pages.yml`),
mas falha até que alguém habilite `Settings → Pages → Source: GitHub Actions`.
Isso é irrelevante agora que se sabe do deploy automático via Hostinger acima.

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
| Instagram | `agenciarochapro` |
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

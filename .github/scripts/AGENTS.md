# AGENTS.md — Diretriz local de localização: torbox-app

## 1. Domínio

Interface web (Next.js App Router + React, com casca Tauri para desktop) para gerenciamento de
downloads em serviço Debrid. O usuário é power-user: envia lote, escolhe arquivos dentro de um
torrent, opera Usenet/NZB, automatiza por RSS, monitora limites de armazenamento temporário e acompanha
cache no servidor.

Esse público aprendeu o vocabulário na documentação da API e em fóruns. Traduzir "friendly" demais
faz o recurso parecer outro produto; manter inglês onde já existe consenso também cansa. A decisão se
toma por evidência de uso, não por preferência.

## 2. Onde o texto vive (verificado no clone)

| Caminho | Papel |
| --- | --- |
| `src/i18n/messages/en.json` | catálogo fonte — **1.607 folhas** (1.551 traduzíveis + 26 `Admin.*` + 30 `CustomViews.presets.*`) |
| `src/i18n/messages/pt-BR.json` | **delta pt-BR** — só chaves cujo valor difere do inglês (1.464 folhas no estado atual) |
| `src/i18n/messages/inherited.json` | registro de chaves cujo pt-BR é **idêntico ao inglês** (87 entradas `pt-BR` no estado atual) |
| `src/i18n/messages/{de,es,fr,ja,pl}.json` | catálogos delta existentes — evidência de tom e densidade |
| `src/components/` | UI; todo texto vem do catálogo |
| `src/app/[locale]/` | rotas localizadas (`(app)`, `admin`) — sem texto hardcoded |
| `scripts/i18n.js` + `scripts/i18n/lib/` | CLI de tradução, sync e validação |

### Modelo real: delta + inherited (não há paridade total)

`en.json` é a fonte. Para cada locale não-inglês o runtime faz `deepmerge(en, delta)` em
[`src/i18n/request.ts`](src/i18n/request.ts); chave ausente do delta herda o inglês.

O validador, porém, rejeita **qualquer** chave do delta cujo valor seja igual ao inglês. Por isso o
repositório separa dois conjuntos complementares:

- **delta** (`pt-BR.json`): chaves traduzidas de fato; nenhum valor idêntico ao inglês.
- **inherited** (`inherited.json` → `pt-BR`): chaves em que o pt-BR é intencionalmente igual ao inglês
  (termos técnicos, marcas, siglas, URLs, unidades). São registradas via
  `bun scripts/i18n.js translate --apply`, que as remove do delta e as grava no ledger.

Invariante: **delta ∪ inherited = 1.551 chaves traduzíveis**, sem interseção, excluindo `Admin.*` e
`CustomViews.presets.*`. Um `pt-BR.json` com paridade de 1.607 chaves falha no validador por conter
chaves excluídas e cópias idênticas ao inglês. O alvo correto é 1.551 chaves cobertas entre delta e
inherited — não paridade literal com `en.json`.

Se `en.json` mudar para uma chave herdada, a entrada obsoleta é descartada e a chave volta para a fila
de tradução (`translate`). O ledger nunca deve ser editado à mão sem rodar o validador.

## 3. Protocolo de inferência semântica

1. **Ler a chave e o namespace.** `downloads.batch.upload.title` informa mais que o valor: namespace
   dá a área do produto, sufixo dá o papel (`.title`, `.description`, `.error`, `.action`).
2. **Olhar o componente consumidor** em `src/components/` para saber o slot e o estado (vazio,
   carregando, erro, sucesso).
3. **Classificar o vocabulário em duas faixas:**
   - *vocabulário de operação* — o que o usuário já viu na API/fóruns; preservar o termo consagrado
     mesmo que exista sinônimo em português;
   - *interface comum* — navegação, botões, mensagens de erro; aqui vale o português corrente.
4. **Consultar evidência.** Como concorrentes de Debrid e clientes de torrent em português nomeiam
   lote, fila, seleção de arquivos e cache? Cite a fonte.
5. **Testar em tela estreita.** Muitos rótulos são botões de tabela; se não couber, corte.
6. **Registrar** decisão, faixa e evidência.

## 4. Invariantes técnicas

- Chaves, namespaces e valores de enum não se traduzem.
- ICU intacto: `{count}`, `{name, plural, one {…} other {…}}`, tags `<strong>`; nenhum placeholder
  pode desaparecer nem ser renomeado.
- Campos de API (`asset_id`, `download_state`, `torrent_id`) e rotas permanecem literais.
- `pt-BR.json` é **delta**: subconjunto de `en.json`, sem `Admin.*`, sem `CustomViews.presets.*` e sem
  nenhum valor idêntico ao inglês.
- Valor idêntico ao inglês é intencional? Vai para `inherited.json` (via `translate --apply`), nunca
  para o delta.
- Toda entrada de `inherited.json` deve bater exatamente com o valor atual de `en.json`.
- JSON válido, UTF-8 sem BOM, LF, chaves ordenadas — o validador compara byte a byte.
- Nenhuma string hardcoded em `src/app/` nem em `src/components/`.
- `Common.time.*` preserva os espaços semânticos do inglês: `ago` = `" atrás"` (espaço à esquerda) e
  `in` = `"em "` (espaço à direita). Unidades: `d`, `h`, `s` iguais; `m` → `min`; `mo` → `m`; `y` → `a`.

## 5. Critérios de aceitação

- Botão ≤ 22 caracteres; rótulo de coluna ≤ 18; descrição de estado vazio ≤ 120.
- Caixa de frase; ação no infinitivo; estado no particípio.
- Erro de API: o que falhou + o que fazer; código HTTP preservado entre parênteses.
- Unidades no padrão do projeto (`Intl`), não escritas à mão.
- Consistência entre telas: o mesmo objeto tem o mesmo nome em lista, detalhe e formulário.
- Tipografia: reticências `…` e aspas curvas `“ ”` — não usar `...` nem aspas retas.

## 6. Proibições

- Traduzir termo de operação consagrado por sinônimo doméstico que o usuário não reconhece.
- Criar `src/app/[locale]/pt-BR/**` ou qualquer texto de interface em arquivo de rota.
- Traduzir chave, campo de API, enum ou nome de serviço.
- Copiar catálogo de outro idioma como se fosse pt-BR.
- Inventar termo para recurso cujo nome é marca/produto do serviço.
- Copiar valor inglês para o delta (use `inherited.json` via `translate --apply`).

## 7. Verificação

```bash
bun scripts/i18n.js verify            # gate oficial: JSON, ICU, deltas, inherited, ordenação
bun run i18n:verify                    # alias do gate
bun scripts/i18n.js translate --locale pt-BR   # fila de pendências deve ser {}
bun run lint
```

Checklist: `verify` sem erros; fila de pendências vazia; ICU balanceado; delta ∪ inherited = 1.551;
nenhum texto hardcoded em rota/componente.

Nota de ambiente: em checkouts Windows com `core.autocrlf=true` os catálogos chegam com CRLF e o
validador acusa "keys are not sorted". Rode `bun scripts/i18n.js sync` uma vez para regravá-los com LF
(é só normalização de EOL; o conteúdo não muda).

## 8. Decisões terminológicas pt-BR

| EN | pt-BR | Regra |
| --- | --- | --- |
| upload (link/magnet) | **Adicionar** | adicionar torrent, NZB ou WebDL a partir de link/magnet |
| upload (arquivo físico, nuvem, fila de envio) | **Enviar / Envio** | .torrent/.nzb, upload para provedor, página Uploads |
| torrent(s) | **torrent(s)** | termo consagrado; não traduzir para "torrente" |
| seed / seeding | **seed / seeding** | "Parar seed", "Seeding", "Fazer seed" |
| peer(s) | **peer(s)** | manter |
| ratio | **ratio** | manter |
| tracker | **tracker** | manter |
| magnet | **magnet** | "link magnet" |
| hash | **hash** | manter |
| ETA | **ETA** | manter |
| Usenet / NZB / RSS | **Usenet / NZB / RSS** | manter |
| Airlock | **Airlock** | marca do TorBox; "no Airlock", "Dados no Airlock" |
| Protect / Protected | **Proteger / Protegido** | diferente de Airlock (bloqueio/armazenamento) |
| stream(s) | **stream(s)** | manter |
| WebDL | **WebDL** | grafia única; nunca "Webdl" nem "Web Download" |
| Pass Check | **Ignorar verificação de cache** | sentido real, não literal |
| delete | **Excluir** | exclusão definitiva |
| remove | **Remover** | desvincular/retirar de lista, tag, arquivo, conta |
| cached (cache compartilhado TorBox) | **em cache** | "Em cache", "Somente em cache", "sem cache" |
| view (visão salva) | **visualização** | "Criar visualização" |
| rate limit | **limite de requisições** | "limite de criações" quando for cota de create |
| backend | **backend** | manter |

## 9. Handoff

| chave | EN | faixa (operação / interface) | pt-BR escolhido | evidência | alternativa rejeitada | motivo |
| --- | --- | --- | --- | --- | --- | --- |
| `RssFeeds.passCheck` | Pass Check | interface | Ignorar verificação de cache | descrição EN "Skip cache checking for this feed" | "Pasar Verificación" (es) | literal não comunica a ação |

Termo de operação sem evidência citada volta para revisão.

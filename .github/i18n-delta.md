# Delta upstream — torbox-app

- Gerado em: 2026-11-13T19:28:02.6486734+00:00
- Base local: e241f40dae54520b45e8ebccff1e99d0e06f4837
- Upstream: upstream/main / 57e1626b7b991c3b0e55a680b5f4a465052f3dd3
- Chaves pendentes: 3

## Chaves detectadas

```json
{
  "tool": "sync-check.ps1",
  "version": "1.0.0",
  "generatedAt": "2026-11-13T19:28:02.0056617+00:00",
  "root": "D:\\a\\torbox-app",
  "mode": "nofetch",
  "summary": {
    "analyzed": 1,
    "updated": 0,
    "newStrings": 1,
    "removals": 0,
    "gaps": 0,
    "errors": 0,
    "pendingKeys": 3,
    "missingKeys": 0
  },
  "baselineUpdated": [],
  "repos": [
    {
      "name": "torbox-app",
      "branch": "main",
      "head": "ff5358518db86a20f1a15de386d8e69585907cf6",
      "headShort": "ff535851",
      "upstreamRef": "upstream/main",
      "upstreamCommit": "57e1626b7b991c3b0e55a680b5f4a465052f3dd3",
      "baseCommit": "e241f40dae54520b45e8ebccff1e99d0e06f4837",
      "baseSource": "baseline",
      "ahead": 11,
      "behind": 3,
      "status": "Novas strings",
      "added": 3,
      "modified": 0,
      "removed": 0,
      "pending": 3,
      "baseKeys": 1551,
      "missingKeys": 0,
      "extraKeys": 0,
      "coveragePercent": 100.0,
      "dirtyFiles": [],
      "sourceDirty": [],
      "fetchError": "",
      "error": "",
      "warnings": [
        "Modo offline (-NoFetch): git fetch não executado."
      ],
      "files": [
        {
          "path": "src/i18n/messages/en.json",
          "target": "src/i18n/messages/pt-BR.json",
          "targetExists": true,
          "baseKeys": 1551,
          "upstreamKeys": 1554,
          "added": [
            "Columns.relevance",
            "SearchBar.searchDownloadsHelpRanking",
            "SearchBar.sortByRelevance"
          ],
          "modified": [],
          "removed": [],
          "missing": [],
          "extra": [],
          "warning": ""
        }
      ]
    }
  ]
}

```

## Sugestões DeepSeek

| chave | EN | sugestão pt-BR | justificativa |
| --- | --- | --- | --- |
| `Columns.relevance` | Relevance | Relevância | Rótulo de coluna (≤ 18 caracteres: 10). Interface comum; termo corrente em busca/ordenação, sem consagração técnica que exija manter o inglês. |
| `SearchBar.searchDownloadsHelpRanking` | Search downloads help ranking | Ajuda a classificar os resultados da busca | Texto de ajuda (descrição). Interface comum; "ranking" não é termo de operação consagrado no domínio Debrid — "classificar" comunica a ação. Alternativa rejeitada: "Ajuda o ranking da busca" (ambíguo sobre o que é ranqueado). |
| `SearchBar.sortByRelevance` | Sort by relevance | Ordenar por relevância | Ação no infinitivo, consistente com demais opções de ordenação. Interface comum; "relevância" alinhado a `Columns.relevance` (mesmo objeto, mesmo nome). |

> Revise as sugestões e aplique apenas as chaves listadas, seguindo o AGENTS.md local. Este PR não altera os catálogos automaticamente.

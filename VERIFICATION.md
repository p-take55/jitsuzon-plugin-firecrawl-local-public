# Firecrawl Local Pluginの確認結果

確認日: 2026-11-23。macOS arm64、Firecrawl Local 0.1.1、Node.js 22.17.0、公式firecrawl-mcp 3.41.0。

## 動作確認済み

- 実MCPのinitialize・tools/list成功。公式の31toolsが表示される。
- `firecrawl_scrape`で公開ページ `https://example.com/` の既知本文をMarkdownで取得。HTTP200、source=local、engine=local-fetch、creditsUsed=0。
- aachatの専用検証環境で、配布設定を導入したCodex Agentをresume。MCP scrapeを1回実行し、取得本文に沿った日本語要約と出典URLを返した。保存されたtool出力でも本文・local source・HTTP200を確認。
- Cloud検索要求は明示拒否。到達不能なURLのscrapeはFETCH_FAILEDを返し、検証用Cloud転送先へのPOSTは0件。private URLはBLOCKED_TARGETで拒否。
- aachatの既存Plugin normalizer・MCP validator・runtime resolverと、導入時command確認・MCP接続probeが成功。

## 配布物の移行

検証済みのMCP設定とcommand確認設定を独立repository rootへ移した。MCP設定はバイト単位で同一。Plugin manifestには配布用のschema・version・descriptionだけを加え、READMEの参照先をこのrepository内へ修正した。新しいGit履歴で作成し、aachat本体やAgentの履歴・認証情報は含めない。

## 確認していないこと

Discoverからの新規導入、JavaScript描画・Chrome、PDF、crawl/map等の通常動作、全31toolsの副作用は未検証。元の実Agent確認は専用環境へのfixture配置後のresumeで、新規Discover導入全体の確認ではない。

Cloud転送停止は、取得本文を要約用LLMへ渡さない保証ではない。全ネットワーク通信をcaptureした証明でもない。公式MCPのscrape以外を強制禁止する仕組みは含めない。

## 0.2.0で加えたもの（未検証）

Cloudを使うかを利用者に確認して `mcp.json` を切り替えるSkill `firecrawl-cloud-setting` と、READMEの手順を加えた。`FIRECRAWL_CLOUD=auto` での取得、`firecrawl-local login` 後の動作、Skillに沿った切り替えの実動作は確認していない。

# Firecrawl Local Plugin

公開WebページのURLをAgentへ渡し、[Firecrawl Local](https://github.com/firecrawl/firecrawl/tree/main/apps/local)で本文をMarkdownとして取得するaachat Pluginです。取得した本文の要約はAgentが行います。

この独立repositoryがPluginの配布単位です。現在は公開前の候補で、Discoverにはまだ登録していません。利用者が導入するのはrootの `plugin.json` と `mcp.json`、この導入手順です。Firecrawl本体・Node.js・ブラウザは同梱しません。未登録のcatalog IDによるinstallはできません。

## 実行端末の準備

RustとC compiler、Node.js 22以上を用意します。aachat Agentが実行される端末で以下を実行し、AgentのPATHから `firecrawl-local`、`node`、`npx` が見えるようにします。

```sh
cargo install firecrawl-local --version 0.1.1 --locked
firecrawl-local --version
node --version
npx --version
```

依存の導入は自動実行しません。Chrome/ChromiumはJavaScript描画が必要なページで使われます。ブラウザなしで取得できないページもあり、全サイトの本文取得は保証しません。

API key、Firecrawlへのlogin、Docker、Redis、Postgresは不要です。本Pluginは単一binaryのLocalを使います。Firecrawl Cloudや、Docker等でfull serverを運用するself-host構成とは異なります。

## Agentへ追加して使う

公開後は既存の **Add to agent** から対象Agentへ追加します。Plugin配置後は新しいSessionを開始します。Readyはcommand確認とMCP initialize・tools/listの成功を示し、対象URLの本文取得成功は別に確認します。

依頼例：

> https://example.com の本文を取得して、日本語で要約して。取得できない場合は理由を教えて。

Agentは `firecrawl_scrape` に対象の公開URLと `formats: ["markdown"]` を渡し、返った本文に基づいて要約します。本文が得られなければ取得失敗として扱い、推測で補いません。URLの出典も返します。

公式の `firecrawl-local mcp --public-only` をstdioで起動します。Localは空いているloopback portにAPIを起動し、公式 `firecrawl-mcp@3.41.0` をnpxで取得・起動します。初回はnpmへの通信とcacheへの書込みがあり、準備に時間がかかる場合があります。`firecrawl-local init` による他のAIツールの共有設定書換えは行いません。

## 取得範囲

`FIRECRAWL_CLOUD=off` をMCP環境に固定し、取得できないページのCloud fallbackとCloud検索を停止します。対象Webサイトへの通信とnpm package取得は行います。取得本文は要約のためAgentのLLMへ渡ります。Firecrawl Cloudへの転送停止は、要約まで端末内で完結する保証ではありません。`--public-only` によりlocalhostやprivate networkへの取得を拒否します。

本Pluginの用途は公開URLの単一ページ本文取得です。公式MCPにはcrawl/map、skillを作成・変更する `firecrawl_save_skill` 等も表示されます。このPluginはtoolを非表示にしたり、scrape以外の呼出しを強制的に禁止したりしません。Cloud無効化は全toolの副作用を無効にする設定ではありません。この候補ではscrape以外の通常動作を検証していません。Cloud専用toolも表示されることがあり、Cloudを必要とする要求は失敗します。検索・巡回・ログインが必要なページへの対応は提供範囲に含めません。

導入例と検証はLocal 0.1.1を使用します。command確認はLocalのversionを強制しません。MCP packageは宣言で3.41.0を指定します。npmの推移依存全体は固定していないため、将来の取得結果まで完全には再現しません。

## 取得できない場合

- command確認失敗：AgentのPATHとLocal / Node.js / npxのversionを確認します。
- MCP起動失敗：npm取得の接続とcache、初回起動時間を確認します。初回取得がaachatの確認期限を超えた場合もReadyにはなりません。
- Cloud無効のエラー：本Pluginの設定どおりです。Cloudを有効にして回避せず、公開URLの本文取得という依頼範囲を確認します。
- 本文なし・ブラウザ警告・サイト側の拒否：その理由を報告します。Chromeの有無、公開状態、対象サイトの制限を確認します。

実測範囲は [VERIFICATION.md](VERIFICATION.md) を参照してください。

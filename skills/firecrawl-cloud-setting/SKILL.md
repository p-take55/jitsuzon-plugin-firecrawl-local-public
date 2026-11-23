---
name: firecrawl-cloud-setting
description: Firecrawl Local で取れないページを Firecrawl Cloud に回すかどうかを、利用者に聞いて切り替える。API キーを持っている人には端末でのログインを案内する。Use when a page could not be fetched locally, or when the user asks to use or stop Firecrawl Cloud or mentions a Firecrawl API key.
metadata:
  aachat.headline.ja: 取れないページを Cloud で取り直すか切り替える
  aachat.headline.en: Choose whether blocked pages may go to Firecrawl Cloud
  aachat.description.ja: Firecrawl Local で取れなかったページを Firecrawl Cloud に回すかどうかを利用者に聞いて切り替え、API キーの登録を案内する。
  aachat.description.en: Ask whether pages that Firecrawl Local cannot fetch may be retried on Firecrawl Cloud, switch the setting, and guide API key sign-in.
  aachat.discovery.listed: "false"
---

# 取れないページを Cloud で取り直すか切り替える

この Plugin は、最初は端末の中だけでページを取得する（`FIRECRAWL_CLOUD=off`）。Cloud を使うかどうかは利用者が決める。Agent が自分の判断で切り替えない。

## 提案するとき

次のどちらかのときだけ、利用者に一度聞く。

- ページが取れなかったとき（本文なし、サイト側の拒否、ブラウザが必要など）。取れなかった理由を先に伝える。
- 利用者が Cloud や API キーのことを口にしたとき。

聞き方の例:

> このページは、この端末からは取得できませんでした。取れなかったページを Firecrawl Cloud に回して取り直すこともできます。その場合、ページの URL が Firecrawl に送られます。Web 検索など Cloud の機能も使えるようになります。Cloud を使うようにしますか？（使わない場合は、これまでどおり端末の中だけで取得します）

答えが「はい」とはっきりしないときは、`off` のままにする。断られたら、同じ会話の中で繰り返し聞かない。

## 切り替える

1. この Plugin が置かれた場所の `mcp.json`（通常は `.agents/plugins/firecrawl-local/mcp.json`）を開く。
2. `mcpServers.firecrawl.env.FIRECRAWL_CLOUD` を書き換える。Cloud を使うなら `auto`、やめるなら `off`。ほかの行は変えない。
3. commit・push する。
4. 「次の Session から有効になります」と伝える。いまの Session の取得は、元の設定のままになる。

Plugin を更新して置き直すと、`mcp.json` は配布時の `off` に戻る。Cloud を使い続けたい人には、更新のあとにもう一度切り替えるよう伝える。

## API キーを持っている人

Cloud を使うことにした人に、Firecrawl のアカウントや API キーを持っているか聞く。キーが無くても Cloud での取り直しは使える（回数に制限がある）。

持っている人には、Agent が動いている端末のターミナルで次を実行してもらう。

```sh
firecrawl-local login
```

一度ログインすれば、以後の Session で自動的に使われる。API キーを会話に貼らないよう、必ず添える。貼られた場合は使わず、そのキーを Firecrawl 側で無効にして作り直すよう勧める。

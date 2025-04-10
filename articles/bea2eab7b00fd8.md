---
title: "Open WebUI で MCP してみた"
emoji: "💬"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["llm", "ollama", "openwebui", "mcpo", "MCP"]
published: true
---

## 環境

- MacOS 15.3.1
- Ollama version is 0.6.5
    - Ollama は、MacOS 上にインストール（非 Docker）
- Docker version 27.5.1
- Open WebUI v0.6.2
    - Docker で起動

## Open WebUI

すごく便利で Ollama と一緒に使って遊んでいる。

ただ、公式には MCP に対応していなかった。

久々に情報を漁っていたら、 [🛰️ MCP Support | Open WebUI](https://docs.openwebui.com/openapi-servers/mcp) がヒット。😯

mcpo だと？

## mcpo を使用した MCP Server の実行

シンプルなコマンドでいける [mcp-server-fetch](https://github.com/modelcontextprotocol/servers/tree/main/src/fetch) をお試しに

```bash
uvx mcpo --port 8000 -- uvx mcp-server-fetch
```

ポートは後ほど設定で使う。

## Open WebUI の設定

画面右上のユーザーアイコン？をクリックして "設定" を選択する。

![設定](/images/bea2eab7b00fd8/image.png =240x)

設定メニューの "ツール" を選択する。

Manage Tool Servers の "＋" を選択する。

![ツール](/images/bea2eab7b00fd8/image-1.png)

URL に "http://localhost:8000" を入力する。

更新アイコンを押すと mcpo との接続確認ができる。

![接続追加](/images/bea2eab7b00fd8/image-2.png)

プロンプト入力欄の下にスパナ 🔧 アイコンが現れ、接続しているツールの数が表示される。

![ホーム](/images/bea2eab7b00fd8/image-3.png)

## 使ってみる

```plain
https://react.dev/ を取得して内容を解説してください。
```

![動作チェック](/images/bea2eab7b00fd8/image-4.png)

実際のページと見比べてみるとちゃんと解説されていそう。

![ソース](/images/bea2eab7b00fd8/image-5.png)

`tool_fetch_post` をクリックすると取得したソースが表示される。

動いたよ！

## mcpo とは

"MCP to OpenAPI プロキシサーバー" とのこと。

mcpo サーバーで MCP Server をラップしている感じ？

mcpo を使用して MCP Server を起動すると、mcpo が MCP Server が用意しているツールの REST API 風なエンドポイントを公開する。

[http://localhost:8000/docs ](http://localhost:8000/docs) で、OpenAPI の仕様を確認できる。（ポート番号は mcpo で MCP Server を起動した時に指定してポート番号）

![oas](/images/bea2eab7b00fd8/image-6.png)

JSON-RPC をラップして、REST API 風なレイヤーを挟む意味はなんだろう？

> MCP サーバーは通常、次のような生の stdio 通信に依存します。
> - 🔓 環境間で本質的に安全ではない
> - ❌ 最新のツール、UI、プラットフォームのほとんどと互換性がない
> - 🧩 認証、ドキュメント、エラー処理などの重要な機能が不足している
> 
> mcpo プロキシはこれらの問題を自動的に排除します。
> - ✅ 既存の OpenAPI ツール、SDK、クライアントと即座に互換性があります
> - 🛡 安全でスケーラブルな標準ベースの HTTP エンドポイントでツールをラップします
> - 🧠 すべてのツールのインタラクティブな OpenAPI ドキュメントを自動生成します。設定は不要です。
> - 🔌 プレーンなHTTPを使用—ソケットのセットアップ、デーモンのジャグリング、プラットフォーム固有のグルーコードは不要
> 
> そのため、mcpo を追加すると、最初は「単なるもう 1 つのレイヤー」のように思えるかもしれませんが、実際にはすべてが簡素化され、次のメリットが得られます。
> - より良い統合✅
> - セキュリティ強化✅
> - スケーラビリティの向上✅
> - 開発者とユーザーの満足度向上✅
> ✨ mcpo を使用すると、ツール サーバー コードを 1 行も変更せずに、ローカル専用の AI ツールをクラウド対応、UI フレンドリー、そして即座に相互運用可能にすることができます。

まぁ、使い込んでいくことで実感できてくるかも。

## fetch で取得した日本語を LLM が解釈していない？

### 不具合発覚

```plain
https://ja.react.dev/ を取得して内容を解説してください。
```

なんかね、内容が怪しい。
React 自体は学習されているだろうから、説明はしてくれている。
指定したサイトの内容か？ 🤔
  
tool_fetch_post を見てみる。

:::details tool_fetch_post
``
ソース
tool_fetch_post
コンテンツ
[
"Contents of https://ja.react.dev/:\n![logo by @sawaratsuki1004](/_next/image?url=%2Fimages%2Fuwu.png&w=640&q=75 \"logo by @sawaratsuki1004\")\n\nWeb \u3068\u30cd\u30a4\u30c6\u30a3\u30d6\u30e6\u30fc\u30b6\u30a4\u30f3\u30bf\u30fc\u30d5\u30a7\u30fc\u30b9\u306e\u305f\u3081\u306e\u30e9\u30a4\u30d6\u30e9\u30ea\n\n## \u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u304b\u3089 \u30e6\u30fc\u30b6\u30a4\u30f3\u30bf\u30fc\u30d5\u30a7\u30fc\u30b9\u3092\u4f5c\u6210\n\nReact \u3067\u306f\u30e6\u30fc\u30b6\u30a4\u30f3\u30bf\u30fc\u30d5\u30a7\u30fc\u30b9\u3092\u3001\u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u3068\u547c\u3070\u308c\u308b\u90e8\u54c1\u3092\u4f7f\u3063\u3066\u69cb\u7bc9\u3067\u304d\u307e\u3059\u3002`Thumbnail`\u3001`LikeButton`\u3001`Video`\u3068\u3044\u3063\u305f React \u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u3092\u66f8\u304d\u3001\u305d\u308c\u3089\u3092\u7d44\u307f\u5408\u308f\u305b\u3066\u753b\u9762\u3084\u30da\u30fc\u30b8\u3084\u30a2\u30d7\u30ea\u306e\u5168\u4f53\u3092\u7d44\u307f\u7acb\u3066\u307e\u3057\u3087\u3046\u3002\n\n\u72ec\u308a\u3067\u958b\u767a\u3057\u3066\u3044\u3066\u3082\u3001\u6570\u5343\u306e\u958b\u767a\u8005\u3068\u5171\u540c\u958b\u767a\u3057\u3066\u3044\u3066\u3082\u3001React \u306e\u958b\u767a\u4f53\u9a13\u306f\u540c\u3058\u3067\u3059\u3002\u500b\u4eba\u3001\u30c1\u30fc\u30e0\u3001\u5927\u898f\u6a21\u306a\u7d44\u7e54\u306b\u3088\u3063\u3066\u66f8\u304b\u308c\u3055\u307e\u3056\u307e\u306a\u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u3092\u3001\u30b7\u30fc\u30e0\u30ec\u30b9\u306b\u7d44\u307f\u5408\u308f\u305b\u306a\u304c\u3089\u958b\u767a\u3067\u304d\u308b\u3002\u305d\u308c\u304c React \u306e\u8a2d\u8a08\u7406\u5ff5\u3067\u3059\u3002\n\n## \u30de\u30fc\u30af\u30a2\u30c3\u30d7\u3068\u30b3\u30fc\u30c9\u304b\u3089 \u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u3092\u4f5c\u6210\n\nReact \u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u306f\u5358\u306a\u308b JavaScript \u306e\u95a2\u6570\u3067\u3059\u3002\u6761\u4ef6\u306b\u3088\u3063\u3066\u30b3\u30f3\u30c6\u30f3\u30c4\u306e\u8868\u793a\u3092\u5909\u3048\u305f\u3051\u308c\u3070 `if` \u6587\u3092\u4f7f\u3044\u307e\u3057\u3087\u3046\uff01 \u30ea\u30b9\u30c8\u3092\u8868\u793a\u3057\u305f\u3044\u306a\u3089\u914d\u5217\u306e `map()` \u3092\u4f7f\u3044\u307e\u3057\u3087\u3046\uff01 React \u3092\u5b66\u3076\u3068\u3044\u3046\u3053\u3068\u306f\u3001\u30d7\u30ed\u30b0\u30e9\u30df\u30f3\u30b0\u3092\u5b66\u3076\u3068\u3044\u3046\u3053\u3068\u306a\u306e\u3067\u3059\u3002\n\n\u3053\u306e\u30de\u30fc\u30af\u30a2\u30c3\u30d7\u69cb\u6587\u306f JSX \u3068\u547c\u3070\u308c\u307e\u3059\u3002React \u304c\u666e\u53ca\u3055\u305b\u305f JavaScript \u306e\u69cb\u6587\u62e1\u5f35\u3067\u3059\u3002JSX \u30de\u30fc\u30af\u30a2\u30c3\u30d7\u306f\u95a2\u9023\u3059\u308b\u30ec\u30f3\u30c0\u30ea\u30f3\u30b0\u30ed\u30b8\u30c3\u30af\u306e\u3059\u3050\u305d\u3070\u306b\u914d\u7f6e\u3067\u304d\u308b\u306e\u3067\u3001React \u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u306f\u7c21\u5358\u306b\u4f5c\u6210\u3001\u4fdd\u5b88\u3001\u524a\u9664\u304c\u3067\u304d\u307e\u3059\u3002\n\n## \u30a4\u30f3\u30bf\u30e9\u30af\u30c6\u30a3\u30d6\u6a5f\u80fd\u3092 \u3069\u3053\u3067\u3082\u5fc5\u8981\u306a\u5834\u6240\u306b\n\nReact \u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u306f\u30c7\u30fc\u30bf\u3092\u53d7\u3051\u53d6\u308a\u3001\u753b\u9762\u306b\u8868\u793a\u3059\u308b\u3082\u306e\u3092\u8fd4\u3057\u307e\u3059\u3002\u5165\u529b\u30d5\u30a3\u30fc\u30eb\u30c9\u3078\u306e\u30bf\u30a4\u30d4\u30f3\u30b0\u306a\u3069\u306e\u30e6\u30fc\u30b6\u64cd\u4f5c\u306b\u3088\u3063\u3066\u65b0\u3057\u3044\u30c7\u30fc\u30bf\u304c\u3067\u304d\u305f\u3089\u3001\u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u306b\u305d\u308c\u3092\u6e21\u3057\u307e\u3059\u3002React \u304c\u65b0\u3057\u3044\u30c7\u30fc\u30bf\u306b\u57fa\u3065\u3044\u3066\u753b\u9762\u3092\u66f4\u65b0\u3057\u307e\u3059\u3002\n\n\u30da\u30fc\u30b8\u5168\u4f53\u3092 React \u3067\u69cb\u7bc9\u3059\u308b\u5fc5\u8981\u306f\u3042\u308a\u307e\u305b\u3093\u3002\u65e2\u5b58\u306e HTML \u30da\u30fc\u30b8\u306b React \u3092\u8ffd\u52a0\u3059\u308c\u3070\u3001\u3069\u3093\u306a\u5834\u6240\u306b\u3067\u3082\u30a4\u30f3\u30bf\u30e9\u30af\u30c6\u30a3\u30d6\u306a React \u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u3092\u8868\u793a\u3067\u304d\u307e\u3059\u3002\n\n[\u65e2\u5b58\u306e\u30da\u30fc\u30b8\u306b React \u3092\u8ffd\u52a0\u3059\u308b](/learn/add-react-to-an-existing-project)\n\n## \u30d5\u30ec\u30fc\u30e0\u30ef\u30fc\u30af\u3067 \u30d5\u30eb\u30b9\u30bf\u30c3\u30af\u306a\u958b\u767a\u3092\n\nReact \u306f\u30e9\u30a4\u30d6\u30e9\u30ea\u3067\u3059\u3002\u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u3092\u7d44\u307f\u5408\u308f\u305b\u308b\u3053\u3068\u306f\u3067\u304d\u307e\u3059\u304c\u3001\u30eb\u30fc\u30c6\u30a3\u30f3\u30b0\u3084\u30c7\u30fc\u30bf\u30d5\u30a7\u30c3\u30c1\u306e\u65b9\u6cd5\u307e\u3067\u306f\u6307\u5b9a\u3057\u307e\u305b\u3093\u3002React \u3067\u30a2\u30d7\u30ea\u5168\u4f53\u3092\u69cb\u7bc9\u3059\u308b\u5834\u5408\u306f\u3001[Next.js](https://nextjs.org/) \u3084 [Remix](https://remix.run/) \u306e\u3088\u3046\u306a\u30d5\u30eb\u30b9\u30bf\u30c3\u30af\u306e\u30d5\u30ec\u30fc\u30e0\u30ef\u30fc\u30af\u3092\u304a\u52e7\u3081\u3057\u307e\u3059\u3002\n\nReact \u3068\u306f\u30a2\u30fc\u30ad\u30c6\u30af\u30c1\u30e3\u3067\u3082\u3042\u308a\u307e\u3059\u3002\u30d5\u30ec\u30fc\u30e0\u30ef\u30fc\u30af\u3067\u306f\u3001\u30b5\u30fc\u30d0\u3084\u30d3\u30eb\u30c9\u6642\u306b\u52d5\u4f5c\u3059\u308b\u975e\u540c\u671f\u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u3092\u4f7f\u3063\u3066\u30c7\u30fc\u30bf\u306e\u53d6\u5f97\u304c\u53ef\u80fd\u3067\u3059\u3002\u30d5\u30a1\u30a4\u30eb\u3084\u30c7\u30fc\u30bf\u30d9\u30fc\u30b9\u304b\u3089\u30c7\u30fc\u30bf\u3092\u8aad\u307f\u8fbc\u3093\u3067\u3001\u30a4\u30f3\u30bf\u30e9\u30af\u30c6\u30a3\u30d6\u306a\u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u306b\u6e21\u3057\u307e\u3057\u3087\u3046\u3002\n\n[\u30d5\u30ec\u30fc\u30e0\u30ef\u30fc\u30af\u3067\u59cb\u3081\u308b](/learn/start-a-new-react-project)\n\n## \u3042\u3089\u3086\u308b\u30d7\u30e9\u30c3\u30c8\u30d5\u30a9\u30fc\u30e0\u306e \u80fd\u529b\u3092\u6700\u5927\u9650\u306b\u6d3b\u7528\n\n\u4eba\u3005\u306f\u30a6\u30a7\u30d6\u3092\u611b\u3057\u3001\u305d\u3057\u3066\u30cd\u30a4\u30c6\u30a3\u30d6\u30a2\u30d7\u30ea\u3092\u611b\u3057\u3066\u3044\u307e\u3059\u3002\u305d\u306e\u7406\u7531\u306f\u69d8\u3005\u3067\u3059\u3002React \u3092\u4f7f\u3048\u3070\u3001\u540c\u3058\u30b9\u30ad\u30eb\u3092\u4f7f\u3063\u3066\u30a6\u30a7\u30d6\u30a2\u30d7\u30ea\u3068\u30cd\u30a4\u30c6\u30a3\u30d6\u30a2\u30d7\u30ea\u306e\u4e21\u65b9\u3092\u69cb\u7bc9\u3067\u304d\u307e\u3059\u3002\u5404\u30d7\u30e9\u30c3\u30c8\u30d5\u30a9\u30fc\u30e0\u304c\u6301\u3064\u72ec\u81ea\u306e\u5f37\u307f\u3092\u6d3b\u304b\u3057\u3001\u3069\u3093\u306a\u30d7\u30e9\u30c3\u30c8\u30d5\u30a9\u30fc\u30e0\u306b\u304a\u3044\u3066\u3082\u81ea\u7136\u306a\u30a4\u30f3\u30bf\u30fc\u30d5\u30a7\u30fc\u30b9\u3092\u5b9f\u73fe\u3057\u307e\u3059\u3002\n\nReact \u3092\u4f7f\u3048\u3070\u3001\u30a6\u30a7\u30d6\u958b\u767a\u8005\u306b\u3082\u30cd\u30a4\u30c6\u30a3\u30d6\u30a2\u30d7\u30ea\u958b\u767a\u8005\u306b\u3082\u306a\u308c\u308b\u306e\u3067\u3059\u3002\u30e6\u30fc\u30b6\u30fc\u4f53\u9a13\u3092\u72a0\u7272\u306b\u3059\u308b\u3053\u3068\u306a\u304f\u3001\u591a\u304f\u306e\u30d7\u30e9\u30c3\u30c8\u30d5\u30a9\u30fc\u30e0\u3067\u30ea\u30ea\u30fc\u30b9\u3092\u884c\u3048\u307e\u3059\u3002\u3072\u3068\u3064\u306e\u30d7\u30e9\u30c3\u30c8\u30d5\u30a9\u30fc\u30e0\u306b\u7e1b\u3089\u308c\u308b\u3053\u3068\u306a\u304f\u3001\u3059\u3079\u3066\u306e\u6a5f\u80fd\u3092\u30a8\u30f3\u30c9\u30c4\u30fc\u30a8\u30f3\u30c9\u3067\u62c5\u5f53\u3059\u308b\u30c1\u30fc\u30e0\u3092\u4f5c\u308c\u307e\u3059\u3002\n\n[\u30cd\u30a4\u30c6\u30a3\u30d6\u30d7\u30e9\u30c3\u30c8\u30d5\u30a9\u30fc\u30e0\u5411\u3051\u306b\u958b\u767a\u3059\u308b](https://reactnative.dev/)\n\n## \u5b8c\u6210\u3057\u305f\u6a5f\u80fd\u3060\u3051\u304c \u30ea\u30ea\u30fc\u30b9\u3055\u308c\u308b\n\nReact \u306f\u958b\u767a\u30a2\u30d7\u30ed\u30fc\u30c1\u306e\u5909\u66f4\u306b\u614e\u91cd\u306b\u53d6\u308a\u7d44\u307f\u307e\u3059\u3002\u3059\u3079\u3066\u306e\u30b3\u30df\u30c3\u30c8\u306f 10 \u5104\u4eba\u4ee5\u4e0a\u306e\u30e6\u30fc\u30b6\u306b\u3088\u308b\u30d3\u30b8\u30cd\u30b9\u30af\u30ea\u30c6\u30a3\u30ab\u30eb\u306a\u74b0\u5883\u306b\u304a\u3044\u3066\u30c6\u30b9\u30c8\u3055\u308c\u307e\u3059\u3002Meta \u306b\u3042\u308b 10 \u4e07\u4ee5\u4e0a\u306e React \u30b3\u30f3\u30dd\u30fc\u30cd\u30f3\u30c8\u304c\u3001\u3059\u3079\u3066\u306e\u79fb\u884c\u6226\u7565\u306e\u691c\u8a3c\u3092\u652f\u63f4\u3057\u307e\u3059\u3002\n\nReact \u30c1\u30fc\u30e0\u306f\u3001\u5e38\u306b React \u3092\u6539\u5584\u3059\u308b\u65b9\u6cd5\u3092\u6a21\u7d22\u3057\u3066\u3044\u307e\u3059\u304c\u3001\u7814\u7a76\u306b\u3088\u3063\u3066\u306f\u6210\u679c\u304c\u51fa\u308b\u307e\u3067\u306b\u4f55\u5e74\u3082\u304b\u304b\u308b\u3053\u3068\u3082\u3042\u308a\u307e\u3059\u3002\u7814\u7a76\u306e\u30a2\u30a4\u30c7\u30a2\u3092\u30ea\u30ea\u30fc\u30b9\u3059\u308b\u307e\u3067\u306e\u9ad8\u3044\u30cf\u30fc\u30c9\u30eb\u3092\u8d8a\u3048\u305f\u3001\u5b9f\u8a3c\u6e08\u307f\u306e\u30a2\u30d7\u30ed\u30fc\u30c1\u3060\u3051\u304c React \u306e\u4e00\u90e8\u3068\u306a\u308b\u306e\u3067\u3059\u3002\n\n[React \u306e\u30cb\u30e5\u30fc\u30b9\u3092\u8aad\u3080](/blog)\n\n## \u6570\u767e\u4e07\u4eba\u306e \u30b3\u30df\u30e5\u30cb\u30c6\u30a3\u306b\u53c2\u52a0\u3057\u3088\u3046\n\n\u3042\u306a\u305f\u306f 1 \u4eba\u3067\u306f\u3042\u308a\u307e\u305b\u3093\u3002\u4e16\u754c\u4e2d\u304b\u3089\u6bce\u6708 200 \u4e07\u4eba\u306e\u958b\u767a\u8005\u304c React \u30c9\u30ad\u30e5\u30e1\u30f3\u30c8\u306b\u8a2a\u308c\u3066\u3044\u307e\u3059\u3002\u4eba\u3005\u3068\u30c1\u30fc\u30e0\u304c\u5171\u611f\u3067\u304d\u308b\u6280\u8853\u3001\u305d\u308c\u304c React \u306a\u306e\u3067\u3059\u3002\n\nReact \u306f\u5358\u306a\u308b\u30e9\u30a4\u30d6\u30e9\u30ea\u3084\u30a2\u30fc\u30ad\u30c6\u30af\u30c1\u30e3\u3001\u3042\u308b\u3044\u306f\u30a8\u30b3\u30b7\u30b9\u30c6\u30e0\u3068\u3044\u3046\u4ee5\u4e0a\u306e\u5b58\u5728\u3067\u3059\u3002React \u3068\u306f\u30b3\u30df\u30e5\u30cb\u30c6\u30a3\u3067\u3059\u3002\u30d8\u30eb\u30d7\u3092\u6c42\u3081\u3001\u30c1\u30e3\u30f3\u30b9\u3092\u898b\u3064\u3051\u3001\u65b0\u3057\u3044\u53cb\u4eba\u306b\u4f1a\u3048\u308b\u5834\u6240\u3067\u3059\u3002\u958b\u767a\u8005\u3084\u30c7\u30b6\u30a4\u30ca\u3001\u521d\u5fc3\u8005\u3084\u30a8\u30ad\u30b9\u30d1\u30fc\u30c8\u3001\u7814\u7a76\u8005\u3084\u30a2\u30fc\u30c6\u30a3\u30b9\u30c8\u3001\u6559\u5e2b\u3084\u5b66\u751f\u3068\u51fa\u4f1a\u3048\u308b\u5834\u6240\u3067\u3059\u3002\u79c1\u305f\u3061\u306e\u30d0\u30c3\u30af\u30b0\u30e9\u30a6\u30f3\u30c9\u306f\u3055\u307e\u3056\u307e\u3067\u3059\u304c\u3001React \u3092\u901a\u3058\u3066\u7686\u3067\u30e6\u30fc\u30b6\u30fc\u30a4\u30f3\u30bf\u30fc\u30d5\u30a7\u30fc\u30b9\u306e\u5275\u9020\u306b\u53d6\u308a\u7d44\u3093\u3067\u3044\u308b\u306e\u3067\u3059\u3002\n\n![logo by @sawaratsuki1004](/images/uwu.png \"logo by @sawaratsuki1004\")\n\n## React \u30b3\u30df\u30e5\u30cb\u30c6\u30a3\u306b \u3088\u3046\u3053\u305d\uff01\n\n[\u306f\u3058\u3081\u308b](/learn)"
]
``
:::

⁉️
  
こ、これは。Unicode エスケープシーケンス。
クラウドサービスの大規模言語モデルならいざしらず、ローカルの LLM では、解釈できていないっぽい。

困った。

### 改修

```bash
git clone https://github.com/open-webui/open-webui.git
```

`backend/open_webui/utils/middleware.py` 233 行目（Open WebUI v0.6.2）

```diff
                 if isinstance(tool_result, dict) or isinstance(tool_result, list):
-                    tool_result = json.dumps(tool_result, indent=2)
+                    tool_result = json.dumps(tool_result, indent=2, ensure_ascii=False)
```

Docker のイメージ作成と実行

```bash
# Build
docker build -t open-webui:multibyte .

# Run
docker run -it -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always open-webui:open-webui:multibyte
```

日本語として読める！よしよし。

:::details tool_fetch_post
``
ソース
tool_fetch_post
コンテンツ
[
"Contents of https://ja.react.dev/:\n![logo by @sawaratsuki1004](/_next/image?url=%2Fimages%2Fuwu.png&w=640&q=75 \"logo by @sawaratsuki1004\")\n\nWeb とネイティブユーザインターフェースのためのライブラリ\n\n## コンポーネントから ユーザインターフェースを作成\n\nReact ではユーザインターフェースを、コンポーネントと呼ばれる部品を使って構築できます。`Thumbnail`、`LikeButton`、`Video`といった React コンポーネントを書き、それらを組み合わせて画面やページやアプリの全体を組み立てましょう。\n\n独りで開発していても、数千の開発者と共同開発していても、React の開発体験は同じです。個人、チーム、大規模な組織によって書かれさまざまなコンポーネントを、シームレスに組み合わせながら開発できる。それが React の設計理念です。\n\n## マークアップとコードから コンポーネントを作成\n\nReact コンポーネントは単なる JavaScript の関数です。条件によってコンテンツの表示を変えたければ `if` 文を使いましょう！ リストを表示したいなら配列の `map()` を使いましょう！ React を学ぶということは、プログラミングを学ぶということなのです。\n\nこのマークアップ構文は JSX と呼ばれます。React が普及させた JavaScript の構文拡張です。JSX マークアップは関連するレンダリングロジックのすぐそばに配置できるので、React コンポーネントは簡単に作成、保守、削除ができます。\n\n## インタラクティブ機能を どこでも必要な場所に\n\nReact コンポーネントはデータを受け取り、画面に表示するものを返します。入力フィールドへのタイピングなどのユーザ操作によって新しいデータができたら、コンポーネントにそれを渡します。React が新しいデータに基づいて画面を更新します。\n\nページ全体を React で構築する必要はありません。既存の HTML ページに React を追加すれば、どんな場所にでもインタラクティブな React コンポーネントを表示できます。\n\n[既存のページに React を追加する](/learn/add-react-to-an-existing-project)\n\n## フレームワークで フルスタックな開発を\n\nReact はライブラリです。コンポーネントを組み合わせることはできますが、ルーティングやデータフェッチの方法までは指定しません。React でアプリ全体を構築する場合は、[Next.js](https://nextjs.org/) や [Remix](https://remix.run/) のようなフルスタックのフレームワークをお勧めします。\n\nReact とはアーキテクチャでもあります。フレームワークでは、サーバやビルド時に動作する非同期コンポーネントを使ってデータの取得が可能です。ファイルやデータベースからデータを読み込んで、インタラクティブなコンポーネントに渡しましょう。\n\n[フレームワークで始める](/learn/start-a-new-react-project)\n\n## あらゆるプラットフォームの 能力を最大限に活用\n\n人々はウェブを愛し、そしてネイティブアプリを愛しています。その理由は様々です。React を使えば、同じスキルを使ってウェブアプリとネイティブアプリの両方を構築できます。各プラットフォームが持つ独自の強みを活かし、どんなプラットフォームにおいても自然なインターフェースを実現します。\n\nReact を使えば、ウェブ開発者にもネイティブアプリ開発者にもなれるのです。ユーザー体験を犠牲にすることなく、多くのプラットフォームでリリースを行えます。ひとつのプラットフォームに縛られることなく、すべての機能をエンドツーエンドで担当するチームを作れます。\n\n[ネイティブプラットフォーム向けに開発する](https://reactnative.dev/)\n\n## 完成した機能だけが リリースされる\n\nReact は開発アプローチの変更に慎重に取り組みます。すべてのコミットは 10 億人以上のユーザによるビジネスクリティカルな環境においてテストされます。Meta にある 10 万以上の React コンポーネントが、すべての移行戦略の検証を支援します。\n\nReact チームは、常に React を改善する方法を模索していますが、研究によっては成果が出るまでに何年もかかることもあります。研究のアイデアをリリースするまでの高いハードルを越えた、実証済みのアプローチだけが React の一部となるのです。\n\n[React のニュースを読む](/blog)\n\n## 数百万人の コミュニティに参加しよう\n\nあなたは 1 人ではありません。世界中から毎月 200 万人の開発者が React ドキュメントに訪れています。人々とチームが共感できる技術、それが React なのです。\n\nReact は単なるライブラリやアーキテクチャ、あるいはエコシステムという以上の存在です。React とはコミュニティです。ヘルプを求め、チャンスを見つけ、新しい友人に会える場所です。開発者やデザイナ、初心者やエキスパート、研究者やアーティスト、教師や学生と出会える場所です。私たちのバックグラウンドはさまざまですが、React を通じて皆でユーザーインターフェースの創造に取り組んでいるのです。\n\n![logo by @sawaratsuki1004](/images/uwu.png \"logo by @sawaratsuki1004\")\n\n## React コミュニティに ようこそ！\n\n[はじめる](/learn)"
]
``
:::

解説も英語サイトの時より簡素になっているが、ちゃんと読み込んでいそう。

エスケープさせないようにソースコード改変。あとでプルリク出してみようかな。

## MCP Server をいくつか試す

### fetch

すでに試したものだけど、いくつかの実行方法をメモ。

上記で既に試したもの

```bash
uvx mcpo --port 8000 -- uvx mcp-server-fetch
```

バックグラウンドでの実行

```bash
nohup uvx mcpo --port 8000 -- uvx mcp-server-fetch &
```

Docker での実行

```bash
docker run -d -p 8000:8000 ghcr.io/open-webui/mcpo:main -- uvx mcp-server-fetch
```

### filesystem

```bash
uvx mcpo --port 8001 -- /Users/hoge/.nvm/versions/node/v22.14.0/bin/node /Users/hoge/.nvm/versions/node/v22.14.0/lib/node_modules/@modelcontextprotocol/server-filesystem/dist/index.js /Users/hoge/Documents
```

### Brave Search

わたしの環境ではフルパスでないと動かない。

nvm 使っているからか。

.env ファイルを作成して、node の --env-file オプションで読み込む。.env ファイルのパスに注意。

```bash
# .env 作成
echo "BRAVE_API_KEY=XXXXXXXXXXX_XXXXXXXX_XXXXXXXXXX" >> .env

# .env ファイルのパスを指定して実行
uvx mcpo --port 8002 -- /Users/hoge/.nvm/versions/node/v22.14.0/bin/node --env-file=/Users/hoge/brave-search-api-key/.env /Users/hoge/.nvm/versions/node/v22.14.0/lib/node_modules/@modelcontextprotocol/server-brave-search/dist/index.js
```

### Obsidian MCP Tools

動かしたかったけど、動いてくれず。情報求ム

```bash
uvx mcpo --port 8003 --env OBSIDIAN_API_KEY=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx -- /Users/hoge/Documents/Obsidian\ Vault/.obsidian/plugins/mcp-tools/bin/mcp-server
```

エラー出る。Python 詳しくないけど、バグってる？あとで見てみよう。そう、windsurf でね。

```plain
│    77 │   env_dict = {}                                │
│    78 │   if env:                                      │
│    79 │   │   for var in env:                          │
│ ❱  80 │   │   │   key, value = env.split("=", 1)       │
│    81 │   │   │   env_dict[key] = value     
```

### Playwright

ブラウザ開いて動くけど、、

```bash
uvx mcpo --port 8004 -- npx @playwright/mcp@latest
```

### Cloud Desktop の claude_desktop_config.json を使う

お試しなのでコピーしてから

```bash
# コピー
cp "/Users/hoge/Library/Application Support/Claude/claude_desktop_config.json" ~/Downloads/config.json

# 実行
# 上記で作成したファイルをフルパスで指定（わたしの環境ではフルパスじゃないと起動しない）
uvx mcpo --port 8005 -- mcpo --config /Users/hoge/Downloads/config.json
```

接続もできるし、それぞれ認識もしてそうだけど、プロンプトで工夫してみてもツールが呼び出されることはなかった。

---
title: "claude-code-router と Ollama で遊んでみた。辛いだけだった。"
emoji: "🦙"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["claudecode", "claudecoderouter", "ollama", "qwen3", "qwen25coder"]
published: true
---

## claude-code-router

claude code を claude 以外の LLM で動かせる、という楽しげな響きに誘われた。
https://github.com/musistudio/claude-code-router

## 検証環境

- MacOS 15.3.1
- Ollama version is 0.9.6
- claude code 1.0.51
- claude-code-router version: 1.0.18

## 検証結果

もっと気軽に動かして、わーい！で終わるつもりだった。だが、しかし、動かない、動かない。
コンテキストレングス長くしたら動いた。

- モデル
    - [qwen3:14b](https://ollama.com/library/qwen3:14b)
    - [qwen2.5-coder:14b](https://ollama.com/library/qwen2.5-coder:14b)
- コンテキストレングス
    - 32768（`PARAMETER num_ctx 32768`）

いくつものモデルでごちゃごちゃ試したが、上記の組み合わせで動いて、力尽きた。

## 検証に使ったプロンプト

```plain
Create a simple task management app with only index.html
```

## 所感

今回動かした内容だと、メモリが 30GB くらい持っていかれる。まぁ、個人的にはなかなか厳しい結果。
試行錯誤して、四苦八苦して、楽しかった。

以上

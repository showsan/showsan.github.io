---
title: "VSCodeでMarkdownを扱うときの備忘録"
date: 2022-10-23
categories: ["Tips"]
tags: ["Tips","Markdown","VSCode"]
menu: main
---

## 忘れがちな設定

### VSCodeの設定

#### スニペットの設定

マークダウンのスニペットはデフォルトでオフなので設定を加える必要がある。

```json:setting.json
{
  "[markdown]": {
  "editor.quickSuggestions": {
    "other": true,
    "comments": false,
    "strings": true
  },
}
```

#### files.trimTrailingWhitespace

ファイルの行の最後にスペースがあると自動で取り除いてくれる機能  
すごい便利だが、Markdownで記事を書いているときとかに自動でトリミングされてしまうと改行が全部なくなるので非常に困る。  
なのでMarkdownをいじるときだけオフにするのが良い  

```json:setting.json
{
  "[markdown]": {
        "files.trimTrailingWhitespace": false,
  }
}

```

### Markdown Preview Enhancedの設定

#### markdown-preview-enhanced.breakOnSingleNewLine

プレビューするときに半角なしでも改行できているように見せるよーという機能  
Markdownで記事を書いたりするときにプラットフォームによっては半角スペース二つなくても改行できてしまうが、そうでないサイトもあるのでオフにすること推奨  

```json:setting.json
{
  "markdown-preview-enhanced.breakOnSingleNewLine": false
}
```

### PasteImage

#### SSH先でやるには一工夫いるかも

これはMarkdownっていうよりかは全般的な話かも  
私は結構自分の開発サーバーになんでもGitCloneしてDocker上でいろいろ動かしたりしながら開発とかする人間なのですが、このブログもその要領でやろうとしてました。  
しかしSSH先のサーバーでPasteImageを使おうとするとうまくいかなかったです。  
多分WindowsからLinuxにSSHしているからーとかいろいろあると思いますが、いったんWindowsでプッシュも全部やることで解決しています。

## まとめ

備忘録的に書きました。
追記あれば日付入りでテキトーに追記しようと思います。  

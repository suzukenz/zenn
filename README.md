# Zenn CLI

* [📘 How to use](https://zenn.dev/zenn/articles/zenn-cli-guide)

## 基本的な使い方

### 記事（article）の管理

#### 記事の作成

```bash
# 新しい記事を作成する
npx zenn new:article

# オプションを指定して作成する
npx zenn new:article --slug 記事のスラッグ --title タイトル --type idea --emoji ✨
```

作成されるファイルは `articles/ランダムなslug.md` となり、次のような構造になっています:

```markdown
---
title: "" # 記事のタイトル
emoji: "😸" # アイキャッチとして使われる絵文字（1文字だけ）
type: "tech" # tech: 技術記事 / idea: アイデア記事
topics: [] # タグ。["markdown", "rust", "aws"]のように指定する
published: true # 公開設定（falseにすると下書き）
---
ここから本文を書く
```

#### プレビュー

```bash
# ブラウザでプレビューを開始
npx zenn preview
```

#### 記事の公開

記事を zenn.dev 上で公開するには `published` オプションを `true` にしてからファイルをコミットし、連携済みのGitHubリポジトリにプッシュします。

### 本（book）の管理

#### 本の構成

```
books
└── 本のスラッグ
    ├── config.yaml # 本の設定
    ├── cover.png　# カバー画像（推奨: 幅500px・高さ700px）
    └── チャプターのスラッグ.md # 各チャプター
```

#### 本の作成

```bash
# 新しい本の雛形を作成する
npx zenn new:book

# スラッグを指定して作成する
npx zenn new:book --slug 本のスラッグ
```

#### 本の設定（config.yaml）

```yaml
title: "本のタイトル"
summary: "本の紹介文"
topics: ["markdown", "zenn", "react"] # トピック（5つまで）
published: true # falseだと下書き
price: 0 # 有料の場合200〜5000
chapters:
  - example1 # チャプター1
  - example2 # チャプター2
```

#### チャプターの作成

各チャプターのmarkdownファイルは次のように作成します:

```markdown
---
title: "チャプターのタイトル"
---
ここからチャプター本文
```

#### 本のプレビュー

```bash
# ブラウザでプレビューを開始
npx zenn preview
```

# aios-workspace

経営者がClaude Codeを自分の事業向けに使い始め、AIオペレーティングシステムを構築するためのプラグイン。事業の情報を整理し、日々の計画・実行・振り返りまでをサポートします。

---

## このプラグインで何ができるか

| スキル | 用途 |
|---|---|
| `/aios-workspace:guide` | 使い方ガイド（初めての方はここから） |
| `/aios-workspace:setup` | 事業情報をClaudeに教え込む初回セットアップ |
| `/aios-workspace:plan` | タスクの計画書を作成 |
| `/aios-workspace:execute` | 計画書をClaudeと一緒に実行 |
| `/aios-workspace:brainstorm` | 迷っているテーマの選択肢とトレードオフを整理 |
| `/aios-workspace:update-context` | 事業情報を最新化（自動でサブフォルダ分割対応） |
| `/aios-workspace:audit-workspace` | ワークスペースの健康診断と改善提案 |

---

## インストール

Claude Codeのセッション内で以下を実行してください：

```
/plugin marketplace add DeePeriver/aios-workspace
/plugin install aios-workspace@aios-workspace
/reload-plugins
```

---

## 使い始め方

新しい空のフォルダを作って、その中でClaude Codeを起動。最初に：

```
/aios-workspace:guide
```

を実行すると、Claude Codeとは何か、このプラグインで何ができるか、最初の一歩は何かが分かります。Claude Codeを使ったことがない経営者向けに、ゼロから説明します。

その後、

```
/aios-workspace:setup
```

を実行すると、あなたの事業のこと（業種、顧客、戦略、財務、現在の優先事項など）をインタビュー形式で聞き出し、Claudeに覚えさせます。所要時間は30分程度。一度やれば、それ以降は毎セッション自動で読み込まれます。

---

## 仕組み

このプラグインは、ワークスペースに以下のディレクトリ構造を作ります：

```
your-workspace/
├── CLAUDE.md              # 毎セッション自動で読み込まれるメインの説明
├── context/               # あなたの事業情報（7次元）
│   ├── business-overview.md
│   ├── personal-profile.md
│   ├── strategy.md
│   ├── customers.md
│   ├── competitors.md
│   ├── financials.md
│   └── current-priorities.md
├── plans/                 # /plan で作った計画書
└── outputs/               # /execute の成果物
```

事業情報が増えてくると、`update-context` が自動で `context/<次元>/` サブフォルダに整理してくれます。長く使い続けても見通しが保てる設計です。

---

## ライセンス

[MIT License](./LICENSE)

---

## Author

Hachi ([@DeePeriver](https://github.com/DeePeriver))

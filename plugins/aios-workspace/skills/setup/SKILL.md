---
name: setup
description: 経営者のClaude Code / Cowork ワークスペースの初回ブートストラップ。プラグインに同梱された雛形（CLAUDE.md と context/ ディレクトリ）を作業ディレクトリに展開し、7つのコンテキスト次元（ビジネス概要、個人プロフィール、戦略、顧客、競合、財務、現在の優先事項）でユーザーにインタビューして context/ ファイルを記入する。空または空に近いディレクトリでビジネスワークスペースをゼロから立ち上げる時、または「ワークスペースをセットアップ」「オンボーディング」「始める」「初期化」と言及された時に使用する。
---

# Setup

経営者のワークスペースをゼロから完全にブートストラップする。Claude Code と Cowork の両方で動作する。

## 終了状態

このスキルが完了した時点で、ユーザーは以下を持つ：
- ビジネスに合わせてカスタマイズされたワークスペースルートの `CLAUDE.md`
- `context/`、`plans/`、`outputs/`、`reference/` ディレクトリ
- `context/` 内の7つの記入済みコンテキストファイル
- ワークスペースが使用準備完了であることの確認

## 手順

### 1. 実行環境を特定する

このスキルは Claude Code と Cowork の両方で動く。最初に環境とパスを確定させる。後続のステップはここで決めた変数名を使う。

**判定**：
- `${CLAUDE_PLUGIN_ROOT}` が値を持って解決される、かつ `/sessions/` が存在しない → **Claude Code 環境**
- `${CLAUDE_PLUGIN_ROOT}` が空、かつ `/sessions/<name>/mnt/` が存在する → **Cowork 環境**

判定が曖昧な場合は `ls /sessions/ 2>/dev/null` と `echo "${CLAUDE_PLUGIN_ROOT}"` を実行して確認する。

**変数の特定**：

| 変数 | Claude Code | Cowork |
|---|---|---|
| `<plugin-root>`（雛形の在処） | `${CLAUDE_PLUGIN_ROOT}` | `find /sessions/*/mnt/.remote-plugins -type d -name "aios-workspace*"` で発見 |
| `<workspace>`（bash 用の作業ディレクトリ） | カレントディレクトリ（pwd） | `/sessions/<name>/mnt/AI-Workspace/`（または connect されたフォルダ） |
| `<workspace-host>`（Read/Write/Edit ツール用） | `<workspace>` と同じ | `/Users/<user>/Documents/Claude/Projects/AI-Workspace/`（ホスト側パス） |

**重要**：Cowork では bash ツールは VM パス（`/sessions/...`）、Read/Write/Edit ツールはホスト側パス（`/Users/...`）を使う。両者を混同しない。Claude Code ではこの区別はない。

### 2. 開始状態を確認

`<workspace>` の中身を `ls` で確認する。
- `CLAUDE.md` または `context/` がすでに実コンテンツ（テンプレートのプレースホルダー `{{BUSINESS_NAME}}` などではなく、実際のビジネス情報）を含む場合、**上書き**、**マージ**、**中止** のどれにするかをユーザーに確認する。デフォルトは中止。
- ディレクトリが空、またはテンプレートスタブのみ（`{{BUSINESS_NAME}}` のままの CLAUDE.md、空の context/）の場合は次へ進む。

### 3. 既存の保護ディレクトリをリセット（Cowork のみ）

**Claude Code 環境ならこのステップをスキップ** してステップ 4 に進む。

Cowork では `<workspace>` 直下の事前存在ディレクトリ・ファイルは書き込み保護されている。雛形をコピーする前に、既存の `context/` ディレクトリ（空のスタブ）と `CLAUDE.md`（プレースホルダーのまま）を削除許可付きで作り直す必要がある。

実存するもののみ対処する：
1. `context/` が存在する場合：
   - `mcp__cowork__allow_cowork_file_delete` を `<workspace-host>/context` に対して呼ぶ
   - `rmdir <workspace>/context && mkdir <workspace>/context`
2. `CLAUDE.md` がスタブのまま存在する場合：
   - `mcp__cowork__allow_cowork_file_delete` を `<workspace-host>/CLAUDE.md` に対して呼ぶ
   - `rm <workspace>/CLAUDE.md`

書き込みが Permission denied で失敗したら、ユーザーに「不可能」と伝えるのではなく、まずこのリセット手順を試す。

### 4. 雛形をコピー

プラグインに同梱された雛形を作業ディレクトリに展開する：

```bash
cp <plugin-root>/CLAUDE.md <workspace>/CLAUDE.md
cp <plugin-root>/context/*.md <workspace>/context/
```

これにより、ワークスペースルートに `CLAUDE.md` と `context/` 内の7次元のテンプレートファイルが配置される。

### 5. 空ディレクトリを作成

```bash
mkdir -p <workspace>/plans <workspace>/outputs <workspace>/reference
```

### 6. コンテキストインタビューを実施

7つの次元をこの順序で進める。各次元について：
- 何を扱うかを1文で簡潔に説明する
- 3〜6個のオープンエンドな質問を投げる
- 会話形式の回答を聞く。ユーザーにマークダウンを書かせない
- 受け取った回答を使って `<workspace-host>/context/<name>.md` のプレースホルダー（`{{...}}` の中身）を Edit ツールで埋める
- ユーザーが答えられない場合は推測せず、明確な `TODO:` マーカーを残す
- `Last updated:` を今日の日付に設定

| # | 次元 | 対象ファイル |
|---|---|---|
| 1 | ビジネス概要 | `context/business-overview.md` |
| 2 | 個人プロフィール | `context/personal-profile.md` |
| 3 | 戦略 | `context/strategy.md` |
| 4 | 顧客 | `context/customers.md` |
| 5 | 競合 | `context/competitors.md` |
| 6 | 財務 | `context/financials.md` |
| 7 | 現在の優先事項 | `context/current-priorities.md` |

### 7. CLAUDE.md のビジネス名を置き換え

ステップ6でビジネス名がわかったら、`<workspace-host>/CLAUDE.md` 内の `{{BUSINESS_NAME}}` を実際のビジネス名で置き換える（Edit ツールで `replace_all`）。

### 8. 検証して報告

7つのファイルがすべて存在したら：
- 作成したファイルを絶対パスでリスト化する
- ワークスペースの準備が整ったことを確認する
- ワークスペースが使用準備完了であることをユーザーに伝える。何をしたいかに応じて `/aios-workspace:plan` や `/aios-workspace:brainstorm` などのスキルが自然にトリガーされる。

## 再開可能性

ユーザーがインタビューの途中で中断したり終了したりした場合、それまでに収集した内容を保存する。再実行時には既存のコンテキストファイルを検出し、完了済みの次元をスキップして「中断したところから続ける」と提案する。すでに記入済みのファイルについて再質問しない。

## トーン

ユーザーは忙しい経営者であり、開発者ではない。専門用語を避ける。「コンテキストファイルを記入しましょう」ではなく「あなたのビジネスについて教えてください」というフレーミングをする。会話的に保つ — 1ターンに1〜2問、長い箇条書きの羅列は避ける。

## やってはいけないこと

- 7つの次元をすべて1回の大きなプロンプトで扱おうとしない。1つずつ進める。
- テンプレートのプレースホルダー（`{{...}}`）を最終ファイルに残さない。記入するか `TODO:` 行に変換する。
- 7つのコンテキストファイルのいずれかが欠けている状態でステップ8に進まない。
- Cowork 環境で `cd` を使った相対パスで作業しない。bash ツールは call ごとに cwd がリセットされるため、すべてのパスを絶対パスで指定する。
- Cowork 環境で書き込みが Permission denied になったとき、ユーザーに「不可能」と伝えない。ステップ3の `allow_cowork_file_delete` → `rmdir`/`rm` → `mkdir` シーケンスを試す。
- Cowork 環境で bash ツールにホスト側パス（`/Users/...`）を渡さない。逆に Read/Write/Edit ツールに VM パス（`/sessions/...`）を渡さない。bash は VM、ファイルツールはホスト。

---
name: claude-guide
description: Claude Code と Cowork というプロダクトそのものの使い方・機能・挙動について調べ、答える Q&A スキル。経営者が「Claude Code って何ができるの？」「Cowork は普通の Claude Code と何が違う？」「hooks って何？」「MCP って関係ある？」「キーバインド変えたい」など、ツール自体について尋ねた時に必ず使う。また Claude 自身が新しいスキル・hook・MCP 連携を実装したり、ある機能の挙動を試す前に「Claude Code / Cowork は実際にこの挙動をサポートしているか」を確認する時にも必ず使う。/aios-workspace:guide（このプラグインの使い方を案内する）とは別物で、こちらはプロダクトそのものを対象とする。Claude Code・Cowork・hooks・スラッシュコマンド・MCP・スキル・プラグイン・サブエージェント・CLAUDE.md・settings.json・キーバインド・パーミッションモード・Cowork のパス構造・書き込み保護といったキーワードや疑問が出たら、迷わず起動すること。
---

# Claude Guide

Claude Code と Cowork というプロダクトそのものの使い方・機能・挙動を調べ、答える Q&A スキル。経営者の素朴な疑問にも、Claude 自身の挙動確認にも応じる。

## 役割と境界

このスキルが扱うもの・扱わないもの：

| 対象 | 扱う | 扱わない |
|---|---|---|
| Claude Code（CLI） | ✓ | |
| Cowork（Anthropic の VM 実行環境） | ✓ | |
| Claude Agent SDK | | ✓（範囲外） |
| Claude API / Anthropic SDK | | ✓（範囲外） |
| このプラグイン (`aios-workspace`) の使い方 | | ✓ → `/aios-workspace:guide` を案内 |

範囲外の質問が来たら、無理に答えず「このスキルでは扱わない領域です」と明示し、適切な代替（公式ドキュメント、`/aios-workspace:guide` など）を1行だけ提示する。

## 動作フロー

### 1. 質問の有無を判定

スキル起動時の入力を見る。

- **質問が同時に渡された場合**（例: `/aios-workspace:claude-guide hooks ってなんですか？`）→ ステップ 3 へ
- **質問なしで起動された場合** → ステップ 2（受付モード）へ

### 2. 受付モード

短い案内を1つだけ出して質問を待つ。それ以上のことは何もしない（推測で答えない）。

```
Claude Code / Cowork について、何でも聞いてください。

例:
- 「hooks って何？」
- 「Cowork と Claude Code の違いは？」
- 「MCP サーバーって普通の経営者にも関係ある？」
- 「このプラグインに新しいスキルを追加するには？」
```

質問が来たらステップ 3 へ。

### 3. 呼び出し文脈を判定（経営者モード / Claude 内部モード）

直前の会話文脈を読み、どちらの呼ばれ方かを決める：

- **経営者モード**: 直前のメッセージが経営者からの自然言語の質問。ターンの主体が経営者。
- **Claude 内部モード**: Claude 自身が作業中（新スキル実装、hook の挙動確認、デバッグ、MCP 連携の検証など）に呼んだ。

判定が曖昧な場合は **経営者モードを既定** とする。誤って技術仕様の壁を経営者に出すよりは、平易に答えて詳しく聞き返してもらう方がリスクが低いため。

### 4. 知識源の選択

質問のタイプで使い分ける。

| 質問タイプ | 知識源 |
|---|---|
| Claude Code とは / Cowork とは / 両者の違い など基礎概念 | このファイル下部の **静的知識** |
| Cowork のパス構造・書き込み保護などの実用知識 | このファイル下部の **静的知識** |
| 「最新バージョンで X はサポートされているか」「v0.X.Y の hook の挙動」など、変動する仕様 | **WebFetch** で公式ドキュメント |
| Claude が「実際にこの挙動をするのか」を確認したい時 | **必ず WebFetch で最新の公式仕様を確認**（静的知識を信用しない） |

最後の行は重要。Claude 内部モードの本質は「実際にそうなのかを確かめる」ことなので、ローカルの埋め込み知識だけで答えると確認の意味が無くなる。`Claude 内部モードでは原則 WebFetch を発火させる`。

WebFetch の起点 URL は下部の「公式ドキュメント URL」セクションを参照。

### 5. 回答

モードに応じてフォーマットを変える（次節）。回答後、追加質問が来る可能性が高いので、自動でターンを閉じない（「もっと聞きたい点はありますか？」で締めて待つ）。

## 出力フォーマット

### 経営者モード

3段構成、短く。

```
**結論**: <1〜2文の答え>

**もう少し詳しく**: <3〜5文の補足、必要なら例え話を1つ>

**もっと知りたいですか?**: <関連する次の質問の例を2つ>
```

専門用語は使ったその場で噛み砕く。例:「hook（フック、特定のタイミングで自動実行される仕掛け）」「MCP サーバー（Claude が外部ツールを呼ぶための共通の橋渡し）」。

### Claude 内部モード

3段構成、正確さ重視。

```
**仕様**: <事実ベースで簡潔に>

**例**: <コマンド例・コード例・パス例など>

**出典**: <WebFetch で参照した公式ドキュメント URL、または "SKILL.md 内蔵知識（最終更新 YYYY-MM-DD）">
```

「Claude が自分の挙動確認のため呼んだ」場合は、出典が **必ず WebFetch 由来** であること。`SKILL.md 内蔵知識` のままで答えるのは「確認」として機能しないため、内部モードでは原則 WebFetch を発火させる。

### 共通ルール

- 1ターンで複数質問が来た時は、独立した3段構成を質問ごとに繰り返す（混ぜない）。
- 確信が持てない時は推測で答えず「確認します」と明示してから WebFetch する。
- 静的知識を超えた質問（特定 hook の最新引数仕様など）は、モードに関わらず WebFetch を実行する。

## 静的知識（基礎概念のみ・最小限）

ここに書いてある以外のことは WebFetch で確認する。陳腐化を避けるため意図的に最小限。

### Claude Code とは

Anthropic が提供する CLI ツール。ターミナル上で AI「Claude」と対話しながら、自分のパソコンのファイルを実際に読み書き・編集・実行できる。普通のチャット型 AI と違い、コードベース全体を把握して作業を代行できる「実際に手を動かすアシスタント」として動く。経営者の視点では、ファイルやフォルダのある作業（ドキュメント、計画、データ整理）を任せられる相棒。

### Cowork とは

Anthropic が提供するもう一つの Claude 実行環境。Claude Code と違い、各セッションが **クラウド上の VM（仮想マシン）** で動く。ローカル PC を使わずブラウザから Claude に作業させたい時に使う。同時並行に複数のセッションを動かせる。Claude Code と互換のスキル・プラグインの多くがそのまま動くが、ファイルパスの扱いが異なる（後述）。

### Claude Code と Cowork の比較

| 観点 | Claude Code | Cowork |
|---|---|---|
| 実行場所 | ユーザーのローカル PC | Anthropic 側のクラウド VM |
| 作業ディレクトリ | カレントディレクトリ（ユーザーが選ぶ） | `/sessions/<name>/mnt/...` |
| Read/Write/Edit ツール | 上と同じパス | ホスト側のパス（`/Users/<user>/Documents/Claude/Projects/...`） |
| Bash ツール | 上と同じパス | VM 内パス（`/sessions/<name>/mnt/...`） |
| ファイル永続化 | ローカルファイルシステム | セッション付きのリモートストレージ |
| 同時並行 | 通常1セッション | 複数セッション可 |
| 想定シーン | 自分の手元で開発・作業 | ブラウザだけで完結させたい / 複数並行 |

Cowork の最重要ポイントは **「Bash と Read/Write/Edit でパスが違う」**。同じファイルを Bash では `/sessions/<name>/mnt/foo.txt`、Read/Write/Edit では `/Users/<user>/Documents/Claude/Projects/foo.txt` で参照する。混同するとファイルが見つからない・書き込みが失敗する。

また、Cowork では **作業ディレクトリ直下の事前存在ファイルは書き込み保護** されている。削除・上書きする前に `mcp__cowork__allow_cowork_file_delete` を呼んで許可を取る必要がある。

### スラッシュコマンドの仕組み

入力欄で `/` から始まる文字列を打つと、登録されたスキル（決まった作業手順）が起動する。書式は `/<plugin>:<skill>`（例: `/aios-workspace:setup`）。スキルを使わず日本語で話しかけても動く — スラッシュコマンドは「決まった作業を確実にやってもらう」ためのショートカット。

### 主要な拡張概念（要約のみ）

それぞれ詳細を知りたい時は WebFetch で公式ドキュメントを参照。

| 概念 | 1行説明 |
|---|---|
| **スキル** | 決まった作業手順を SKILL.md に書いたもの。スラッシュコマンドや description のキーワード一致で自動起動する。 |
| **プラグイン** | 複数のスキル・コマンド・hook・MCP 設定をまとめた配布単位。マーケットプレイスでインストールできる。 |
| **hooks** | 特定のタイミング（PreToolUse, PostToolUse, SessionStart, Stop など）で自動的にシェルコマンドを実行する仕掛け。`settings.json` に登録する。 |
| **MCP サーバー** | Claude が外部のツール・サービス（GitHub, Slack, ブラウザ等）を呼ぶための共通インターフェース。 |
| **サブエージェント** | メインの Claude から切り離して特定のタスクを別コンテキストで実行する子エージェント。`Agent` ツールで呼ぶ。 |
| **CLAUDE.md** | プロジェクト直下に置くと毎セッション自動で読み込まれる、Claude 向けの恒久的な指示書。 |
| **settings.json** | Claude Code の設定ファイル。`~/.claude/settings.json`（グローバル）と `<repo>/.claude/settings.json`（プロジェクト）の2層がある。permissions, env, hooks などを書く。 |
| **パーミッションモード** | Claude のツール実行をどこまで自動許可するか。`default` / `acceptEdits` / `plan` / `bypassPermissions` など。 |
| **キーバインド** | キーボードショートカット。`~/.claude/keybindings.json` で変更可能。 |
| **IDE 統合** | VS Code / JetBrains で Claude Code を呼べる拡張機能。 |

## 公式ドキュメント URL（WebFetch の起点）

最新仕様の確認時はここから取りに行く。リンク切れや改名は十分あり得るので、目的のページに辿り着けない時は WebSearch にフォールバックする。

| トピック | URL |
|---|---|
| Claude Code 概要 | https://docs.claude.com/en/docs/claude-code/overview |
| スラッシュコマンド | https://docs.claude.com/en/docs/claude-code/slash-commands |
| Hooks | https://docs.claude.com/en/docs/claude-code/hooks |
| MCP | https://docs.claude.com/en/docs/claude-code/mcp |
| スキル | https://docs.claude.com/en/docs/claude-code/skills |
| プラグイン | https://docs.claude.com/en/docs/claude-code/plugins |
| サブエージェント | https://docs.claude.com/en/docs/claude-code/sub-agents |
| 設定（settings.json） | https://docs.claude.com/en/docs/claude-code/settings |
| IDE 統合 | https://docs.claude.com/en/docs/claude-code/ide-integrations |
| Cowork 関連 | 専用 URL が見つからない場合は WebSearch で「Anthropic Cowork」「Claude Cowork sessions mnt」を検索 |

このリストに無い領域、またはここでは古くなっている可能性がある場合は、まず WebSearch で公式ドキュメントの最新ページを探してから WebFetch する。

## トーン

- 経営者モード: 専門用語は最小限、その場で噛み砕く。1段落2〜4文。
- Claude 内部モード: 事実ベース、引用元を明示。冗長な前置きはしない。
- どちらのモードでも、不確かなことを断定しない。「確認します」と告げて WebFetch することを恐れない。

## やってはいけないこと

- **範囲外の質問（Agent SDK, Claude API, このプラグインの使い方）に無理やり答える**。1行だけ案内して止める。
- **Claude 内部モードで静的知識のまま答える**。「確認」の用が為さなくなる。原則 WebFetch を発火させる。
- **URL を推測で出す**。下部リストに無いページが必要な時は WebSearch を経由する。
- **経営者モードで技術仕様の壁を貼る**。短く、結論ファースト、続きは聞かれてから出す。
- **質問なしで起動された時に推測で何かを答える**。受付モードに留まり、質問を待つ。
- **回答を1ターンで打ち切る**。追加質問の余地を残す（「もっと知りたい点はありますか？」で締める）。

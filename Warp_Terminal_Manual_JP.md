# Warp ターミナル Windows版 実用マニュアル

> **対象バージョン**: Warp 2.0（Agentic Development Environment）
> **対象OS**: Windows（PowerShell / WSL / Git Bash 対応、x64 & ARM64）
> **作成日**: 2026年3月

---

## 目次

1. [Warpとは何か](#1-warpとは何か)
2. [ワークスペースとしての使い方](#2-ワークスペースとしての使い方)
3. [ノートブック（Notebooks）の使い方](#3-ノートブックnotebooksの使い方)
4. [タブとペインの切り替え・操作](#4-タブとペインの切り替え操作)
5. [コンテキスト注入（Rules / AGENTS.md）](#5-コンテキスト注入rules--agentsmd)
6. [エージェントモード](#6-エージェントモード)
7. [よく使うショートカット一覧](#7-よく使うショートカット一覧)
8. [参考リンク集](#8-参考リンク集)

---

## 1. Warpとは何か

Warp 2.0 は単なるターミナルエミュレータではなく、**ADE（Agentic Development Environment）** と位置づけられている。4つの柱で構成されている。

| 柱 | 役割 |
|---|---|
| **Terminal** | 従来のコマンドライン操作。ブロック単位の出力表示やモダンなテキスト編集機能 |
| **Code** | 組み込みのコードエディタ。ターミナルと同一画面でファイルを編集 |
| **Agents** | AIエージェント。自然言語でコマンドを実行・デバッグ・コード生成 |
| **Drive** | Warp Drive。ワークフロー、ノートブック、ルール、環境変数を保存・共有 |

Windows版ではPowerShell、WSL（Windows Subsystem for Linux）、Git Bashのいずれかをシェルとして選択できる。

---

## 2. ワークスペースとしての使い方

### 2.1 基本的な画面構成

Warpを開くと、1つのウィンドウの中にタブとペインがある。タブはブラウザのタブと同じ概念で、ペインは1つのタブ内の分割画面。

```
┌──────────────────────────────────────────┐
│ [タブ1] [タブ2] [タブ3]                     │
├──────────────────┬───────────────────────┤
│                  │                       │
│   ペイン 1        │     ペイン 2           │
│  (PowerShell)    │    (WSL / bash)       │
│                  │                       │
├──────────────────┴───────────────────────┤
│                                          │
│   ペイン 3（下段全幅）                      │
│                                          │
└──────────────────────────────────────────┘
```

### 2.2 Launch Configurations（起動設定）

プロジェクトごとにウィンドウ・タブ・ペインの構成を保存しておき、ワンクリックで再現できる機能。

**作成方法（UIから）:**

1. Command Palette を開く（`Ctrl+Shift+P`）
2. 「Save New Launch Configuration」と入力
3. 現在のレイアウト（タブ数、ペイン分割、作業ディレクトリ）が保存される

**作成方法（YAMLファイルで手動作成）:**

ファイルの保存先: `~/.warp/launch_configurations/`

```yaml
# 例: my-project.yaml
name: "My Web Project"
windows:
  - tabs:
      - title: "Frontend"
        layout:
          panes:
            - command: "cd ~/projects/frontend && npm run dev"
            - command: "cd ~/projects/frontend && code ."
          split_direction: horizontal
      - title: "Backend"
        layout:
          panes:
            - command: "cd ~/projects/backend && python manage.py runserver"
      - title: "Database"
        layout:
          panes:
            - command: "psql -U myuser -d mydb"
```

### 2.3 プロジェクト管理

Gitリポジトリのディレクトリに `cd` すると、Warpが自動的にプロジェクトとして認識し、コードベースのインデックスを開始する。これにより、エージェントモードでプロジェクト固有の質問に答えられるようになる。

---

## 3. ノートブック（Notebooks）の使い方

### 3.1 ノートブックとは

Warp Drive内にある **Notebooks** は、Markdownベースの「実行可能なドキュメント」。Jupyter Notebookのターミナル版と考えるとわかりやすい。

**通常のドキュメントとの違い:**

| 項目 | 通常のMarkdown | Warp Notebook |
|---|---|---|
| コマンド実行 | できない | クリック1つで実行可能 |
| リアルタイム同期 | なし | チーム全員にリアルタイム反映 |
| 引数パラメータ化 | なし | `{{変数名}}` で引数指定可能 |
| ワークフロー埋め込み | なし | 既存ワークフローの埋め込み可能 |
| オフライン利用 | ファイル依存 | 読み取り専用モードで利用可能 |

### 3.2 ノートブックの作成手順

1. **Warp Driveパネルを開く**: `Ctrl+Shift+\`
2. **新規作成**: 「+」アイコンをクリック → 「New Notebook」を選択
3. **またはCommand Paletteから**: `Ctrl+Shift+P` → 「Create New Notebook」と入力

### 3.3 ノートブックの書き方

ノートブックのエディタはMarkdown記法を認識する。

```markdown
# サーバーセットアップ手順

## 1. 依存パッケージのインストール

まずNode.jsの依存パッケージをインストールする。

​```bash
npm install
​```

## 2. 環境変数の設定

以下のコマンドで `.env` ファイルを作成する。
ポート番号は環境に合わせて変更すること。

​```bash
cp .env.example .env
echo "PORT={{ポート番号}}" >> .env
​```

## 3. 開発サーバーの起動

​```bash
npm run dev
​```
```

**ポイント:**
- コードブロック（` ``` `）内のコマンドは「Run」ボタンが表示され、クリックでターミナルに送信される
- `{{ポート番号}}` のような `{{二重波括弧}}` は実行時に入力を求められるパラメータになる
- 「+」ボタンで既存のWorkflowを埋め込むことも可能

### 3.4 ノートブックの共有

1. Warp Driveパネルで対象のノートブックを見つける
2. **Personal** フォルダからチームの **Shared** フォルダにドラッグ＆ドロップ
3. チームメンバー全員に編集権限が付与され、変更はリアルタイムに同期

### 3.5 エクスポート

ノートブックはMarkdown形式でエクスポートできるため、他のツールへの移行も容易。ベンダーロックインの心配はない。

---

## 4. タブとペインの切り替え・操作

### 4.1 タブ操作

| 操作 | ショートカット |
|---|---|
| 新しいタブを開く | `Ctrl+Shift+T` |
| タブを閉じる | `Ctrl+Shift+W` |
| 閉じたタブを復元 | `Ctrl+Alt+T` |
| 前のタブへ移動 | `Ctrl+PgUp` |
| 次のタブへ移動 | `Ctrl+PgDn` |
| タブ1〜8に直接ジャンプ | `Ctrl+1` 〜 `Ctrl+8` |
| 最後のタブにジャンプ | `Ctrl+9` |
| タブを左に移動 | `Ctrl+Shift+Left` |
| タブを右に移動 | `Ctrl+Shift+Right` |

**タブのカスタマイズ:**
- タブを右クリック → 名前の変更、色の変更が可能
- プロジェクトごとに色分けしておくと視認性が上がる

### 4.2 ペイン（画面分割）操作

| 操作 | ショートカット |
|---|---|
| 右にペインを分割 | `Ctrl+Shift+D` |
| 下にペインを分割 | `Ctrl+Shift+E` |
| ペイン間の移動（上下左右） | `Ctrl+Alt+矢印キー` |
| 現在のペインを最大化/元に戻す | `Ctrl+Shift+Enter` |
| ペインを閉じる | `Ctrl+Shift+W`（タブ内最後のペインならタブごと閉じる） |

### 4.3 実用的な使い方の例

**フロントエンド開発の場合:**
```
タブ1「Dev Server」
  ├── 左ペイン: npm run dev（開発サーバー稼働中）
  └── 右ペイン: 作業用シェル（git操作やファイル操作）

タブ2「Testing」
  └── テスト実行用

タブ3「Database」
  └── psql や prisma studio など
```

---

## 5. コンテキスト注入（Rules / AGENTS.md）

### 5.1 Rulesとは

**Rules（ルール）** は、エージェントに対して永続的なコンテキストを提供する仕組み。プロジェクトの技術スタック、コーディング規約、よくある作業パターンなどを事前に伝えておくことで、エージェントの回答精度が大幅に向上する。

### 5.2 ルールの種類

| 種類 | 設定場所 | 適用範囲 |
|---|---|---|
| **グローバルルール** | Warp Drive > Personal > Rules | すべてのプロジェクトに適用 |
| **プロジェクトルール** | リポジトリルートの `AGENTS.md` | そのプロジェクト内のみ |
| **ディレクトリルール** | 任意のディレクトリの `AGENTS.md` | そのディレクトリ以下で適用 |

### 5.3 AGENTS.md（旧 WARP.md）の書き方

プロジェクトのルートディレクトリに `AGENTS.md` というファイルを作成する（ファイル名は**大文字**で記述すること）。

```markdown
# プロジェクト: My Web App

## 技術スタック
- フロントエンド: React 19 + TypeScript + Vite
- バックエンド: Python 3.12 + FastAPI
- データベース: PostgreSQL 16
- ORM: SQLAlchemy 2.0
- パッケージマネージャ: pnpm（フロント）、uv（バック）

## コーディング規約
- TypeScriptでは関数コンポーネントのみ使用（クラスコンポーネント禁止）
- Python側はtype hintsを必ず付ける
- テストはpytest（バック）とVitest（フロント）を使用
- コミットメッセージはConventional Commitsに従う

## ディレクトリ構成
- `frontend/` - Reactアプリケーション
- `backend/` - FastAPI サーバー
- `infrastructure/` - Terraform / Docker設定
- `docs/` - ドキュメント

## よくある作業
- 開発サーバー起動: `cd frontend && pnpm dev`（フロント）、`cd backend && uvicorn main:app --reload`（バック）
- マイグレーション: `cd backend && alembic upgrade head`
- テスト: `cd frontend && pnpm test` / `cd backend && pytest`

## 注意事項
- `.env` ファイルは絶対にコミットしないこと
- API キーは環境変数から読み取ること
- main ブランチへの直接 push は禁止
```

### 5.4 対応しているルールファイル形式

Warpは以下のファイルを自動認識する（他のAIツールとの互換性あり）:

- `AGENTS.md`（Warp推奨のデフォルト）
- `WARP.md`（旧形式、引き続きサポート）
- `CLAUDE.md`（Claude Code互換）
- `.cursorrules`（Cursor互換）
- `AGENT.md`
- `GEMINI.md`
- `.clinerules`
- `.windsurfrules`
- `.github/copilot-instructions.md`（GitHub Copilot互換）

すでに他のAIツール用にルールファイルを作っている場合、そのまま流用できる。

### 5.5 MCP（Model Context Protocol）サーバーの設定

MCPサーバーを接続することで、エージェントに外部ツールやデータソースへのアクセスを追加できる。

**設定方法1: 起動コマンド方式**
```json
{
  "mcpServers": {
    "my-server": {
      "command": "npx",
      "args": ["-y", "@my-org/mcp-server"],
      "env": {
        "API_KEY": "your-api-key"
      },
      "working_directory": "/path/to/project"
    }
  }
}
```

**設定方法2: SSE（Server-Sent Events）方式**
```json
{
  "mcpServers": {
    "remote-server": {
      "url": "https://my-mcp-server.example.com/sse",
      "headers": {
        "Authorization": "Bearer your-token"
      }
    }
  }
}
```

複数のMCPサーバーを同時に設定でき、`mcpServers` オブジェクト内にキー名を分けて記述すれば自動的にすべて接続される。

---

## 6. エージェントモード

### 6.1 エージェントモードへの切り替え

| 方法 | 操作 |
|---|---|
| ショートカット | `Ctrl+I` |
| 自動検出 | 入力欄に自然言語を打つと自動でエージェントモードに切り替わる（設定で有効化） |
| ボタン | ターミナルフッターの「Use Agent」ボタン |

### 6.2 基本的な使い方

エージェントモードに入ったら、やりたいことを日本語または英語で入力するだけ。

**例:**
```
> このディレクトリのPythonファイルで、未使用のimportがあるものを探して修正して
```

エージェントは以下のステップで動作する:
1. 入力を解釈し、必要なコマンドを判断
2. コマンドを提示し、**実行前にユーザーの承認を求める**
3. 承認後にコマンドを実行
4. 出力結果を分析し、次のアクションを判断
5. エラーがあれば修正案を提示

### 6.3 実用的なユースケース

**即座の修正（Immediate Fix）:**
コマンドがエラーで失敗した場合、エージェントモードが約2秒で3つの修正候補をランク付きで提示する。

**マルチステップの作業:**
```
> Dockerfileを作って、Node.js 20ベースで、pnpmを使って依存関係インストールして、
  本番ビルドを実行するマルチステージビルドにして
```
→ エージェントがDockerfile作成 → ビルドテスト → エラーがあれば修正、という流れを自律的に実行

**デバッグ:**
```
> npm run build でエラーが出る。原因を調べて修正して
```
→ ビルド実行 → エラー内容を分析 → 修正を提案・適用

### 6.4 Full Terminal Use（インタラクティブツールとの連携）

すでに起動中の対話型ツール内でもエージェントを呼び出せる。

**対応例:**
- データベースシェル（`psql`、`mysql`）
- デバッガー（`gdb`、`pdb`）
- 開発サーバー（`npm run dev` の出力を監視）
- テキストエディタ（`vim`、`nano`）

**使い方:**
1. 対話型ツールを起動した状態で `Ctrl+I`
2. またはフッターの「Use Agent」をクリック
3. エージェントがそのツールのコンテキストを理解して操作を支援

### 6.5 スラッシュコマンド

エージェントモードの入力欄で `/` を入力すると、利用可能なスラッシュコマンドの一覧が表示される。

| コマンド例 | 説明 |
|---|---|
| `/explain` | 直前のコマンドやエラーの説明 |
| `/fix` | エラーの修正を提案 |
| `/test` | テストコードの生成 |

スラッシュコマンドはカスタマイズ可能で、Warp Driveにプロンプトとして保存しておけば `/` メニューから呼び出せる。

### 6.6 Conversation（会話スレッド）の管理

#### タブ（Tab）とConversationの違い

WarpにおけるTabとConversationはまったく別の概念である。

| 概念 | 説明 | 例え |
|---|---|---|
| **Tab（タブ）** | ターミナルの「入れ物」。独立したシェルセッションが動く物理的な場所 | ブラウザのタブ |
| **Conversation（会話）** | エージェントとのやりとりの「スレッド」。1つのタブ内で複数持てる | ChatGPTの会話スレッド |

```
┌─ タブ1 ──────────────────────────────┐
│  シェルセッション（PowerShell）         │
│                                      │
│  Conversation A: 「Dockerの設定を手伝って」  │
│  Conversation B: 「テストを書いて」          │
│  Conversation C: 「このエラーを直して」      │
│                                      │
│  ※ 会話スレッドを切り替えて使える        │
└──────────────────────────────────────┘
```

#### Pair Mode（ペアモード）

エージェントモードに入ると、デフォルトで **Pair Mode** になる。Pair Modeでは直前の会話の続きとしてフォローアップの質問やタスクを依頼できる。エージェントは過去のやりとりのコンテキストを保持しているので、関連する質問なら同じConversation内で続けた方が精度が高い。

#### Conversationの操作（Windows）

| 操作 | 方法 |
|---|---|
| 会話メニューを開く | `Ctrl+Shift+Y` |
| 新しいConversationを開始 | 会話メニュー → 「New Conversation」、または `/new` コマンド |
| 過去のConversationに戻る | 会話メニューから選択、または入力欄のピンクの会話チップをクリック |

#### 使い分けのベストプラクティス

- **同じConversationで続ける場合**: 質問が前の話題と関連しているとき。文脈を活かせるので回答精度が上がる
- **新しいConversationを始める場合**: まったく別の話題に移るとき。無関係なコンテキストが混ざると精度が落ちる
- **注意点**: Conversationが長くなりすぎると動作が遅くなり、回答の質も下がる。別の作業に移るときは新しいConversationを始めるのが推奨

---

## 7. よく使うショートカット一覧（Windows版）

### 一般操作

| 操作 | ショートカット |
|---|---|
| Command Palette | `Ctrl+Shift+P` |
| Warp Drive パネル表示 | `Ctrl+Shift+\` |
| エージェントモード切替 | `Ctrl+I` |
| 設定を開く | `Ctrl+,` |

### タブ

| 操作 | ショートカット |
|---|---|
| 新しいタブ | `Ctrl+Shift+T` |
| タブを閉じる | `Ctrl+Shift+W` |
| 閉じたタブ復元 | `Ctrl+Alt+T` |
| 前のタブ / 次のタブ | `Ctrl+PgUp` / `Ctrl+PgDn` |
| タブ番号でジャンプ | `Ctrl+1` 〜 `Ctrl+9` |

### ペイン

| 操作 | ショートカット |
|---|---|
| 右に分割 | `Ctrl+Shift+D` |
| 下に分割 | `Ctrl+Shift+E` |
| ペイン間移動 | `Ctrl+Alt+矢印キー` |
| ペイン最大化 | `Ctrl+Shift+Enter` |

### Conversation（会話）

| 操作 | ショートカット |
|---|---|
| 会話メニュー | `Ctrl+Shift+Y` |
| 新しい会話を開始 | `/new` コマンド |

### 入力・編集

| 操作 | ショートカット |
|---|---|
| 入力欄をクリア | `Ctrl+U` |
| 直前のコマンド | `↑` |
| コマンド検索（履歴） | `Ctrl+R` |
| 補完を受け入れ | `Tab` |
| ブロック検索 | `Ctrl+Shift+F` |

---

## 8. 参考リンク集

| リソース | URL |
|---|---|
| Warp 公式サイト | https://www.warp.dev/ |
| Warp ドキュメント（トップ） | https://docs.warp.dev/ |
| Warp Drive | https://docs.warp.dev/knowledge-and-collaboration/warp-drive |
| Notebooks | https://docs.warp.dev/knowledge-and-collaboration/warp-drive/notebooks |
| Workflows | https://docs.warp.dev/knowledge-and-collaboration/warp-drive/workflows |
| Rules（コンテキスト注入） | https://docs.warp.dev/agent-platform/capabilities/rules |
| Agent Mode | https://docs.warp.dev/agents/using-agents |
| Agent Conversations | https://docs.warp.dev/agents/using-agents/agent-conversations |
| Full Terminal Use | https://docs.warp.dev/agent-platform/capabilities/full-terminal-use |
| MCP Servers | https://docs.warp.dev/agent-platform/capabilities/mcp |
| Windows版 Warp | https://www.warp.dev/windows-terminal |
| スラッシュコマンド | https://docs.warp.dev/agent-platform/capabilities/slash-commands |

---

*このマニュアルはWarp公式ドキュメントに基づいて作成されています。Warpは活発に開発が進んでいるため、最新の情報は公式ドキュメントも併せてご確認ください。*

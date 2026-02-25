# QA Planner Plugin

GitHub の PR URL を入力するだけで、テスト設計書およびリグレッションテスト計画書を自動生成する Claude Cowork プラグイン。

## インストール

```
claude plugins add knowledge-work-plugins/qa-planner
```

## セットアップ

### 1. GitHub PAT の設定

```bash
export GITHUB_PERSONAL_ACCESS_TOKEN="ghp_xxxxxxxxxxxx"
# 永続化する場合
echo 'export GITHUB_PERSONAL_ACCESS_TOKEN="ghp_xxxxxxxxxxxx"' >> ~/.zshrc
```

必要なスコープ：`repo`, `read:org`

### 2. Cowork での設定

Claude Desktop → Cowork タブ → Settings → Connectors → **GitHub をオン**

### 3. knowledge/ のカスタマイズ

自社構成に合わせて以下のファイルを書き換えてください：

```
knowledge/service-dependency.md   ← 自社サービス構成・API・イベント依存
knowledge/environment-config.md   ← 自社環境URL・デプロイ順序
knowledge/incident-history.md     ← 過去の障害事例（随時追記）
knowledge/user-flows.md           ← 主要ユーザーフロー定義
```

## コマンド

| コマンド | 概要 |
|---------|------|
| `/create-test` | PR URL からテスト設計書を生成 |
| `/create-regression` | 複数の PR URL からリグレッションテスト計画書を生成 |

## スキル

| スキル | 役割 |
|-------|------|
| `qa-knowledge` | QA 基準・波及リスク判定ルール・アーキテクチャ知識 |
| `test-output-format` | テスト設計書のテンプレートと記述ルール |
| `regression-output-format` | リグレッションテスト計画書のテンプレートと記述ルール |

## 使用例

### テスト設計書の生成

```
You: /create-test https://github.com/myorg/backend/pull/88

Claude: PR の diff・コメント・変更ファイルを取得中...
        [テスト設計書を生成]
        保存先: ./backend/PR-88/20260221_テスト設計_PR88.md
```

### リグレッションテスト計画書の生成

```
You: /create-regression
     https://github.com/myorg/frontend/pull/210
     https://github.com/myorg/backend/pull/88
     https://github.com/myorg/infra/pull/15

Claude: 3件の PR を横断分析中...
        [サービス間波及リスクを分析]
        [リグレッションテスト計画書を生成]
        保存先:
          ./frontend/PR-210/20260221_regression-plan_v2.4.0.md
          ./backend/PR-88/20260221_regression-plan_v2.4.0.md
          ./infra/PR-15/20260221_regression-plan_v2.4.0.md
```

## 注意事項

- Cowork はリサーチプレビューのため、規制対象業務への使用は避けること
- テストデータには本番データを使用しないこと
- `GITHUB_PERSONAL_ACCESS_TOKEN` はファイルにハードコードしないこと
- プラグインのチーム配布は各自インストールが必要

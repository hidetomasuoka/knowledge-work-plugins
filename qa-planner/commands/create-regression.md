---
description: 複数の GitHub PR URL からリグレッションテスト計画書を生成する
argument-hint: "<GitHub PR URLs (スペースまたは改行区切り)>"
---

# Create Regression

> GitHub が接続されていない場合は [CONNECTORS.md](../CONNECTORS.md) を確認してください。

リリースに含まれる複数の PR を横断的に分析し、サービス間の波及リスクを考慮したリグレッションテスト計画書を生成します。

## ワークフロー

### 1. PR URL の受け取り

引数として複数の PR URL が渡された場合はそのまま使用する。渡されていない場合：

```
リグレッションテスト計画書を作成します。
対象 PR の URL を貼り付けてください（複数可、スペースまたは改行区切り）。

例:
https://github.com/myorg/frontend/pull/210
https://github.com/myorg/backend/pull/88
https://github.com/myorg/infra/pull/15

リリースバージョン（任意）:
```

### 2. 全 PR 情報の一括取得

~~github を使って各 PR の以下を取得する（並列実行可）：

- PR タイトル・説明・変更ファイル一覧・diff
- リポジトリ名・PR 番号・マージ先ブランチ

### 3. 変更の横断分析

**qa-knowledge** スキルと `knowledge/` 配下のファイルを参照して分析する。

**サービス間波及の確認：**

```
API 変更      → knowledge/service-dependency.md で下流サービスを特定
イベント変更   → Subscriber サービスを特定
認証・共通変更 → 全サービスを要確認リストへ
インフラ変更   → knowledge/service-dependency.md で利用サービスを特定（Redis・MQ 等）
```

**ユーザーフロー影響の確認：**
- `knowledge/user-flows.md` で影響するフローを列挙
- 複数 PR にまたがる変更で壊れるフローを特定

**過去インシデントの照合：**
- `knowledge/incident-history.md` で類似の変更パターンを検索
- 再発防止テスト観点を追加

**環境・デプロイリスクの確認：**
- `knowledge/environment-config.md` でデプロイ順序や環境差異を確認
- STG/本番で挙動が異なる設定を特定

### 4. テスト範囲の分類

全 PR を通じて影響を受ける機能を以下の3段階で分類する：

- ✅ **必須テスト** — 直接変更された機能・確実に波及するサービス
- 🔶 **推奨テスト** — 波及リスクがある既存機能（時間があれば確認）
- ⬜ **スキップ可** — 影響なしと判断した機能

### 5. リグレッションテスト計画書の生成

**regression-output-format** スキルのテンプレートに従って計画書を生成する。

### 6. ファイルへの保存

リポジトリごとに以下のパスに Markdown ファイルを保存する：

```
{作業フォルダ}/{バージョン}/{リポジトリ名}/PR-{番号}/YYYYMMDD_regression-plan_{バージョン}.md
```

例：
```
./v2.4.0/frontend/PR-210/20260221_regression-plan_v2.4.0.md
./v2.4.0/backend/PR-88/20260221_regression-plan_v2.4.0.md
./v2.4.0/infra/PR-15/20260221_regression-plan_v2.4.0.md
```

バージョンが指定されていない場合は `{作業フォルダ}/{リポジトリ名}/PR-{番号}/YYYYMMDD_regression-plan.md` に保存する。

保存後、以下を報告する：
- 保存先パス一覧
- 横断的なリスクサマリー（特に注意が必要なサービス間波及）
- テスト項目数の合計（必須 / 推奨 / スキップ可）

## 注意事項

- PR 数が多い場合は重複するテスト項目を集約してスリム化する
- ~~github が接続されていない場合はエラーを表示し、接続方法を案内する
- デプロイ順序がある場合は計画書内に明記する

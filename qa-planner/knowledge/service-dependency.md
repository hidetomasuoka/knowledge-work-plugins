# サービス依存関係

> このファイルは自社のサービス構成に合わせて書き換えてください。

## サービス一覧

| サービス名 | 役割 | リポジトリ |
|-----------|-----|----------|
| frontend | ユーザー向け Web UI | myorg/frontend |
| backend | メインの API サーバー | myorg/backend |
| infra | インフラ設定 (Terraform 等) | myorg/infra |

## API 依存関係

<!-- どのサービスが誰の API を呼ぶか -->

```
frontend
  └─ backend API (/api/v1/*)

backend
  └─ 外部サービス API (例: Stripe, SendGrid)
```

## イベント / メッセージキュー依存関係

<!-- Publisher → Topic → Subscriber の関係 -->

```
backend (Publisher)
  └─ order.created → backend (Subscriber: 通知処理)
```

## 共通インフラ依存

| インフラ | 利用サービス | 用途 |
|---------|-----------|-----|
| Redis | backend | セッション・キャッシュ |
| PostgreSQL | backend | メインデータベース |

---

## カスタマイズ方法

1. 上記のサービス一覧を自社のサービスに書き換える
2. API 依存関係を実際の呼び出し関係に書き換える
3. イベント/メッセージキュー依存があれば追記する
4. インフラ依存を実際の利用状況に書き換える

書き換え後は `/create-test` や `/create-regression` 実行時に自動的に参照されます。

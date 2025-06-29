# ClickHouse + Grafana Docker Compose 環境

ClickHouseとGrafanaを統合したDocker Compose環境です。Apple Silicon (M1/M2) Macにも対応しています。

## 📋 概要

このプロジェクトは以下を提供します：
- **ClickHouse**: 高性能な分析データベース
- **Grafana**: データ可視化・ダッシュボードツール
- **自動プロビジョニング**: ClickHouseデータソースの自動設定
- **Apple Silicon対応**: M1/M2 Macでの動作確認済み

## 🏗️ アーキテクチャ

```
┌─────────────────┐    ┌─────────────────┐
│   Grafana       │    │   ClickHouse    │
│   Port: 3000    │◄──►│   Port: 8123    │
│                 │    │   Port: 9001    │
└─────────────────┘    └─────────────────┘
```

## 🚀 クイックスタート

### 1. リポジトリのクローン
```bash
git clone <repository-url>
cd clickhouse-docker-compose
```

### 2. サービスの起動
```bash
docker compose up -d
```

### 3. サービスの確認
```bash
docker compose ps
```

### 4. Grafanaにアクセス
- URL: http://localhost:3000
- ユーザー名: `admin`
- パスワード: `admin`

## 🔧 設定詳細

### ClickHouse設定
- **ポート**: 
  - HTTP: `8123`
  - Native TCP: `9001` (通常の9000から変更)
- **データベース**: `mydb`
- **ユーザー**: `myuser`
- **パスワード**: `password`

### Grafana設定
- **ポート**: `3000`
- **管理者アカウント**: `admin` / `admin`
- **プラグイン**: ClickHouse datasource (自動インストール)
- **データソース**: 自動プロビジョニング済み

## 📊 データソース設定

ClickHouseデータソースは自動的に設定されますが、手動で確認する場合：

1. Grafana → Connections → Data sources
2. ClickHouse データソースを選択
3. 以下の設定を確認：
   - **Server**: `clickhouse:8123`
   - **Database**: `mydb`
   - **Username**: `myuser`
   - **Password**: `password`

## 🎯 使用例

### サンプルクエリ
```sql
-- 基本的な選択
SELECT * FROM mydb.sample_metrics LIMIT 10;

-- 時系列データ
SELECT 
    toDateTime(timestamp) as time,
    avg(value) as avg_value
FROM mydb.sample_metrics 
WHERE timestamp >= now() - INTERVAL 1 HOUR
GROUP BY time
ORDER BY time;
```

### ダッシュボード作成
1. Grafana → Dashboards → New dashboard
2. Add visualization を選択
3. ClickHouse データソースを選択
4. クエリを入力して可視化

## 🔍 トラブルシューティング

### サービスが起動しない場合
```bash
# ログの確認
docker compose logs clickhouse
docker compose logs grafana

# サービスの再起動
docker compose restart
```

### ClickHouse接続テスト
```bash
# HTTPエンドポイントの確認
curl http://localhost:8123/ping

# 期待される応答: "Ok."
```

### Grafanaプラグインの確認
```bash
# プラグインログの確認
docker compose logs grafana | grep -i plugin
```

## 📁 ディレクトリ構造

```
clickhouse-docker-compose/
├── docker-compose.yaml           # Docker Compose設定
├── README.md                     # このファイル
├── click-data/                   # ClickHouseデータ永続化
│   ├── var/lib/clickhouse/
│   └── var/log/clickhouse-server/
└── grafana/
    └── provisioning/
        └── datasources/
            └── clickhouse.yaml   # データソース自動設定
```

## 🛠️ 管理コマンド

### サービス管理
```bash
# サービス起動
docker compose up -d

# サービス停止
docker compose down

# サービス再起動
docker compose restart

# ログ確認
docker compose logs -f
```

### データ管理
```bash
# データボリュームの確認
docker volume ls

# データのバックアップ（例）
docker compose exec clickhouse clickhouse-client --query "BACKUP DATABASE mydb TO 'backup.sql'"
```

## 🔒 セキュリティ考慮事項

### 本番環境での推奨事項
1. **パスワード変更**: デフォルトパスワードを変更
2. **読み取り専用ユーザー**: Grafana用に専用ユーザーを作成
3. **ネットワーク制限**: 必要なポートのみ公開
4. **HTTPS設定**: SSL/TLS証明書の設定

### 読み取り専用ユーザーの作成例
```sql
-- ClickHouseで実行
CREATE USER grafana_user IDENTIFIED BY 'secure_password';
GRANT SELECT ON mydb.* TO grafana_user;
```

## 📈 パフォーマンス最適化

### ClickHouse設定
- メモリ制限の調整
- ディスク容量の監視
- インデックス戦略の検討

### Grafana設定
- クエリキャッシュの活用
- ダッシュボードの最適化
- アラート設定

## 🔗 関連リンク

- [ClickHouse公式ドキュメント](https://clickhouse.com/docs)
- [Grafana公式ドキュメント](https://grafana.com/docs/)
- [ClickHouse Grafanaプラグイン](https://grafana.com/grafana/plugins/grafana-clickhouse-datasource/)
- [ClickHouse + Grafana統合ガイド](https://clickhouse.com/docs/jp/integrations/grafana)

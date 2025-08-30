# InvestTrack Database Design

## 概要

InvestTrackは、個人投資家向けのポートフォリオ管理および投資分析ツールです。本プロジェクトでは、ユーザーが保有する資産や取引履歴を管理し、パフォーマンスをトラッキングします。このドキュメントは、InvestTrackのデータベース設計について説明します。

## テーブル設計

### 1. **m_user（ユーザー）**
ユーザーの基本情報を管理します。

```sql
CREATE TABLE m_user (
  id SERIAL PRIMARY KEY,
  username VARCHAR(255) UNIQUE NOT NULL,
  email VARCHAR(255) UNIQUE NOT NULL,
  password VARCHAR(255) NOT NULL,
  first_name VARCHAR(255),
  last_name VARCHAR(255),
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  created_by VARCHAR(255),
  updated_by VARCHAR(255)
);
````

### 2. **m\_portfolio（ポートフォリオ）**

ユーザーごとのポートフォリオを管理します。

```sql
CREATE TABLE m_portfolio (
  id SERIAL PRIMARY KEY,
  user_id INTEGER REFERENCES m_user(id) ON DELETE CASCADE,
  name VARCHAR(255) NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  created_by VARCHAR(255),
  updated_by VARCHAR(255)
);
```

### 3. **m\_asset（資産）**

資産の種類を管理します。株式、仮想通貨、不動産など、異なるタイプの資産を扱います。

```sql
CREATE TABLE m_asset (
  id SERIAL PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  type VARCHAR(50) NOT NULL,  -- 例: Stock, Cryptocurrency, Bond, RealEstate
  ticker_symbol VARCHAR(50),  -- 株式のティッカーシンボル
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  created_by VARCHAR(255),
  updated_by VARCHAR(255)
);
```

### 4. **t\_portfolio\_asset（ポートフォリオと資産の関連）**

ユーザーのポートフォリオとそのポートフォリオに含まれる資産を管理する中間テーブル。

```sql
CREATE TABLE t_portfolio_asset (
  id SERIAL PRIMARY KEY,
  portfolio_id INTEGER REFERENCES m_portfolio(id) ON DELETE CASCADE,
  asset_id INTEGER REFERENCES m_asset(id) ON DELETE CASCADE,
  quantity NUMERIC NOT NULL,
  purchase_price NUMERIC NOT NULL,
  purchase_date DATE NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  created_by VARCHAR(255),
  updated_by VARCHAR(255)
);
```

### 5. **t\_transaction（取引）**

ユーザーが行った取引履歴（売買など）を管理します。

```sql
CREATE TABLE t_transaction (
  id SERIAL PRIMARY KEY,
  portfolio_asset_id INTEGER REFERENCES t_portfolio_asset(id) ON DELETE CASCADE,
  transaction_type VARCHAR(10) CHECK (transaction_type IN ('BUY', 'SELL')) NOT NULL,
  quantity NUMERIC NOT NULL,
  price NUMERIC NOT NULL,
  transaction_date TIMESTAMPTZ NOT NULL,
  fees NUMERIC,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  created_by VARCHAR(255),
  updated_by VARCHAR(255)
);
```

### 6. **m\_market\_data（市場データ）**

資産のリアルタイムまたは過去の市場データを格納します。

```sql
CREATE TABLE m_market_data (
  id SERIAL PRIMARY KEY,
  asset_id INTEGER REFERENCES m_asset(id) ON DELETE CASCADE,
  price NUMERIC NOT NULL,
  volume NUMERIC,
  market_cap NUMERIC,
  date TIMESTAMPTZ NOT NULL,
  created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
  created_by VARCHAR(255),
  updated_by VARCHAR(255)
);
```

## テーブルリレーション

* **m\_user → m\_portfolio**: 1対多（1人のユーザーが複数のポートフォリオを持つ）
* **m\_portfolio → t\_portfolio\_asset**: 1対多（1つのポートフォリオに複数の資産が含まれる）
* **m\_asset → t\_portfolio\_asset**: 多対多（1つの資産が複数のポートフォリオに含まれる、逆も同じ）
* **t\_portfolio\_asset → t\_transaction**: 1対多（1つのポートフォリオ資産が複数の取引履歴を持つ）
* **m\_asset → m\_market\_data**: 1対多（1つの資産に対して複数の市場データが関連する）

## 監査カラム

各テーブルには、以下の監査用カラムを追加しています：

* **created\_by**: データを作成したユーザーやシステム（システムであれば"system"など）
* **updated\_by**: 最後にデータを更新したユーザーやシステム
* **created\_at**: データが作成された日時
* **updated\_at**: データが最後に更新された日時

## インデックスの設計

* **m\_user.username**, **m\_user.email**: ユーザーを一意に識別するためのインデックス
* **m\_portfolio.user\_id**: ユーザーごとのポートフォリオを効率よく取得するためのインデックス
* **t\_transaction.transaction\_date**: 取引履歴を日付順に検索するためのインデックス
* **m\_market\_data.date**: 市場データの日時を効率よく検索するためのインデックス

---

このDB設計は、投資家が自分のポートフォリオを管理し、取引履歴や市場データを追跡するための基盤を提供します。データベースのパフォーマンスと整合性を考慮しつつ、将来的な拡張性も視野に入れた設計となっています。

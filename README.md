# InvestTrack

InvestTrackは、個人投資家向けのポートフォリオ管理および投資分析ツールです。ユーザーが簡単に投資状況をトラッキングし、利益やリスクを分析できるようサポートします。株式、仮想通貨、その他の資産クラスに対応しており、パフォーマンスを視覚的に管理できます。

## Features

- **ポートフォリオ管理**: 複数の資産クラスをトラッキング（株式、仮想通貨、債券、不動産など）
- **パフォーマンス分析**: ROI（投資利益率）、シャープレシオ、最大ドローダウンなどの指標を表示
- **リアルタイムデータ**: 外部APIを利用して、株価や仮想通貨の価格をリアルタイムで取得
- **リスク管理**: ポートフォリオのリスクを評価し、分散投資をサポート
- **売買タイミングの提案**: 過去のデータを基に、買い時・売り時を予測（AIモデルを活用）
- **税金計算サポート**: 投資利益に対する税金計算をサポート

## Tech Stack

- **フロントエンド**: React
- **バックエンド**: Spring Boot (API)
- **データベース**: PostgreSQL
- **ORM**: JPA (Java Persistence API)
- **外部API**: Yahoo Finance, Alpha Vantage, CoinGecko など
- **Java**: 21

## Getting Started

### 必要なツール

- JDK 21以上（バックエンド用）
- Node.js 14以上（フロントエンド用）
- PostgreSQL 13以上
- Git

### インストール手順

#### 1. リポジトリのクローン

```bash
git clone https://github.com/your-username/InvestTrack.git
cd InvestTrack
````

#### 2. フロントエンドのセットアップ

フロントエンドのディレクトリに移動し、依存関係をインストールします。

```bash
cd frontend
npm install
```

#### 3. バックエンドのセットアップ

バックエンドのディレクトリに移動し、Gradleの依存関係をインストールします。

```bash
cd backend
./gradlew build
```

#### 4. データベースの設定

PostgreSQLをインストールし、データベースを作成します。`src/main/resources/application.yaml`ファイルを編集して、データベースの接続設定を行ってください。

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/investtrack
    username: your-username
    password: your-password
    driver-class-name: org.postgresql.Driver
  jpa:
    hibernate:
      ddl-auto: update
    properties:
      hibernate:
        format_sql: true
  jackson:
    serialization:
      WRITE_DATES_AS_TIMESTAMPS: false
```

#### 5. アプリケーションの起動

フロントエンドとバックエンドをそれぞれ起動します。

```bash
# フロントエンド
npm start

# バックエンド
./gradlew bootRun
```

## Usage

アプリケーションを起動後、ブラウザで `http://localhost:3000` にアクセスして、投資状況を管理・分析できます。

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Contributing

1. フォークしてクローンします。
2. 新しいブランチを作成します（`git checkout -b feature-name`）。
3. 変更を加え、コミットします（`git commit -am 'Add new feature'`）。
4. プッシュします（`git push origin feature-name`）。
5. プルリクエストを作成します。

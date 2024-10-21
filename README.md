#　 Laravel Sail

Laravel Sail は、Laravel フレームワークの公式開発環境です。Sail は、Docker を使用して Laravel アプリケーションの開発環境を簡単にセットアップおよび管理するための軽量なコマンドラインインターフェース（CLI）を提供します。以下に、Laravel Sail の主な特徴と使用方法について説明します。

### **主な特徴**

1. **Docker ベース**:
    - Sail は Docker を使用して、Laravel アプリケーションの開発環境をコンテナ化します。これにより、開発環境が一貫しており、他の開発者と共有しやすくなります。
2. **簡単なセットアップ**:
    - Sail を使用すると、数行のコマンドで Laravel の開発環境をセットアップできます。
3. **複数のサービス**:
    - Sail は、MySQL、Redis、MailHog、MeiliSearch など、さまざまなサービスをサポートしています。これにより、複雑な開発環境を簡単に構築できます。

### **使用方法**

### 1. Laravel プロジェクトの作成

まず、新しい Laravel プロジェクトを作成します。

```bash
curl -s "https://laravel.build/example-app" | bash
cd example-app
```

### **Laravel Sail のセットアップ手順**

1. **Laravel Sail のインストール**:

    ```bash
    composer require laravel/sail --dev
    ```

2. **Sail のインストール**: Sail のインストールコマンドを実行します。これにより、Sail の設定ファイルが生成されます。

    ```bash
    php artisan sail:install
    ```

3. **Docker コンテナの起動**: Sail を使用して Docker コンテナを起動します。

    ```bash
    ./vendor/bin/sail up
    ```

### 2. Sail の起動

プロジェクトディレクトリに移動し、Sail を起動します。

```bash
./vendor/bin/sail up
```

このコマンドは、Docker コンテナを起動し、Laravel アプリケーションを実行します。

### 3. Sail コマンドの使用

Sail を使用して、さまざまな開発タスクを実行できます。以下は、いくつかの例です。

-   **アプリケーションの起動**:

```bash
./vendor/bin/sail up
```

-   **アプリケーションの停止**:

```bash
./vendor/bin/sail down
```

-   **Artisan コマンドの実行**:

```bash
./vendor/bin/sail artisan migrate
```

-   **Composer コマンドの実行**:

```bash
./vendor/bin/sail composer install
```

-   **npm コマンドの実行**:

```bash
./vendor/bin/sail npm install
```

## エイリアス登録

`./vendor/bin/sail` を打つのはめんどくさいので shell の設定ファイルにエイリアスを保存しておく。

```bash
echo "alias sail='sh $([ -f sail ] && echo sail || echo vendor/bin/sail)'" >> ~/.bash_profile
```

```bash
source ~/.bash_profile
```

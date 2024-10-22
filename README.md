#　 Laravel Sail

## Laravel Sail について

Laravel Sail は、Laravel フレームワークの公式開発環境です。Sail は、Docker を使用して Laravel アプリケーションの開発環境を簡単にセットアップおよび管理するための軽量なコマンドラインインターフェース（CLI）を提供します。以下に、Laravel Sail の主な特徴と使用方法について説明します。

## Laravel Sail を使用するメリット

1. **OS に依存せず開発環境を構築できる**
    - Sail は Docker を使用して、Laravel アプリケーションの開発環境をコンテナ化する。これにより OS に依存しない開発環境が構築できる。
2. **開発環境の構築が簡単**
    - Sail を使用すると、数行のコマンドで Laravel の開発環境をセットアップできる。MySQL、Redis、MailHog などを用いた複雑な開発環境でも簡単に構築可能。
3. **バージョンの異なるプロジェクトの管理が簡単**
    - 例えば Laravel のバージョンは PHP のバージョンに依存しているが、プロジェクトごとの環境を docker-compose.yml ファイルで設定することでバージョンを簡単に切り替えることができる。

![Sail Containers](./public//images/sail_containers_image.png)
Laravel Sail によって Docker を使用し、プロジェクトを作成すると下図のような構成になる。

##　使用方法

### 1. Laravel プロジェクトの作成

下記コマンドで新しい Laravel プロジェクトを作成し Laravel Sail がセットアップされる。

```bash
curl -s "https://laravel.build/example-app" | bash
cd example-app
```

これにより、Laravel のプロジェクトファイルが作成される。
また、開発環境に関しては生成された docker-compose.yml ファイルに記述されている。

#### with クエリでサービスを指定する

PHP のバージョンを指定したい場合や、MySQL ではなく PostgreSQL を使用したい場合は
プロジェクト生成時に実行するコマンドに、以下のように `with=<service>,<service>,...` を指定する。

```bash
curl -s "https://laravel.build/example-app?php=81&with=pgsql,redis" | bash
```

### 2. Sail の起動

プロジェクトディレクトリに移動し、Sail を起動する。

```bash
./vendor/bin/sail up -d
```

このコマンドは Docker コンテナを起動し Laravel アプリケーションを実行する。

#### ※エイリアス登録

`./vendor/bin/sail` を打つのはめんどくさいので shell の設定ファイルに `sail` としてエイリアスを保存しておく。

下記コマンドで自分が使用している Shell のパスを確認

```bash
echo $SHELL
```

例 `/bin/zsh`

**設定ファイルの編集**

-   zsh シェルの場合

Shell 設定ファイルは ~/.zshrc
エイリアスを追加するには、以下のコマンドを実行する

```bash
echo "alias sail='sh $([ -f sail ] && echo sail || echo vendor/bin/sail)'" >> ~/.zshrc
source ~/.zshrc
```

-   bash シェルの場合:

Shell 設定ファイルは ~/.bash_profile または ~/.bashrc
エイリアスを追加するには、以下のコマンドを実行する

```bash
echo "alias sail='sh $([ -f sail ] && echo sail || echo vendor/bin/sail)'" >> ~/.bash_profile
source ~/.bash_profile
```

上記設定により、省略形で sail コマンドが実行できる。

```bash
sail up -d
```

### 3. Sail コマンドの使用

Sail を使用して、さまざまな開発タスクを実行できます。以下は、いくつかの例です。

-   **アプリケーションの起動**:

```bash
sail up -d
```

-   **アプリケーションの停止**:

```bash
sail down
```

-   **Artisan コマンドの実行**:

```bash
sail artisan migrate
```

-   **Composer コマンドの実行**:

```bash
sail composer install
```

-   **npm コマンドの実行**:

```bash
sail npm install
```

### 4. サービスの変更

プロジェクト作成後でも Docker の設定ファイルである、docker-compose.yml ファイルを修正することでサービスの変更が可能になる。
ここでは PHP のバージョン変更と、使用 DB の変更を行う。

#### PHP のバージョン変更

まずは、現在の PHP のバージョンを確認

```bash
sail php -v
PHP 8.3.12 (cli) (built: Sep 27 2024 03:53:05) (NTS)
```

次に docker-compose.yml ファイルに記述されている開発環境情報を修正する。

```php
services:
    laravel.test:
        build:
            context: "./vendor/laravel/sail/runtimes/8.3"
            dockerfile: Dockerfile
            args:
                WWWGROUP: "${WWWGROUP}"
        image: "sail-8.3/app"
```

`build context` と `image` を `8.1` に修正。

```php
services:
    laravel.test:
        build:
            context: "./vendor/laravel/sail/runtimes/8.1"
            dockerfile: Dockerfile
            args:
                WWWGROUP: "${WWWGROUP}"
        image: "sail-8.1/app"
```

docker-compose.yml を修正したら、一度アプリケーションを停止する。

```bash
sail down
```

次に変更を反映するために Docker コンテナを再ビルドする。

```bash
sail build --no-cache
```

アプリケーションの再起動。

```bash
sail up -d
```

PHP のバージョンが変更できたか確認

```bash
sail php -v
PHP 8.1.30 (cli) (built: Sep 27 2024 04:07:29) (NTS)
```

PHP 8.3 から PHP 8.1 バージョンを変更できた。

#### 使用データベースの変更

MySQL で作成ずみのプロジェクトを PostgreSQL に変更する。

Sail のインストールコマンドを実行

```bash
sail php artisan sail:install
```

`pgsql` を選択

```bash
 ┌ Which services would you like to install? ───────────────────┐
 │   ◼ mysql                                                  ┃ │
 │ › ◻ pgsql                                                  │ │
 │   ◻ mariadb                                                │ │
 │   ◻ redis                                                  │ │
 │   ◻ memcached                                              │ │
 └────────────────────────────────────────────────── 1 selected ┘
  Use the space bar to select options.
```

docker-compose.yml を確認し、pgsql に関する記述が追記されていることを確認。

```php
services:
    laravel.test:
...
        depends_on:
            - mysql
            - redis
            - meilisearch
            - mailpit
            - selenium
            - pgsql
...
        pgsql:
            image: 'postgres:17'
            ports:
                - '${FORWARD_DB_PORT:-5432}:5432'
            environment:
                PGPASSWORD: '${DB_PASSWORD:-secret}'
                POSTGRES_DB: '${DB_DATABASE}'
                POSTGRES_USER: '${DB_USERNAME}'
                POSTGRES_PASSWORD: '${DB_PASSWORD:-secret}'
            volumes:
                - 'sail-pgsql:/var/lib/postgresql/data'
                - './vendor/laravel/sail/database/pgsql/create-testing-database.sql:/docker-entrypoint-initdb.d/10-create-testing-database.sql'
            networks:
                - sail
            healthcheck:
                test:
                    - CMD
                    - pg_isready
                    - '-q'
                    - '-d'
                    - '${DB_DATABASE}'
                    - '-U'
                    - '${DB_USERNAME}'
                retries: 3
                timeout: 5s
```

次にプロジェクト配下の .env を開き、`DB_CONNECTION=pgsql`
`DB_HOST=pgsql` になっていることを確認。
もし変更されていない場合は、手動で変更。

```.env
DB_CONNECTION=pgsql
DB_HOST=pgsql
DB_PORT=5432
DB_DATABASE=laravel
DB_USERNAME=sail
DB_PASSWORD=******
```

docker-compose.yml と .env の変更を確認したら、一度アプリケーションを停止する。

```bash
sail down
```

次に変更を反映するために Docker コンテナを再ビルドする。

```bash
sail build --no-cache
```

アプリケーションの再起動。

```bash
sail up -d
```

migration ファイルを実行。

```bash
sail artisan migrate

INFO  Preparing database.

Creating migration table ................................ 6.00ms DONE

INFO  Running migrations.

0001_01_01_000000_create_users_table .................... 5.74ms DONE
0001_01_01_000001_create_cache_table .................... 1.45ms DONE
0001_01_01_000002_create_jobs_table ..................... 3.44ms DONE
```

**Docker の中で Shell を起動し、pgsql を確認する**

現在実行中の Docker コンテナの一覧を表示

```bash
sail ps
```

```bash
NAME                                IMAGE                          COMMAND                  SERVICE
laravel-sail-setup-laravel.test-1   sail-8.3/app                   "start-container"        laravel.test
laravel-sail-setup-mailpit-1        axllent/mailpit:latest         "/mailpit"               mailpit

```

アプリケーションのコンテナ名を確認し Docker 内のアプリケーションの bash を起動する

```bash
docker exec -it laravel-sail-setup-laravel.test-1 bash
```

pgsql と接続

```bash
psql -U sail -h pgsql laravel
```

パスワードが求められるので、.env に記載してある DB_PASSWORD を入力。

```bash
Password for user sail:******
```

pgsql 内のテーブルを確認。

```bash
laravel=# \dt
               List of relations
 Schema |         Name          | Type  | Owner
--------+-----------------------+-------+-------
 public | cache                 | table | sail
 public | cache_locks           | table | sail
 public | failed_jobs           | table | sail
 public | job_batches           | table | sail
 public | jobs                  | table | sail
 public | migrations            | table | sail
 public | password_reset_tokens | table | sail
 public | sessions              | table | sail
 public | users                 | table | sail
(9 rows)
```

このように、プロジェクト作成後でもの MySQL から PostgreSQL に変更することができる。

## 最後に

この記事を通じて、Laravel Sail を使用して効率的に開発環境を構築し、管理する方法を理解できるようになります。プロジェクトの要件に応じて、適切なサービスを選択し、設定をカスタマイズすることで、より柔軟で強力な開発環境を実現できます。

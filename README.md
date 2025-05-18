# デプロイ環境について
## フロント
https://meshiuchi-front.vercel.app/
## バック
https://meshiuchi-back.fly.dev/

# 環境構築手順について

## フロント側（Nuxt.js）の環境構築手順について
ルートディレクトリで`docker compose run --rm front yarn create nuxt-app .`を実行
- `Override its contents`を選択 <br>
![image](https://github.com/user-attachments/assets/5b87f81b-bb21-4338-9d2c-31fe6b6a06f4) 
- `yarn`選択 <br>
![image](https://github.com/user-attachments/assets/b6255bf4-324c-4efd-b607-fd1e64d0fb7d) 
- No選択 <br>
![image](https://github.com/user-attachments/assets/bdb9885a-3ca0-4264-a31a-c16b67784f9d)
- 色々聞かれるが全てチェックをしてOK（スペースキー押下でチェック） <br>
![image](https://github.com/user-attachments/assets/037a0610-44b5-4553-ae77-76776784ce2e)
- `http://localhost:8000/`にアクセスして以下のNuxt.jsの初期画面が表示されたら成功 <br>
- <img width="1439" alt="image" src="https://github.com/user-attachments/assets/ca04e66a-e7d2-483c-8303-fea7618ceba6" />

## バック側（RailsAPI）の環境構築手順について
### コマンド実行
- `bakc`ディレクトリで`docker compose run --rm --no-deps back bundle exec rails new . --api --database=postgresql`コマンド実行
### 国際化とタイムゾーンの設定
- `config/application.rb`に以下のコードを追加
```ruby
require_relative "boot"

require "rails/all"

Bundler.require(*Rails.groups)

module App
  class Application < Rails::Application
    config.load_defaults 8.0
    config.autoload_lib(ignore: %w[assets tasks])
    config.api_only = true
    config.time_zone = "Tokyo"
    config.active_record.default_timezone = :local
    config.i18n.default_locale = :ja
  end
end
```
### ホスト機能設定
- `config/environments/development.rb`にconfig.hosts << "api"を追加
```ruby
Rails.application.configure do

  config.hosts << "api"
end
```
### データベースのセットアップ
- `/config/database.yml`に以下のコードを追加
```ruby
default: &default
  adapter: postgresql
  encoding: unicode
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>
  username: user
  password: password
  host: db
```
### 再度、コマンド実行
- `docker compose run --rm back bundle install`実行
- `docker compose build back` 実行
- `docker compose run --rm back bundle exec rails db:create`実行
- http://0.0.0.0:3000/ にアクセスしてRailsの以下の画面が表示されれば完了
<img width="737" alt="image" src="https://github.com/user-attachments/assets/ac2ca4c1-d36d-4544-b467-097a66e7f000" />

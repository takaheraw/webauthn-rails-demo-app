# WebAuthn Rails Demo App

forked from [webauthn-rails-demo-app](https://github.com/cedarcode/webauthn-rails-demo-app)

#### Setup

```
$ mutagen-compose up -d
$ mutagen-compose exec app bash
app# cp .env.example .env
app# bundle install
app# yarn install
app# bundle exec rake db:setup
app# bundle exec rails s -b 0.0.0.0
```

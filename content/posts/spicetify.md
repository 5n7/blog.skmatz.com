+++
title = "Spicetify で Spotify の見た目をカスタマイズする"
date = "2021-01-03"
author = "Shunta Komatsu"
cover = "img/spicetify/spicetify-01.png"
description = "Spotify クライアントの見た目を編集する Spicetify の紹介をします。"
+++

## Spicetify とは

[![khanhas/spicetify-cli](https://gh-card.dev/repos/khanhas/spicetify-cli.svg)](https://github.com/khanhas/spicetify-cli)

Spicetify は、Linux、macOS、Windows で動く、Spotify クライアントの見た目をカスタマイズできるアプリケーションです。
これらの OS 用の Spotify クライアント (おそらく) は Electron 製なので、CSS や JS を編集して UI を変更するインターフェイスを提供してくれます。

## インストール

[wiki](https://github.com/khanhas/spicetify-cli/wiki/Installation) によくまとまっているので参照してください。
Spotify クライアントのインストール方法によっては、

```bash
sudo chmod a+rw /opt/spotify
sudo chmod a+rw /opt/spotify/Apps -R
```

などで編集権限を与える必要があるので注意が必要です。

## 使い方

まずは、

```bash
spicetify
```

を実行し、設定ファイルを生成します。
OS 別に以下のパスに設定ファイルが作成されているはずです。

| OS      | path                                  |
| ------- | ------------------------------------- |
| Linux   | `~/.config/spicetify/config.ini`      |
| macOS   | `~/spicetify_data/config.ini`         |
| Windows | `%userprofile%\.spicetify\config.ini` |

その後、

```bash
spicetify backup apply
```

とすることでクライアントにテーマの反映を行うことができます。

## テーマの設定

テーマの作成は正直面倒なので配布されているものを使います。

[![morpheusthewhite/spicetify-themes](https://gh-card.dev/repos/morpheusthewhite/spicetify-themes.svg)](https://github.com/morpheusthewhite/spicetify-themes)

たくさんのテーマが収録されており、好きなものを選んでダウンロードします。
余談ですが、[Refined GitHub](https://chrome.google.com/webstore/detail/refined-github/hlepfoohegkhhmjieoechaddaejaokhf) とかを入れておくとディレクトリ単位でダウンロードできます。

大抵のテーマは `color.ini` と `user.css` が収録されているので、

```text
~/.config/spicetify
└── Themes
    └── Dark
        ├── color.ini
        ├── README.md
        ├── screenshot1.jpg
        ├── screenshot2.jpg
        └── user.css
```

のようなディレクトリ構造になるように配置します (これは Linux で [Dark](https://github.com/morpheusthewhite/spicetify-themes/tree/master/Dark) を使っている例)。

配置が完了したら、`config.ini` を次のように編集します。

```ini
# ~/.config/spicetify/config.ini
[Setting]
current_theme = Dark
color_scheme  = Base
```

ここで、`current_theme` は `Themes` 下のテーマのディレクトリ名、`color_scheme` はテーマの `color.ini` のテーマ名です。

これが完了したら、

```bash
spicetify update
```

として Spotify を再起動すればテーマが反映されているはずです。

ちなみに、この記事の一番上に貼ってあるスクリーンショットでは [DribbblishDynamic](https://github.com/morpheusthewhite/spicetify-themes/blob/master/DribbblishDynamic) というテーマを使っています。
このテーマは `color.ini` と `user.css` に加えて Spicetify の拡張機能として動的に色を変更するスクリプトが含まれていて、とてもオシャレです。

![spicetify-01](/img/spicetify/spicetify-01.png)
![spicetify-02](/img/spicetify/spicetify-02.png)
![spicetify-03](/img/spicetify/spicetify-03.png)

---

アプリケーションや拡張機能については [wiki](https://github.com/khanhas/spicetify-cli/wiki) を参照してください。
個人的にはそこまで利用するメリットを感じていないので使っていないのですが、まあ暇なときに追記するかもしれません。

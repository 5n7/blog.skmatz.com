+++
title = "dotfiles には dotbot を添えて"
date = "2021-01-01"
author = "Shunta Komatsu"
description = "まさか手動でシンボリックリンクを貼っていませんよね？"
+++

## dotfiles とは

この記事を読むような人は知っている人が多そうですが、要するにアプリケーションの設定ファイルです。
よく見るのは Vim や Zsh の設定ですが、Arch Linux 系の OS やタイル型ウィンドウマネージャを使っている人はかなりの量の設定ファイルを管理していると思います。

適切に dotfiles を管理することで、新しいマシンを購入したときでも一瞬で同じ作業環境を構成できます。
一般的？には、dotfiles 用のリポジトリを作ってすべての設定ファイルを集約させた後、手動または自作スクリプトでシンボリックリンクを貼る方法が紹介されています。

この作業を含め、アプリケーションのインストールなど、環境構築に必要な作業を自動化してくれるのが dotbot です。

## dotbot とは

[![anishathalye/dotbot](https://gh-card.dev/repos/anishathalye/dotbot.svg)](https://github.com/anishathalye/dotbot)

dotbot は dotfiles の管理を簡単にしてくれる Python 製のツールです。
Python 製なのでランタイムに Python を必要としますが、Linux 系なら基本的にシステムにインストールされているはずなので、とくに気にする必要はないです。

## インストール

PyPI に公開されているので、

```bash
pip install [--user] dotbot
```

とかでもインストールできますが、依存を増やさないためにもサブモジュールとして dotfiles のためのリポジトリ内に追加する方法が推奨されています。

```bash
mkdir dotfiles
cd dotfiles
git init
git submodule add https://github.com/anishathalye/dotbot
cp dotbot/tools/git-submodule/install .
touch install.conf.yaml
```

5 行目でコピーしているのは dotbot をいい感じに実行するための shell script です。

以降は 6 行目で作成した `install.conf.yaml` を編集し、

```bash
./install
```

と実行する流れです。

## 設定

dotbot でできることは、

- link: シンボリックリンクの作成
- create: ディレクトリの作成
- shell: シェルの実行
- clean: シンボリックリンクの削除

です。
これに加えてプラグイン機構を備えているので、任意のプラグインを追加することで apt や pacman でアプリケーションをインストールするといったこともできます。

### Link

link ディレクティブはシンボリックリンクを作成します。

```yaml
- link:
    ~/.config/i3: i3
    ~/.zshrc: .zshrc
```

このような感じで、key に登録名、value に実体へのパスを列挙します。
実体へのパスがディレクトリの場合でもファイルの場合でも意図通りにリンクが貼られます。

また、各オプションを利用する場合は次のように書きます。

```yaml
- link:
    ~/.config/i3:
      path: i3
      relink: true
```

`path` がさきほどの value に該当し、その他のオプションを並べます。
いくつかオプションがありますが、`create` と `relink` は常に `true` でいいような気がします (後に紹介する defaults ディレクティブで設定することをオススメします)。

### Create

create ディレクティブはディレクトリを作成します。
次のように作成するディレクトリへのパスを列挙します。

```yaml
- create:
    - ~/sandbox
    - ~/workspace
```

### Shell

shell ディレクティブはシェルを実行します。
コマンドのみ列挙する場合は次のように書きます。

```yaml
- shell:
    - chsh -s $(which zsh)
```

標準入力や標準出力を要する場合は次のように書きます。

```yaml
- shell:
    - command: chsh -s $(which zsh)
      stdin: true
      stdout: true
```

### Clean

clean ディレクティブはリンクが切れているシンボリックリンクの削除を行います。

```yaml
- clean: ["~"]
```

オプションを利用する場合は次のように書きます。

```yaml
- clean:
    ~/:
      force: true
    ~/.config:
      recursive: true
```

### Defaults

defaults ディレクティブは規定の設定を上書きします。
defaults ディレクティブはファイルのどこに何回書いても良いですが、書いた場所以降の設定が変更されることに注意が必要です。

```yaml
- defaults:
    link:
      create: true
      relink: true
```

多用することはないと思いますが、上の 2 つの項目はデフォルトに設定しておくことを推奨します。

## プラグイン

dotbot はプラグイン機構を備えており、OSS で作成されたプラグインなどをサブモジュールとして追加しておくことで、さまざまなことができるようになります。

[wiki](https://github.com/anishathalye/dotbot/wiki/Plugins) に利用可能なプラグインの表が記載されています。

たとえば [oxson/dotbot-yay](https://github.com/oxson/dotbot-yay) は次のように書くことで、Arch Linux の AUR パッケージマネージャである [Yay](https://github.com/Jguer/yay) を使って列挙したアプリケーションをインストールしてくれます。

```yaml
- yay:
    - alacritty
    - tmux
    - zsh
```

## コマンドライン引数

冒頭にも書いたように、基本的には `install.conf.yaml` を編集したら、

```bash
./install
```

と実行するだけですが、次のようにオプションを渡すことができます。

```bash
./install --only link
```

`--only` オプションは実行するディレクティブを限定できます。
これにより、新しいシンボリックリンクを作成したいたけなのにパッケージのインストーラが走るみたいなことを防ぐことができます。

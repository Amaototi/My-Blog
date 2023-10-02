---
title: Arch LinuxのKDEでX11からWaylandに切り替えてみた
description: 
slug: ArchLinux-Wayland
image: Wayland_Logo.png
date: 2023-05-09T15:17:12+09:00
categories:
    - 技術
tags: [ArchLinux, Wyaland, KDE, 備忘録]
---

## Arch Linuxでwaylandを使いたい！

という思いつきで色々自分で調べながら試したので、その備忘録です。  

## ⚠注意⚠

今回はNVIDIAのGPUを使っていません。NVIDIAのGPUを使う場合はまた違う操作が必要になるので、[ArchWiki](https://wiki.archlinux.jp/index.php/Wayland#NVIDIA_.E3.83.89.E3.83.A9.E3.82.A4.E3.83.90)で確認してください。

### 環境

| 名称 | 構成 |
| - | - |
| CPU | Ryzen 5 5500U |
| GPU | AMD Radeon Graphics |
| OS | Arch Linux x86_64 |
| デスクトップ環境 | Plasma |
| ディスプレイマネージャー | sddm |

## パッケージのインストール

### Plasmaのwaylandパッケージをインストール

PlasmaでWaylandを使用するためには追加でplasma-wayland-sessionというパッケージをインストールする必要があります。インストールしていない場合はインストールしてください。

```sh
$ pacman -S plasma-wayland-session
```

### sddmの切り替え

通常のsddmパッケージではwaylandを動かすことができないので、AURパッケージであるsddm-gitに切り替えます。

```sh
$ pacman -S sddm-git
looking for conflicting packages...
:: sddm-git and sddm are in conflict. Remove sddm? [y/N] y # sddmをsddm-gitに置き換える
...
```

### コンポジタ

コンポジタとして、KDEのKwinをインストールします。

```sh
$ pacman -S KWin
```

## 設定ファイルの作成

waylandで動かすために新たに設定ファイルを`/etc/sddm.conf.d/`の下に作成します。ファイル名は`/etc/sddm.conf.d/10-wayland.conf`としてください。また、ディレクトリが存在しない場合は作成してください。

```conf
[General]
DisplayServer=wayland
GreeterEnvironment=QT_WAYLAND_SHELL_INTEGRATION=layer-shell

[Wayland]
CompositorCommand=kwin_wayland --no-lockscreen
```

ファイルを保存したら再起動します。

## Waylandでの起動

ログイン画面になったら、画面左上にあるプルダウンリストからWaylandを選択しそのままログインします。するとWaylandで起動することができます。

### デバッグツール

KWinを使用している場合、以下のコマンドを実行することでどのアプリケーションがWaylandで実行されているかの確認を行えます。
```sh
$ qdbus org.kde.KWin /KWin org.kde.KWin.showDebugConsole
```

## 最後に

意外と簡単にWaylandへの移行が行えていい感じでした。

## 参考文献

* [Wayland - ArchWiki](https://wiki.archlinux.jp/index.php/Wayland)
* [KDE - ArchWiki](https://wiki.archlinux.jp/index.php/KDE)
* [SDDM -ArchWiki](https://wiki.archlinux.jp/index.php/SDDM)
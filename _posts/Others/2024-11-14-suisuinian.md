---
title: suisuinian
---
# 想做一个进度条手机软件！
## 核心
+ 能设置ST ED 日
+ 使用当前日期 date.NOW
+ 条状显示
+ 桌面插件
   + 背景透明
   + 字白色
   + 可显示文字（标题/LABLE）
   + 可显示天数（eg. 经过日/倒数日）

+ 主界面
+ create/编辑界面

## tec key words

｜Android Studio｜Kotlin｜

## 开发步骤（以Android为例）

+ UI设计: 使用XML布局文件设计进度条和日期输入框。
+ 数据存储: 使用SharedPreferences或SQLite数据库保存用户设置的开始日期和结束日期。
+ 计算进度: 根据当前日期和设置的开始、结束日期，计算进度并更新进度条。
+ 界面更新: 使用Handler或LiveData等机制，实时更新进度条。


## Androidのテンプレートやフレームワーク
+ Jetpack Compose: Googleが提供する新しいUIツールキットです。Declarativeな構文でUIを構築でき、少ないコードで複雑なUIを実現できます。
  + A Modern Toolkit for Android UI Development
+ MVVM (Model-View-ViewModel): アプリの構造を整理し、テストの容易性や保守性を高めるためのアーキテクチャパターンです。
+ Room: SQLiteデータベースを扱うための抽象レイヤーを提供します。
+ WorkManager: バックグラウンドタスクをスケジュールするためのライブラリです。


## 学習内容
+ Android Studioのインストールと設定: Androidアプリ開発の統合開発環境であるAndroid Studioのインストール方法や、プロジェクトの作成方法などを学びます。
+ Kotlinの基礎: Androidアプリ開発で主流となっているプログラミング言語Kotlinの文法や特徴を学びます。
+ レイアウトの作成: XMLを使用して、アプリの画面デザインを作成します。
+ ActivityとFragment: Androidアプリの画面の構成要素であるActivityとFragmentについて学びます。
+ データの保存: SharedPreferencesやSQLiteデータベースなど、アプリ内でデータを保存する方法を学びます。
+ ネットワーク通信: サーバーとの通信を行い、データをやり取りする方法を学びます。


## coursera 初心者におすすめのコースを選ぶポイント
+ Kotlinの基礎: Androidアプリ開発ではKotlinが主流となっています。Kotlinの基礎をしっかりと学べるコースを選びましょう。
+ Android Studioの使い方: Android Studioは、Androidアプリ開発の統合開発環境です。Android Studioの使い方を習得しましょう。
+ UI/UXデザイン: ユーザーインターフェースのデザインの基礎を学ぶことで、より魅力的なアプリを作成することができます。
+ 実践的なプロジェクト: 実際のアプリを作成する経験を積むことで、より深くAndroid開発を理解することができます。


## coursera
+ 1. GoogleのAndroid開発者コース
Googleが提供する公式のAndroid開発者コースです。Kotlinの基礎から、最新のAndroid開発のトレンドまでを網羅しています

https://www.coursera.org/professional-certificates/meta-android-developer


## [Android App Development Tutorial for Beginners - Your First App](https://youtu.be/FjrKMcnKahY?si=2nN1pKCe15HXJOVR)

<iframe width="560" height="315" src="https://www.youtube.com/embed/FjrKMcnKahY?si=LLXIRecTOa7eyBpE" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>
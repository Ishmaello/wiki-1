---
id: backup-restore
title: ノードインスタンスのバックアップ／復元
description: "Polygon Edgeノードインスタンスをバックアップおよび復元する方法。"
keywords:
  - docs
  - polygon
  - edge
  - instance
  - restore
  - directory
  - node
---

## 概要 {#overview}

このガイドでは、Polygon Edgeノードインスタンスをバックアップおよび復元する方法について詳しく説明します。
基本フォルダとその内容、およびバックアップと復元を正常に実行するために重要なファイルについて説明します。

## 基本フォルダ {#base-folders}

Polygon Edgeは、ストレージエンジンとしてLevelDBを活用しています。
Polygon Edgeノードを起動すると、指定した作業ディレクトリに次のサブフォルダが作成されます：
* **blockchain** - ブロックチェーンデータを保存します
* **trie** - Merkleトライ（ワールドステートデータ）を保存します
* **keystore** - クライアントの秘密鍵を保存します。これには、libp2p秘密鍵とシーリング／バリデータ秘密鍵が含まれます
* **consensus** - 作業中にクライアントに必要なすべてのコンセンサス情報を保存します。現在のところ、ノードの*バリデータ秘密鍵*が保存されます

Polygon Edgeインスタンスをスムーズに実行するには、これらのフォルダを維持することが重要です。

## 実行中のノードからバックアップを作成し、新しいノードに復元を行います {#create-backup-from-a-running-node-and-restore-for-new-node}

このセクションでは、実行中のノードでブロックチェーンのアーカイブデータを作成し、別のインスタンスに復元する手順について説明します。

### バックアップ {#backup}

`backup`コマンドは、gRPCによって実行ノードからブロックを取得し、アーカイブファイルを生成します。コマンドに`--from`と`--to`が指定されていない場合、このコマンドはブロックをジェネシスから最新に取り込みます。

```bash
$ polygon-edge backup --grpc-address 127.0.0.1:9632 --out backup.dat [--from 0x0] [--to 0x100]
```

### 復元 {#restore}

サーバは、`--restore`フラグで始まるときに、スタート時にアーカイブからブロックをインポートします。新しいノードの鍵があることを確認してください。鍵のインポートまたは生成の詳細については、[シークレットマネージャセクション](/docs/edge/configuration/secret-managers/set-up-aws-ssm)を参照してください。

```bash
$ polygon-edge server --restore archive.dat
```

## データ全体のバックアップ／復元 {#back-up-restore-whole-data}

このセクションでは、状態データと鍵を含むデータのバックアップと新しいインスタンスへの復元について説明します。

### ステップ1：実行中のクライアントを停止します {#step-1-stop-the-running-client}

Polygon Edgeはデータストレージに**LevelDB**を使用するため、**LevelDB**はデータベースファイルへの同時アクセスを許可しないため、バックアップ中はノードを停止する必要があります。

さらに、Polygon Edgeはクローズ時にデータのフラッシュも行います。

最初のステップでは、（サービスマネージャ、またはプロセスにSIGINT信号を送信する他のメカニズムを使用して）実行中のクライアントを停止します。そうすると正常にシャットダウンしながら2つのイベントをトリガーできます：
* 実行中のデータをディスクにフラッシュ
* LevelDBによるDBファイルロックの解除

### ステップ2：ディレクトリのバックアップ {#step-2-backup-the-directory}

クライアントが実行されていないので、データディレクトリを別のメディアにバックアップできます。`.key`拡張子を持つファイルには、現在のノードのなりすましに使用できる秘密鍵データが含まれており、これらのデータを第三者または未知の者と共有してはいけないということを心に留めておいてください。

:::info

生成された`genesis`ファイルを手動でバックアップして復元してください。そうすると復元されたノードが完全に動作します。
:::

## 復元 {#restore-1}

### ステップ1：実行中のクライアントを停止します {#step-1-stop-the-running-client-1}

Polygon Edgeのいずれかのインスタンスが実行されている場合は、ステップ2を正常に実行するために停止する必要があります。

### ステップ2：バックアップしたデータディレクトリを目的のフォルダにコピーします {#step-2-copy-the-backed-up-data-directory-to-the-desired-folder}

クライアントが実行されていない場合は、バックアップ済みのデータディレクトリを目的のフォルダにコピーできます。
さらに、以前にコピーした`genesis`ファイルを復元します。

### ステップ3：正しいデータディレクトリを指定しながら、Polygon Edgeクライアントを実行します {#step-3-run-the-polygon-edge-client-while-specifying-the-correct-data-directory}

Polygon Edgeで復元されたデータディレクトリを使用するには、起動時にユーザーがデータディレクトリへのパスを指定する必要があります。`data-dir`フラグに関する情報については、[CLIコマンド](/docs/edge/get-started/cli-commands)のセクションを参照してください。

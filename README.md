# Ansible Ubuntu Starter

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Ubuntu Server 22.04 のための、安全で再利用可能な初期設定Ansibleプレイブックです。

対話形式のジェネレーター（`generator.html`）を使うことで、Ansible初心者でもミスなく設定ファイルを作成し、サーバーの要塞化（セキュリティ強化）を完了できます。

---

## ✨ 主な特徴

このプレイブックは、まっさらなUbuntu Serverに対して以下の設定を自動で行います。

* ✅ **システムの最新化**: aptパッケージを最新の状態に更新・アップグレードします。
* 🔐 **セキュリティ強化**:
    * 作業用の管理者ユーザーを作成し、sudo権限を付与します。
    * SSHのrootログインとパスワード認証を禁止し、公開鍵認証のみを許可します。
    * UFW（ファイアウォール）を有効化し、SSHポート以外を遮断します。
    * Fail2banを導入し、ブルートフォース攻撃を自動でブロックします。
* 🌐 **ネットワーク設定**: 2段階の安全なプロセスで、サーバーのIPアドレスを固定します。
* 🇯🇵 **地域設定**: タイムゾーンを東京に、ロケールを日本語に設定します。
* ⚙️ **安定性向上**: メモリ不足によるサービス停止を防ぐため、スワップファイルを作成します。
* 🔄 **自動更新**: セキュリティ関連のアップデートを自動で適用するよう設定します。

---

## 🚀 使い方

### 事前準備

**あなたのPC (コントロールノード)**
* Ansibleがインストールされていること。
* SSHの鍵ペア（`~/.ssh/id_rsa`, `~/.ssh/id_rsa.pub`）が作成済みであること。

**サーバー (マネージドノード)**
* Ubuntu Server 22.04 がインストールされた直後の状態であること。
* 初期ユーザーでSSH接続が可能な状態であること。

### Step 1: プロジェクトの準備

まず、このリポジトリをあなたのPCにダウンロードします。
```bash
git clone [https://github.com/yutaro89/Ansible-Ubuntu-Starter.git](https://github.com/yutaro89/Ansible-Ubuntu-Starter.git)
cd Ansible-Ubuntu-Starter
```

### Step 2: 初期接続ユーザー名の確認

`generator.html`に入力する「初期接続ユーザー」が分からない場合は、サーバーにSSH接続した際のプロンプトを確認してください。`@`の前の部分があなたのユーザー名です。
```
# 例: この場合、ユーザー名は "yutaro" です。
yutaro@conaspark:~$
```
クラウドサーバーでは`ubuntu`、一般的なVPSでは`root`であることが多いです。

### Step 3: ジェネレーターで設定ファイルを作成

プロジェクト内の **`generator.html`** ファイルをブラウザで開きます。

1.  フォームに、あなたのサーバー環境に合わせた情報を**全て**入力します。
2.  「設定ファイルを生成する」ボタンをクリックします。
3.  表示された3つのテキストエリアの内容を、それぞれ以下の名前でファイルにコピー＆ペーストして**上書き保存**します。
    * `inventory.ini`
    * `playbook-ip.yml`
    * `playbook-main.yml`

### Step 4: IPアドレスの固定（ステージ1）

`inventory.ini`には**初期IPアドレス**が設定されています。この状態で、IPアドレス固定用のプレイブックを実行します。
```bash
ansible-playbook -i inventory.ini playbook-ip.yml --ask-pass
```
⚠️ **注意**: この処理は最後に接続が切れて `FAILED` と表示されますが、これは**正常な動作**です。

### Step 5: `inventory.ini` の更新

保存した `inventory.ini` ファイルを開き、IPアドレスを先ほど設定した**新しい固定IPアドレス**に手で書き換えます。

### Step 6: サーバーの初期設定（ステージ2）

更新した`inventory.ini`を使って、メインのプレイブックを実行します。
```bash
ansible-playbook -i inventory.ini playbook-main.yml --ask-pass
```
最後までエラーなく完了すれば、全てのセットアップは完了です！

### Step 7: 動作確認

新しく作成したユーザーで、サーバーにSSH接続できることを確認してください。
```bash
ssh [設定したユーザー名]@[サーバーの固定IPアドレス]
```

---

## 📁 ディレクトリ構成

```
.
├── samples/
│   ├── playbook-ip.yml.sample      # (参考用) IPアドレス固定用プレイブック
│   ├── playbook-main.yml.sample    # (参考用) 主要な初期設定用プレイブック
│   └── inventory.ini.sample        # (参考用) inventory.iniのサンプル
├── generator.html                  # 設定ファイル生成用アプリ
└── README.md                       # このファイル
```

---

## 📄 ライセンス

このプロジェクトはMITライセンスです。
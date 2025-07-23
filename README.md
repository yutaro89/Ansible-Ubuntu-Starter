# Ansible Ubuntu Starter

Ubuntu Server 22.04 のための、安全で再利用可能な初期設定Ansibleプレイブックです。

対話形式のジェネレーター（`generator.html`）を使うことで、Ansible初心者でもミスなく設定ファイルを作成し、サーバーの要塞化（セキュリティ強化）を完了できます。

---

### ✨ 主な特徴

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

### 🚀 使い方


#### 事前準備
* **あなたのPC (コントロールノード)**
    * Ansibleがインストールされていること。
    * SSHの鍵ペア（`~/.ssh/id_rsa`, `~/.ssh/id_rsa.pub`）が作成済みであること。

* **サーバー (マネージドノード)**
    * Ubuntu Server 22.04 がインストールされた直後の状態であること。
    * ✅ **初期ユーザー（`root`または`ubuntu`）でSSH接続が可能な状態であること。（パスワード認証、または一時的な公開鍵認証）**

ntu Server 22.04 がインストールされた直後の、まっさらな状態であること。

#### Step 1: プロジェクトをクローン
まず、このリポジトリをあなたのPCにダウンロードします。
```bash
git clone [https://github.com/](https://github.com/)[あなたのGitHubユーザー名]/ansible-ubuntu-starter.git
cd ansible-ubuntu-starter
```

#### Step 2: 設定ファイルの準備（ジェネレーターを使用）
プロジェクト内の `generator.html` ファイルをブラウザで開いてください。

フォームに必要な情報（サーバーの初期IPアドレス、設定したい固定IPアドレスなど）を入力し、「設定ファイルを生成する」ボタンをクリックします。

生成された2つのファイルの内容を、それぞれ `inventory.ini` と `playbook-ip.yml` という名前で保存してください。

#### Step 3: IPアドレスの固定（ステージ1）
以下のコマンドを実行して、サーバーのIPアドレスを固定します。
```bash
ansible-playbook -i inventory.ini playbook-ip.yml --ask-pass
```
⚠️ **注意**: この処理はIPアドレスを変更するため、最後に接続が切れて `FAILED` や `UNREACHABLE` と表示されます。これは**正常な動作**ですので、慌てないでください。

#### Step 4: `inventory.ini` の更新
`inventory.ini` ファイルを開き、IPアドレスを先ほど設定した**新しい固定IPアドレス**に書き換えます。

#### Step 5: サーバーの初期設定（ステージ2）
以下のコマンドで、残りのすべての初期設定を実行します。
```bash
ansible-playbook -i inventory.ini playbook-main.yml --ask-pass
```
処理が最後まで緑色の文字で完了すれば成功です！

#### Step 6: 動作確認
新しく作成したユーザーで、サーバーにSSH接続できることを確認してください。パスワードなしでログインできれば、公開鍵認証も成功しています。
```bash
ssh [設定したユーザー名]@[サーバーの固定IPアドレス]
```

---

### 📁 ディレクトリ構成
```
.
├── playbook-ip.yml      # ステージ1: IPアドレス固定用プレイブック
├── playbook-main.yml    # ステージ2: 主要な初期設定用プレイブック
├── generator.html       # 設定ファイル生成用アプリ
├── inventory.ini.sample # inventory.iniのサンプル
└── README.md            # このファイル
```

---
### 📄 ライセンス
このプロジェクトはMITライセンスです。
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
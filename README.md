# AWS CLIのインストール方法のメモ
Windows環境でpipによるAWS CLIのインストール方法のメモです。
## 背景
以下の想いから環境用意することにしました。
* Terraformを使いたい
* EC2にAWS CLIを入れるのではなく、ローカルにAWS CLIを置いてやってみたい
* 既にPythonの実行環境とpipはローカル側で整っていたので、pipからAWS CLIを導入したい
## 参考記事
* （公式）[Python と pip を使用した Windows での AWS CLI バージョン 1 のインストール、更新、アンインストール](https://docs.aws.amazon.com/ja_jp/cli/v1/userguide/install-windows.html#awscli-install-windows-pip)
* [【VSCode + Git Bash】Windows に Node.js や AWS CLI の環境を構築する方法](https://blog.css-net.co.jp/entry/dev-environment-windows#2-3-1-AWS-CLI-%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)
  >こちらの記事では後述の環境変数への追加はしていなくて、VScodeを再起動するだけでawsコマンドが有効になっていたようです。

## 前提
* Python：3.12.0
* pip：23.3.2
* CLI：Command Prompt
  * はじめは「Git Bash」を使っていたのですが、環境変数にPATHを通すことがうまくできず、コマンドプロンプトに変えました。
  * ↑トライしてはいないですが、Git Bashが動作する仕組みから理解しないといけない気がします（以下参考記事）
    * https://hackmd.io/@kit-web-team/gitbash



## 実施
### AWS CLIのインストール
* いったんpipをアップグレード
```bash
python.exe -m pip install --upgrade pip
```
* aws cliをインストール
```bash
pip install awscli --upgrade --user
```
* aws cliのPATHを通す
  * 「username」のところはご自身のユーザー名で。
  * `setx`は永続的な変更を与えます。`set`だと今回のみのセッションだけの話になるみたいなので。
```bash
setx PATH "%PATH%;C:\Users\username\AppData\Roaming\Python\Python312\Scripts"
```

* aws cliのインストール確認
```bash
aws --version
>aws-cli/1.32.11 Python/3.12.0 Windows/11 botocore/1.34.11
```

### AWS CLIの初期設定
#### 前提
* `aws configure`コマンドでconfigファイル作成する際、アクセスキーIDとシークレットアクセスキーが必要になるので、まずこれらを生成します。
* 「AdministratorAccess」権限で設定しようと思いますが、アカウント自体はあるので、今回はアクセスキー発行からやります。
#### 参考記事
* （公式）[アクセスキーを取得する](https://docs.aws.amazon.com/ja_jp/cli/latest/userguide/cli-authentication-user.html#cli-authentication-user-get)
* [【AWS】Terraformを使うための環境準備](https://cloud5.jp/saitou-terraform-start/)
* [【VSCode + Git Bash】Windows に Node.js や AWS CLI の環境を構築する方法](https://blog.css-net.co.jp/entry/dev-environment-windows#2-3-1-AWS-CLI-%E3%82%A4%E3%83%B3%E3%82%B9%E3%83%88%E3%83%BC%E3%83%AB)

#### 実施
* 上記の参考記事通り実施して、アクセスキーIDとシークレットアクセスキーを取得します。これらが記述された.csvファイルをDLできるので、DLしてローカルに格納します。
* いったんユーザー名のディレクトリに戻ります
```bash
cd ~
pwd
>/c/Users/username
```
* 初期設定
```bash
aws configure
>AWS Access Key ID [None]: AKI**************
>AWS Secret Access Key [None]: **************
>Default region name [None]: ap-northeast-1
>>Default output format [None]: json
```
* `aws configure`したディレクトリで.awsフォルダが生成されるのでいちおう中身確認
```bash
ls -a | grep .aws
>.aws/
ls .aws
>config  credentials
```
* それぞれ以下の記述となっています
  * config
  ```sh:config
  [default]
  region = ap-northeast-1
  output = json
  ```
  * credential
  ```sh:credential
  [default]
  aws_access_key_id = AKI**************
  aws_secret_access_key = **************
  ```


以上です。背景に記載のTerraformの利用までは別途メモを残します。


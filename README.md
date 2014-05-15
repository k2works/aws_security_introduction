awsセキュリティ入門
=========================

# 目的
AWS(Amazon Web Servie)のセキュリティに関するベストプラクティスとコマンドラインツール設定の公式ガイドダイジェスト解説

# 前提
| ソフトウェア   | バージョン   | 備考        |
|:---------------|:-------------|:------------|
| OS X           |10.9.2        |             |
| python         |2.7.5         |             |
| aws-cli        |1.3.10        |             |

# 構成
+ [AWSセキュリティ認証(AWS Security Credentials)](#1)
+ [IAMとは(What Is IAM?)](#2)
+ [AWSコマンドラインツールとは(What Is the AWS Command Line Interface?)](#3)

# 詳細
## <a name="1">AWSセキュリティ認証(AWS Security Credentials)</a>
>When you interact with AWS, you use AWS security credentials to verify who you are and whether you have permission to access the resources that you are requesting. In other words, security credentials are used to authenticate and authorize calls that you make to AWS.

>AWSで作業する時、必要なリソースに対して許可があるかを決めるAWSセキュリティ認証を使います。セキュリティ証明とは認証とAWSでの実行の許可と認証のために使われるものです。

### ルートアカウント認証とIAMユーザー認証
ルートアカウントはAWSに対する全ての権限を持つが複数利用の場合はひとつの認証をシェアする。IAMユーザーならユーザー単位で認証を設定できるのでひとつのAWSのアカウントで複数の利用ユーザーを管理できる。

### セキュリティ認証の種類
+ メールアドレスとパスワード
+ IAMユーザー名とパスワード
+ マルチファクタ認証(MFA)
+ アクセスキー(アクセスキーIDとシークレットアクセスキー)
+ キーペア
+ X.509認証

### どうやってセキュリティ認証を取得するか
もしセキュリティ認証をなくしたり忘れたりした場合、AWSはパスワードの再取得を許可していないので新しく作りなおしてください。

+ メールアドレスとパスワード・・・セキュリティ認証ページで変更できます。
+ IAMユーザー名とパスワード・・・IAMユーザー作成後に個別に作成できます。
+ マルチファクタ認証(MFA)・・・デフォルトでは無効になってます。
+ アクセスキー(アクセスキーIDとシークレットアクセスキー)・・・[セキュリティ認証ページ](https://console.aws.amazon.com/iam/home?#security_credential)で作成できます。
+ キーペア・・・EC2コンソールやコマンドラインツールやAPIからEC2インスタンスのキーペアを作れます。
+ X.509認証・・・SOAPベースリクエストの時だけ利用します、それ以外はアクセスキーを使います。

### AWSアカウント特定
+ AWSアカウントID・・・[Amazon Resource Names (ARNs)](http://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)で使われる１２桁の番号
+ カノニカルID・・・他のAWSアカウントに許可を与える場合につかう長い文字列

### AWSアクセスキー運用のベストプラクティス
+ ルートアカウントアクセスキーを削除する（作らない）
+ ロングタームアクセスキーの代わりにテンポラリセキュリティ認証（IAMロール）を使う
+ IAMユーザーアクセスキープロパティを管理する
  + コード内に直接アクセスキーを書かない
  + アクセスキーを別の場所に置く
  + アプリケーションごとに違うアクセスキーを使う
  + 定期的にアクセスキーを更新する
  + 使わないアクセスキーは削除する
  + もっと重要なオペレーションにはマルチファクター認証を使う

### AWSアカウントのアクセスキー管理
1. [AWSマネジメントコンソール](https://console.aws.amazon.com/console/home)にメールアドレスとパスワードでログインする。
1. 画面右上のメニューからSecurity Credentialsを選択する
1. AWSセキュリティ認証ページのAccess Keys (Access Key ID and Secret Access Key) を選択
1. Create New Access Keyを押す。最大２つまでアクセスキーを作成できます。
1. Download Key Fileを押す。ｃｓｖファイルとしてダウンロードできますが再実行はできません。
1. アクセスキーはMake Inactiveで無効にできてMake Activeで有効にします。
1. アクセスキーを削除するにはDeleteを押します。

### AWSセキュリティ監査ガイドライン
#### いつセキュリティ監査をやるのか

+ 定期的にする

+ 組織が変わった時、人がやめた時とか

+ なにかAWSサービスを停止した時、もう使わないアカウントの権限を削除しておくのは重要

+ ソフトウエアを追加・削除した時、EC2インスタン上のアプリとかOpsWorksのスタックとかクラウドフォーメーションテンプレートとか

+ 許可されていないユーザーがアクセスした時

#### 監査の一般ガイドライン
+ 根気強く
+ 決めつけない
+ シンプルに

#### AWSアカウントチェック
1. 使っていないルートアクセスキーは削除する
1. アクセスキーは定期的に更新する

#### IAMユーザーチェック
1. アクティブになっていないユーザーの削除
1. 必要のないユーザーのグループからの削除
1. グループポリシーの確認（IAMポリシーチェックティップス）

#### IAMグループチェック
1. 使っていないグループの削除
1. すでに所属していないユーザーのグループからの削除とグループ内ユーザーの見直し
1. グループに適用されているポリシーの確認

#### IAMロールチェック
1. 使っていないロールの削除
1. ロールのトラストポリシーをチェックする
1. ロールに割り当てた許可が適切かチェックする

#### SAML用IAMプロバイダチェック
1. 使っていないプロバイダの削除
1. 現在のビジネスニーズに適合しているかをAWSメタデータドキュメントをダウンロードして確認する

#### AWS EC2セキュリティ設定チェック
1. 使っていないもしくは外部に知られる恐れのあるキーペアは削除する
1. EC2セキュリティグループのチェック
  + 既に必要ではなくなったセキュリティグループを削除する
  + 既に必要ではなくなったルールをセキュリティグループから削除する
1. 必要のないEC2インスタンスは削除する
1. 必要のないスポットインスタンスリクエスはキャンセルする
1. オートスケールグループと設定を見直す、不要なら終了させる

#### その他AWSサービスチェック
+ Amazon S3バケットポリシーとACLsのチェック
+ Amazon SQSキューポリシーのチェック
+ Amazon SNSトピックポリシーのチェック
+ AWS OpsWorksポリシーのチェック

#### AWSアカウントのモニタリング
+ [AWS CloudTrail](http://aws.amazon.com/jp/cloudtrail/)を使う
+ AWS CloudTrailのログを分析する(CloudTrail用の解析ツールを提供している[ベンダー](http://aws.amazon.com/jp/cloudtrail/partners/))
+ S3バケットロギングを有効にする
+ もし不正利用の疑いがあるなら問題となる認証を無効にする
+ [Account Billing アラート](http://docs.aws.amazon.com/ja_jp/awsaccountbilling/latest/aboutv2/AlertsNotifications.html)を有効にする

#### IAMポリシーチェックティップス
+ [IAM ベストプラクティス](http://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/IAMBestPractices.html)に従う
+ 必要な許可だけIAMユーザーに与えられているか確認
+ [IAM Policy Simulator](http://aws.typepad.com/aws_japan/2013/10/aws-identity-and-access-management-policy-simulator.html)を使う
+ 各ユーザーに付与された権限を理解することとユーザーに適用されたポリシーを実行することが重要
+ 使わないサービスには権限を付与していないことを確認する
+ EC2インスタンス実行許可を与えているユーザーはiam:PassRoleアクションに従わなければならない
+ *を含んだActionまたはResource要素には注意する
+ 実行内容を反映したポリシー名称にする

#### さらなる情報
+ IAM
  + [IAM Users and Groups](http://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/Using_WorkingWithGroupsAndUsers.html)
  + [Permissions and Policies](http://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/PermissionsAndPolicies.html)
  + [Roles (Delegation and Federation)](http://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/WorkingWithRoles.html)
  + [IAM Policy Simulator](http://docs.aws.amazon.com/IAM/latest/UsingPolicySimulatorGuide/iam-policy-simulator-guide.html)


+ EC2セキュリティ
  + [Amazon EC2 Network and Security documentation](http://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/EC2_Network_and_Security.html)
  + [Demystifying EC2 Resource-Level Permissions](http://blogs.aws.amazon.com/security/post/Tx2KPWZJJ4S26H6/Demystifying-EC2-Resource-Level-Permissions)

## <a name="2">IAMとは(What Is IAM?)</a>
[ここ](http://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/IAM_Introduction.html)

### 使用開始
[ここ](http://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/IAMGettingStarted.html)

### IAM のベストプラクティスとユースケース
[ここ](http://docs.aws.amazon.com/ja_jp/IAM/latest/UserGuide/IAMBestPracticesAndUseCases.html)

+ AWS アカウントのアクセスキーをロックする
+ 個々の IAM ユーザーを作成する
+ IAM ユーザーへのアクセス権限を割り当てるためにグループを使います。
+ 最小限の特権を認める。
+ ユーザーのために強度の高いパスワードを設定する。
+ 特権ユーザーに対して、MFA を有効化する。
+ Amazon EC2 インスタンスで作動するアプリケーションに対し、ロールを使用する。
+ 認証情報を共有するのではなく、ロールを使って委託する。
+ 認証情報を定期的にローテーションする。
+ 追加セキュリティに対するポリシー条件を使用する。

## <a name="3">AWSコマンドラインツールとは(What Is the AWS Command Line Interface?)</a>
>The AWS Command Line Interface is a unified tool to manage your AWS services. With just one tool to download and configure, you can control multiple AWS services from the command line and automate them through scripts.

>AWSコマンドラインインターフェースはAWSサービスを管理する統合ツールです。たったひとつのツールをダウンロードして設定するだけで複数のAWSサービスをコマンドラインとスクリプトから自動化できるようになります。

### コマンドラインインターフェース利用開始
#### 登録
1. AWSアカウントを登録する
1. アクセスキーとシークレットアクセスキーを取得する
  1. IAMコンソールに移動
  1. ナビゲーションメニューのUsersを選択
  1. 自分のIAMユーザー名を選択
  1. User ActionsをクリックしてMnage Access Keysをクリックする
  1. Create Access Keyをクリックする
  1. キーが表示される
  1. Download Credentialsをクリックして安全な場所に保存する

#### AWS CLIインストール(Windows)
1. ええやつをダウンロードする

  [Download the AWS CLI MSI installer for Windows (64-bit)](https://s3.amazonaws.com/aws-cli/AWSCLI64.msi)  
  [Download the AWS CLI MSI installer for Windows (32-bit)](https://s3.amazonaws.com/aws-cli/AWSCLI32.msi)

1. 実行する
1. ええようにしてくれるので従う

#### Bundled Installerを使ったAWS CLIインストール (Linux, OS X, or Unix)
##### 前提
  + Linux, OS X, or Unix
  + Python 2.6.3 or later

##### パイソンインストール
1. [ダウンロードパイソン](http://python.org/download/)
1. インストールパイソン
1. 以下のコマンドを実行してパイソンがインストールされたか確認  
  ```python --help```

##### Bundled Installerを使ったAWS CLIインストール
1. [AWS CLI Bundled Installer](https://s3.amazonaws.com/aws-cli/awscli-bundle.zip)をダウンロード
1. 解凍する
1. インストールコマンドを実行する

#### pipを使ったAWS CLIインストール (Windows, Linux, OS X, or Unix)
##### 前提
  + Windows, Linux, OS X, or Unix
  + Python 2.6.3 or later
  + pip

##### パイソンインストール
1. [ダウンロードパイソン](http://python.org/download/)
1. インストールパイソン
1. 以下のコマンドを実行してパイソンがインストールされたか確認  
  ```python --help```

##### pipインストール
1. 次の３つの手段でpipをインストールできる: easy_install, apt-get/yum (on Linux only), またはマニュアルセットアップ

  a. easy_install (easy_install --helpが動くか確認)  
  ```sudo easy_install pip```

  b. Linuxならapt-get または yum でインストールできる

  Debian または Ubuntu:  
  ```$ sudo apt-get install python-pip```  
  Amazon Linux またｈ Fedora:  
  ```$ sudo yum install python-pip```

  c. pipマニュアルインストールは[setuptools(ez_setup.py)](https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py) と [pip(get-pip.py)](https://raw.github.com/pypa/pip/master/contrib/get-pip.py)をダウンロードして以下のコマンドを実行する  
  ```python ez_setup.py```  
  ```python get-pip.py  ```

1. 以下のコマンドを実行してpipがインストールされているか確認する  
  ```pip --help```

##### pipを使ったAWS CLIインストール
インストール  
```sudo pip install awscli```  
アップグレード  
```sudo pip install --upgrade awscli```

#### AWS CLIがインストールされたか確認
```aws help```

### AWSコマンドラインインターフェースの設定
#### クイックスタート
```bash
$aws configure
AWS Access Key ID [None]: アクセスキーID
AWS Secret Access Key [None]: シークレットアクセスキー
Default region name [None]: ap-northeast-1
Default output format [None]: json
$ chmod 600 ~/.aws/config
```
_~/.bashrc_
```bash
export AWS_DEFAULT_REGION=ap-northeast-1
export AWS_ACCESS_KEY_ID=アクセスキーID
export AWS_SECRET_ACCESS_KEY=シークレットアクセスキー
```
#### 設定方法と優先順位
1. コマンドライン
1. 環境変数
1. 設定ファイル

##### AWS Region

以下の方法で利用するAWSリージョンの値を設定する

+ 設定ファイルのregion
+ 環境変数のAWS_DEFAULT_REGION
+ コマンドラインオプション --region

必須項目：デフォルト値はない

##### AWS Access Key

以下の方法で利用するAWSアクセスキーの値を設定する

+ 設定ファイルのaws_access_key_id
+ 環境変数のAWS_ACCESS_KEY_ID

必須項目：デフォルト値はない

##### AWS Secret Access Key

以下の方法で利用するAWSシークレットアクセスキーの値を設定する

+ 設定ファイルのaws_secret_access_key
+ 環境変数のAWS_SECRET_ACCESS_KEY

必須項目：デフォルト値はない

##### AWS Token

以下の方法で利用するAWSトークンの値を設定する

+ 設定ファイルのaws_security_token in the configuration file.
+ 環境変数のAWS_SECURITY_TOKEN

任意項目：デフォルト値はない

##### AWS Config Location

環境変数AWS_CONFIG_FILEを使って設定ファイルの場所を指定できる

設定ファイルの場所を指定しない場合のデフォルトは  
Linux,OS X,Unix:~/.aws/config  
Windows:C:\Users\USERNAME\.aws\config

##### AWS Profile Name

以下の方法で利用するAWSプロファイルの値を設定する

+ 環境変数のAWS_DEFAULT_PROFILE
+ コマンドラインオプション--profile

指定しない場合、AWS CLIはdefaultを使う

##### Output Style

以下の方法で出力形式を指定できる

+ 設定ファイルのoutput
+ 環境変数のAWS_DEFAULT_OUTPUT
+ コマンドラインオプション--output

指定しない場合AWS CLIはJSON形式で出力する

#### 設定ファイルの場所Configuration File Location

一番簡単な方法はホームディレクトリに.awsディレクトリを作る  
AWS CLIは自動的に設定ファイルを探す

Linux,OS X,Unix:~/.aws/config  
Windows:C:\Users\USERNAME\.aws\config

別の方法として環境変数AWS_CONFIG_FILEを使って設定ファイルの場所を変更できる

#### AWS認証
+ Option #1: 設定ファイル内にアクセスキー、シークレットアクセスキー、セッショントークン（オプション）を入力する
```bash
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
aws_security_token=texample123324
```
+ Option #2: 環境変数AWS_ACCESS_KEY_ID、AWS_SECRET_ACCESS_KEY、AWS_SECURITY_TOKEN（オプション）を設定する
+ Option #3: EC2インスタンスロールを使う

#### AWSリージョン設定
+ Option #1: 設定ファイルのregin
```bash
[default]
region = us-west-2
```
+ Option #2: 環境変数のAWS_DEFAULT_REGIONに設定する
+ Option #3: コマンドラインオプション--region
```
aws sqs list-queues --region us-west-2
```

#### コマンド出力設定
+ Option #1: 設定ファイルのデフォルトoutput
```bash
[default]
output = text
```
+ Option #2: 環境変数のAWS_DEFAULT_OUTPUTに設定する
+ Option #3: コマンドラインオプション--output
```bash
aws ec2 describe-volumes --output table
```

#### 複数設定プロファイル
```bash
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
region=us-east-1

[profile test-user]
aws_access_key_id=AKIAI44QH8DHBEXAMPLE
aws_secret_access_key=je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY
region=us-west-2
```
AWS CLIを実行した時コマンドラインオプションの--profileでプロファイルを指定する  
コマンドラインオプションで指定しない場合は環境変数AWS_DEFAULT_PROFILEで指定されているプロファイルを使う  
環境変数が指定されていない場合は名前がdefaultのプロファイルを使う

#### HTTPプロクシ設定
+ Linux, OS X, or Unix
```bash
export HTTP_PROXY=http://a.b.c.d:n
export HTTPS_PROXY=http://w.x.y.z:m
```
+ Windows
```bash
set HTTP_PROXY=http://a.b.c.d:n
set HTTPS_PROXY=http://w.x.y.z:m
```
上記のhttp://a.b.c.d:n と http://w.x.y.z:m はプロクシ用IPアドレスとポート

もしIAMロールを使っているならばAWS CLIがインスタンスメタデータサービス(IMDS)にアクセスできるように以下の環境変数を設定しなければならない

+ Linux, OS X, or Unix
```bash
export NO_PROXY=169.254.169.254
```
+ Windows
```
set NO_PROXY=169.254.169.254
```

#### 入力補完
bash用
```bash
$ complete -C aws_completer aws
```
tcsh用
```tcsh
$ complete aws 'p/*/`aws_completer`/'
```
zsh用
```zsh
$ source bin/aws_zsh_completer.sh
```
# 参照
+ [AWS General Reference](http://docs.aws.amazon.com/general/latest/gr/Welcome.html)
+ [AWS Security Credentials](http://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html)
+ [What Is IAM?](http://docs.aws.amazon.com/IAM/latest/UserGuide/IAM_Introduction.html)
+ [What Is the AWS Command Line Interface?](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)
+ [AWS CLI](http://docs.aws.amazon.com/cli/latest/index.html)
+ [HowTo: Install AWS CLI - Security Credentials](http://www.dowdandassociates.com/blog/content/howto-install-aws-cli-security-credentials/)

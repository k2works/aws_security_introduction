awsセキュリティ入門
=========================

# 目的
AWS(Amazon Web Servie)のセキュリティに関するベストプラクティスとコマンドラインツール設定の公式ガイドダイジェスト解説

# 前提
| ソフトウェア   | バージョン   | 備考        |
|:---------------|:-------------|:------------|
| OS X           |10.9.2        |             |
| python         |2.7.5        |             |

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

### コマンラインインターフェース利用開始
#### 登録
1. To sign up for an AWS account
1. To get your access key ID and secret access key
  1. Go to the IAM console.
  1. From the navigation menu, click Users.
  1. Select your IAM user name.
  1. Click User Actions, and then click Manage Access Keys.
  1. Click Create Access Key.
  1. Your keys will look something like this:
  1. Click Download Credentials, and store the keys in a secure location.

#### Install the AWS CLI Using the MSI Installer (Windows)
1. Download the appropriate MSI installer.

  [Download the AWS CLI MSI installer for Windows (64-bit)](https://s3.amazonaws.com/aws-cli/AWSCLI64.msi)  
  [Download the AWS CLI MSI installer for Windows (32-bit)](https://s3.amazonaws.com/aws-cli/AWSCLI32.msi)

1. Run the downloaded MSI installer.
1. Follow the instructions that appear.

#### Install the AWS CLI Using the Bundled Installer (Linux, OS X, or Unix)
##### Prerequisites
  + Linux, OS X, or Unix
  + Python 2.6.3 or later

##### Install Python
1. [Download the Python package](http://python.org/download/) for your operating system.
1. Install Python.
1. Verify the Python installation by typing the following at a command prompt:
  ```python --help```

##### Install the AWS CLI Using the Bundled Installer
1. Download the [AWS CLI Bundled Installer](https://s3.amazonaws.com/aws-cli/awscli-bundle.zip).
1. Unzip the package.
1. Run the install command.

#### Install the AWS CLI Using pip (Windows, Linux, OS X, or Unix)
##### Prerequisites
  + Windows, Linux, OS X, or Unix
  + Python 2.6.3 or later
  + pip
##### Install Python
1. [Download the Python package](http://python.org/download/) for your operating system.
1. Install Python.
1. Verify the Python installation by typing the following at a command prompt:
  ```python --help```

##### Install pip
1. You can install pip in three ways: easy_install, apt-get/yum (on Linux only), or manual setup.

  a. If you have easy_install (check by running easy_install --help), type the following at a command prompt to set up pip.  
  ```sudo easy_install pip```

  b. If you are on Linux, you can use apt-get or yum to install pip.

  On Debian or Ubuntu:  
  ```$ sudo apt-get install python-pip```  
  On Amazon Linux or Fedora:  
  ```$ sudo yum install python-pip```

  c. To install pip manually, you need to download two files: [setuptools(ez_setup.py)](https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py) and [pip(get-pip.py)](https://raw.github.com/pypa/pip/master/contrib/get-pip.py). Then, run the following two commands.  
  ```python ez_setup.py```  
  ```python get-pip.py  ```

1. Verify your pip installation by typing the following at your command prompt:  
  ```pip --help```

##### Install the AWS CLI Using pip
```sudo pip install awscli```  
To upgrade, simply use the --upgrade option.  
```sudo pip install --upgrade awscli```

#### Test the AWS CLI Installation
```aws help```

### Configuring the AWS Command Line Interface
#### Quick Start Configuration
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
export AWS_SECURITY_TOKEN=
export AWS_DEFAULT_PROFILE=
export AWS_DEFAULT_OUTPUT=
export AWS_CONFIG_FILE=~/.aws/config
```
#### Configuration Settings and Precedence
1. Command line
1. Environment variable
1. Configuration file

##### AWS Region

Sets the AWS region to use. You can set the AWS region in the following ways:

+ Set region in the configuration file.
+ Set the AWS_DEFAULT_REGION environment variable.
+ Set --region on the command line.

This setting is required; there is no default value. For more information, see Configuring the AWS Region.

##### AWS Access Key
Sets the value of the AWS access key. You can set this value in the following ways:

+ Set aws_access_key_id in the configuration file.
+ Set the AWS_ACCESS_KEY_ID environment variable.

This setting is required; there is no default value. For more information, see AWS Credentials.

##### AWS Secret Access Key
Sets the value of the AWS secret access key. You can set this value in the following ways:

+ Set aws_secret_access_key in the configuration file.
+ Set the AWS_SECRET_ACCESS_KEY environment variable.

This setting is required; there is no default value. For more information, see AWS Credentials.

##### AWS Token
Sets the value of the AWS token, which provides temporary credentials. You can set this value in the following ways:

+ Set aws_security_token in the configuration file.
+ Set the AWS_SECURITY_TOKEN environment variable.

This setting is required if you are using a security token for access; there is no default value. For more information, see AWS Credentials.

##### AWS Config Location
Sets the location of the AWS configuration file. You can set this location using the AWS_CONFIG_FILE environment variable.

If you don't set the location, the AWS CLI uses the default location, which is ~/.aws/config on Linux, OS X, or Unix, and C:\Users\USERNAME\.aws\config on Windows. For more information, see Configuration File Location.

##### AWS Profile Name
Sets the AWS profile name to use. You can set this name in one of the following ways:

+ Set the AWS_DEFAULT_PROFILE environment variable.
+ Set --profile on the command line.

If you don't set this name, the AWS CLI uses default. For more information, see Multiple Configuration Profiles.

##### Output Style
Sets the output style to use. You can set this style in one of the following ways:

+ Set output in the configuration file.
+ Set the AWS_DEFAULT_OUTPUT environment variable.
+ Set --output on the command line.

If you don't set the output style, the AWS CLI assumes JSON output. For more information, see Configuring Command Output

#### Configuration File Location
The simplest way to use a configuration file with the AWS CLI is to name it config and place it in a folder named .aws in your home directory. On Windows, that would look like C:\Users\USERNAME\.aws\config. On Linux, OS X, or Unix, that is ~/.aws/config. The AWS CLI will automatically check this location for a configuration file.

Alternatively, you can place your configuration file in another location and point the AWS CLI to it using an environment variable. Set the AWS_CONFIG_FILE environment variable to the desired location, and the AWS CLI loads that file instead.

#### AWS Credentials
+ Option #1: In the configuration file, enter your access key ID, secret key, and optionally a session token.
```bash
[default]
aws_access_key_id=AKIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
aws_security_token=texample123324
```
+ Option #2: Configure the environment variables AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY, and AWS_SECURITY_TOKEN if using temporary security credentials.
+ Option #3: Use an EC2 instance role. For more information, see Granting Applications that Run on Amazon EC2 Instances Access to AWS Resources in Using IAM.

#### Configuring the AWS Region
+ Option #1: Configure the region setting in the configuration file:
```bash
[default]
region = us-west-2
```
+ Option #2: Specify the region in the AWS_DEFAULT_REGION environment variable.
+ Option #3: Use the --region option with an AWS CLI command. The following example lists the Amazon SQS queues for the us-west-2 region.
```
aws sqs list-queues --region us-west-2
```

#### Configuring Command Output
+ Option #1: Configure the default format in the configuration file:
```bash
[default]
output = text
```
+ Option #2: Specify the format in the AWS_DEFAULT_OUTPUT environment variable.
+ Option #3: Use the --output option with an AWS CLI command. The following example lists the Amazon EBS volumes in a table format.
```bash
aws ec2 describe-volumes --output table
```

#### Multiple Configuration Profiles
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
When you invoke the AWS CLI, specify the profile that you want to use with the --profile command line parameter. If you do not specify a profile with --profile, the AWS CLI uses the profile specified by the environment variable AWS_DEFAULT_PROFILE. If this variable does not specify a profile, the AWS CLI uses the profile named default.

#### HTTP Proxy Settings
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
In these examples, http://a.b.c.d:n and http://w.x.y.z:m are the IP addresses and ports for the HTTP and HTTPS proxies.

If you are using IAM roles, you should also specify the following environment variable, with the following IP address, so that the AWS CLI can access the Instance Meta Data Service (IMDS).

+ Linux, OS X, or Unix
```bash
export NO_PROXY=169.254.169.254
```
+ Windows
```
set NO_PROXY=169.254.169.254
```

#### Command Completion
To enable tab completion for bash, use the built-in command complete:
```bash
$ complete -C aws_completer aws
```
For tcsh,
```tcsh
$ complete aws 'p/*/`aws_completer`/'
```
For zsh, source bin/aws_zsh_completer.sh.
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

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
+ [AWS Security Credentials](#1)
+ [What Is IAM?](#2)
+ [What Is the AWS Command Line Interface?](#3)

# 詳細
## <a name="1">AWS Security Credentials</a>
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
+ When Should You Perform a Security Audit?
+ General Guidelines for Auditing
+ Review Your AWS Account
+ Review Your IAM Users
+ Review Your IAM Groups
+ Review Your IAM Roles
+ Review Your IAM Providers for SAML
+ Review Your Amazon EC2 Security Configuration
+ Review AWS Policies in Other Services
+ Monitor Activity in Your AWS Account
+ Tips for Reviewing IAM Policies
+ More Information

## <a name="2">What Is IAM?</a>
>AWS Identity and Access Management is a web service that enables Amazon Web Services (AWS) customers to manage users and user permissions in AWS. The service is targeted at organizations with multiple users or systems that use AWS products such as Amazon EC2, Amazon RDS, and the AWS Management Console. With IAM, you can centrally manage users, security credentials such as access keys, and permissions that control which AWS resources users can access.

>Without IAM, organizations with multiple users and systems must either create multiple AWS accounts, each with its own billing and subscriptions to AWS products, or employees must all share the security credentials of a single AWS account. Also, without IAM, you have no control over the tasks a particular user or system can do and what AWS resources they might use.

>IAM addresses this issue by enabling organizations to create multiple users (each user is a person, system, or application) who can use AWS products, each with individual security credentials, all controlled by and billed to a single AWS account. With IAM, each user is allowed to do only what they need to do as part of the user's job.

### IAM Getting Started
### IAM Best Practices and Use Cases
#### IAM Best Practices
#### Business Use Cases

## <a name="3">What Is the AWS Command Line Interface?</a>
>The AWS Command Line Interface is a unified tool to manage your AWS services. With just one tool to download and configure, you can control multiple AWS services from the command line and automate them through scripts.

### Getting Set Up with the AWS Command Line Interface
### Configuring the AWS Command Line Interface

# 参照
+ [AWS Security Credentials](http://docs.aws.amazon.com/general/latest/gr/aws-security-credentials.html)
+ [What Is IAM?](http://docs.aws.amazon.com/IAM/latest/UserGuide/IAM_Introduction.html)
+ [What Is the AWS Command Line Interface?](http://docs.aws.amazon.com/cli/latest/userguide/cli-chap-welcome.html)
+ [HowTo: Install AWS CLI - Security Credentials](http://www.dowdandassociates.com/blog/content/howto-install-aws-cli-security-credentials/)

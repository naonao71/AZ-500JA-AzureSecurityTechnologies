---
lab:
    title: '10 - キー コンテナー (Always Encrypted を設定して、セキュアなデータを実装する)'
    module: 'モジュール 03 - データとアプリケーションのセキュリティ保護'
---

# 課題 10: キー コンテナー (Always Encrypted を設定して、セキュアなデータを実装する)
# 受講生用ラボ マニュアル

## ラボ シナリオ

Azure SQL データベースの Always Encrypted 機能のサポートを利用する概念実証アプリケーションの作成を求められました。このシナリオで使用されるすべてのシークレットとキーは、キー コンテナーに格納する必要があります。アプリケーションは、セキュリティの状態を強化するために、Azure Active Directory (Azure AD) に登録する必要があります。これらの目的を達成するには、概念実証に以下を含める必要があります。

- Azure キー コンテナーを作成し、キーとシークレットをコンテナーに格納します。
- Always Encrypted を使用して、SQL Database を作成し、データベース テーブル内の列の内容を暗号化します。

> **注**: このラボのすべてのリソースについて、**米国東部** リージョンを使用しています。クラスに使用する地域であることを講師に確認します。 

この概念実証の構築に関連する Azure のセキュリティの側面に焦点を当て続けるために、自動化された ARM テンプレートの展開から開始し、Visual Studio 2019 と SQL Server Management Studio 2018 を使用して仮想マシンをセットアップします。 

## ラボの目的

このラボでは、次の演習を行います。

- 演習 1: ARM テンプレートから基本インフラストラクチャをデプロイする
- 演習 2: キーとシークレットを使用してキー コンテナーのリソースを構成する
- 演習 3: Azure SQL データベースとデータ駆動型アプリケーションを構成する
- 演習 4: Azure SQL データベースの暗号化における Azure Key Vault の使用方法を示す

## ラボ ファイル:

- **\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json**

- **\\Allfiles\\Labs\\10\\program.cs**

### 予想合計ラボ時間：60 分

### 演習 1: ARM テンプレートから基本インフラストラクチャをデプロイする

この演習では、次のタスクを行います。

- タスク 1: Azure VM と Azure SQL データベースをデプロイする

#### タスク 1: Azure VM と Azure SQL データベースをデプロイする

このタスクでは、Azure VM をデプロイします。これにより、デプロイの一部として Visual Studio 2019 と SQL Server Management Studio 2018 が自動的にインストールされます。 

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**: このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure ポータルにサインインします。

1. Azure portal で Azure portal ページの上部にある **「リソース、サービス、ドキュメントの検索」** テキスト ボックスで、**「カスタム テンプレートのデプロイ」** と入力し、**「Enter」** キーを押します。

1. **カスタム デプロイ** ブレードで、**エディターで独自のテンプレートをビルド** のオプションをクリックします。

1. 「**テンプレートの編集**」 ブレードで、「**ファイルの読み込み**」 をクリックし、**\\Allfiles\\Labs\\10\\az-500-10_azuredeploy.json** ファイルを見つけて、「**開く**」 をクリックします。

1. **「テンプレートの編集」** ブレードで、**「保存」** をクリックします。

1. 「**カスタム デプロイ**」 ブレードの 「**デプロイの範囲**」 で、次の設定が構成されていることを確認します (既定値を使用して他の設定を行います)。

   |設定|値|
   |---|---|
   |サブスクリプション|このラボで使用する Azure サブスクリプションの名前|
   |リソース グループ|「**新規作成**」 をクリックして、「**AZ500Lab10**」という名前を入力します。|
   |リージョン|**米国東部**|
   |Admin Username|**Student**|
   |Admin Password|**Pa55w.rd1234**|
   
    >**注**: 仮想マシンへのログオンに使用される管理資格情報を変更できますが、変更する必要はありません。

    >**注**: Azure VM をプロビジョニングできる Azureリージョンを特定するには、次を参照してください。[**https://azure.microsoft.com/ja-jp/regions/offers/**](https://azure.microsoft.com/ja-jp/regions/offers/)

1. 「**確認と作成**」 ボタンをクリックし、「**作成**」 ボタンをクリックしてデプロイを確認します。 

    >**注**: これにより、このラボに必要な Azure VM と Azure SQL データベースのデプロイが開始されます。 

    >**注**: ARM テンプレートのデプロイが完了するのを待たずに、次の演習に進んでください。デプロイには約 **20～25 分間**かかります。 

### 演習 2: キーとシークレットを使用してキー コンテナーのリソースを構成する

>**注**: このラボのすべてのリソースに対して、**米国東部** リージョンを使用しています。クラスで使用する地域であることを講師に確認します。 

この演習では、次のタスクを行います。

- タスク 1: Key Vault を作成して構成する
- タスク 2: Key Vault にキーを追加します。
- タスク 3: キー コンテナーにシークレットを追加する

#### タスク 1: Key Vault を作成して構成する

このタスクでは、Azure Key Vault リソースを作成します。Azure Key Vault のアクセス許可も構成します。

1. Azure portal の右上にある最初のアイコン（検索バーの横）をクリックして、Cloud Shell を開きます。メッセージが表示されたら、「**PowerShell**」 と 「**ストレージの作成**」 を選択します。

1. 「Cloud Shell」 ウィンドウの左上隅にあるドロップダウン メニューで 「**PowerShell**」 が選択されていることを確認します。

1. 「Cloud Shell」 ウィンドウ内の 「PowerShell」 セッションで次を実行して、リソース グループ **AZ500Lab10** に Azure Key Vault を作成します。（タスク 1 でラボのリソース グループに別の名前を使用した場合は、このタスクでもその名前を使用してください）。キー コンテナー名は一意である必要があります。選択した名前を覚えておいてください。このラボ全体で必要になります。  

    ```powershell
    $kvName = 'az500kv' + $(Get-Random)

    $location = (Get-AzResourceGroup -ResourceGroupName 'AZ500LAB10').Location

    New-AzKeyVault -VaultName $kvName -ResourceGroupName 'AZ500LAB10' -Location $location
    ```

    >**注**: 最後のコマンドの出力には、コンテナー名とコンテナーURIが表示されます。コンテナー URI の形式は `https://<vault_name>.vault.azure.net/` です

1. 「Cloud Shell」 ペインを閉じます。 

1. Azure portal の、Azure portal ページの上部にある **「ソース、サービス、ドキュメントの検索」** テキスト ボックスで、 **「リソース グループ」**と入力し、**「Enter」** キーを押します。

1. 「**リソース グループ**」 ブレードのリソース グループのリストで、**AZ500Lab10**（または以前にリソース グループに選択した他の名前）エントリをクリックします。

1. リソースグループブレードで、新しく作成されたキー コンテナーを表すエントリをクリックします。 

1. キー コンテナー ブレードの 「**設定**」 セクションで、「**アクセス ポリシー**」 をクリックし、「**アクセス ポリシーの追加**」 をクリックします。

1. 「**アクセス ポリシーの追加**」 ブレードで、次の設定を指定します (他の設定はすべて既定値のままにします)。 

    |設定|値|
    |----|----|
    |テンプレートから構成する (オプション)|**キー、シークレット、および証明書の管理**|
    |キーのアクセス許可|「**すべて選択**」をクリックすると、**16 個**のアクセス許可が選択されます (**ローテーション ポリシー操作**のアクセス許可が **オフ** になっていることを確認してください) |
    |シークレットのアクセス許可|「**すべて選択**」 をクリックして、合計 **8 個**の権限を選択します|
    |証明書のアクセス許可|「**すべて選択**」をクリックして、合計 **16 個**の権限を選択します|
    |プリンシパルの選択|「**選択されていません**」 をクリックし、「**プリンシパル**」 ブレードでユーザー アカウントを選択して、「**選択**」 をクリックします|

1. 「**アクセス ポリシーの追加**」 ブレードに戻り、「**追加**」 をクリックしてアクセス ポリシーを追加します。次に、キー コンテナーの 「アクセス ポリシー」 ブレードに戻り、変更を保存するには、「**保存**」 をクリックします。 

#### タスク 2: キー コンテナーにキーを追加する

このタスクでは、キー コンテナーにキーを追加し、キーに関する情報を表示します。 

1. Azure portal 上の Cloud Shell ペインで PowerShell セッションを開きます。

1. Cloud Shell ペインの左上のドロップダウン メニューで、「**PowerShell**」 が選択されていることを確認します。

1. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、ソフトウェアで保護されたキーをキー コンテナーに追加します。 

    ```powershell
    $kv = Get-AzKeyVault -ResourceGroupName 'AZ500LAB10'

    $key = Add-AZKeyVaultKey -VaultName $kv.VaultName -Name 'MyLabKey' -Destination 'Software'
    ```

    >**注**: キーの名前は **MyLabKey** です。

1. Cloud Shell ペイン内の PowerShell セッションで次を実行して、キーが作成されたことを確認します。

    ```powershell
    Get-AZKeyVaultKey -VaultName $kv.VaultName
    ```

1. Cloud Shell ペイン内の PowerShell セッションで次を実行して、キー識別子を表示します。

    ```powershell
    $key.key.kid
    ```

1. Cloud Shell ペインを最小化します。 

1. Azure portal に戻り、キー コンテナー ブレードの 「**設定**」 セクションで、「**キー**」 をクリックします。

1. キーのリストで、「**MyLabKey**」 エントリをクリックし、「**MyLabKey**」 ブレードで、キーの現在のバージョンを表すエントリをクリックします。

    >**注**: 作成したキーに関する情報を調べます。

    >**注**: キー識別子を使用して、任意のキーを参照できます。最新バージョンを取得するには、`https://<key_vault_name>.vault.azure.net/keys/MyLabKey` を参照するか、次のコマンドで特定のバージョンを取得します。`https://<key_vault_name>.vault.azure.net/keys/MyLabKey/<key_version>`


#### タスク 3: Key Vault にシークレットを追加する

1. Cloud Shell ペインに切り替えます。

1. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、安全な文字列値を持つ変数を作成します。

    ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
    ```

1.  Cloud Shell ペイン内の PowerShell セッションで、次を実行してシークレットをコンテナーに追加します。

    ```powershell
    $secret = Set-AZKeyVaultSecret -VaultName $kv.VaultName -Name 'SQLPassword' -SecretValue $secretvalue
    ```

    >**注**: シークレットの名前は SQLPassword です。 

1.  Cloud Shell ペイン内の PowerShell セッションで次を実行して、シークレットが作成されたことを確認します。

    ```powershell
    Get-AZKeyVaultSecret -VaultName $kv.VaultName
    ```

1. Cloud Shell ペインを最小化します。 

1. Azure portal でキー コンテナー ブレードに戻り、「**設定**」 セクションで 「**シークレット**」 をクリックします。

1. シークレットのリストで、**SQLPassword** エントリをクリックし、**SQLPassword** ブレードで、シークレットの現在のバージョンを表すエントリをクリックします。

    >**注**: 作成したシークレットに関する情報を調べます。

    >**注**: シークレットの最新バージョンを取得するには `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>` を参照するか、特定のバージョンを取得するには `https://<key_vault_name>.vault.azure.net/secrets/<secret_name>/<secret_version>` を参照します。


### 演習 3 Azure SQL データベースとデータ駆動型アプリケーションを構成する

この演習では、次のタスクを行います。

- タスク 1: クライアント アプリケーションを有効にして Azure SQL データベース サービスにアクセスできるようにする。
- タスク 2: アプリケーションからキー コンテナーへのアクセスを許可するポリシーを作成する。
- タスク 3: SQL Azure データベースの ADO.NET 接続文字列を取得する 
- タスク 4: Visual Studio 2019 および SQL Management Studio 2018 を実行している Azure VM にログオンします
- タスク 5: SQL Database でテーブルを作成し、暗号化するデータ列を選択する


#### タスク 1: クライアント アプリケーションを有効にして Azure SQL データベース サービスにアクセスできるようにする。 

このタスクでは、クライアント アプリケーションを有効にして Azure SQL データベース サービスにアクセスできるようにします。これは、必要な認証を設定し、アプリケーションの認証に必要なアプリケーション ID とシークレットを取得することにより行われます。T

1. Azure portal で、Azure portal ページの上部にある 「**リソース、サービス、ドキュメントを検索**」 テキスト ボックスに「**アプリの登録**」と入力し、**Enter** キーを押します。

1. 「**アプリの登録**」 ブレードで、「**+新規登録**」 をクリックします。 

1. 「**アプリケーションの登録**」 ブレードで、次の設定を指定します (他の設定はすべて既定値のままにします)。

    |設定|値|
    |----|----|
    |名前|**sqlApp**|
    |リダイレクト URI (省略可能)|**Web** および **https://sqlapp**|

1. 「**アプリケーションの登録**」 ブレードで、「**登録**」 をクリックします。 

    >**注**: 登録が完了すると、ブラウザーは自動的に 「**sqlApp**」 ブレードにリダイレクトされます。 

1. 「**sqlApp**」 ブレードで、「**アプリケーション (クライアント) ID**」 の値を確認します。 

    >**注**: この値を記録します。この名前は、次のタスクで必要になります。

1. 「**sqlApp**」 ブレードの 「**管理**」 セクションで、「**証明書とシークレット**」 をクリックします。

1. **sqlApp \| 証明書とシークレット**」 ブレードの 「**クライアント シークレット**」 セクションで、「**+新しいクライアント シークレット**」をクリックします

1. 「**クライアント シークレットの追加**」 ペインで、次の設定を指定します。

    |設定|値|
    |----|----|
    |説明|**Key1**|
    |有効期限|**12 か月**|
	
1. 「**追加**」 をクリック して、アプリケーションの認証資格情報を更新します。

1. **sqlApp \| 証明書とシークレット**」 ブレードで、**Key1** の値を特定します。

    >**注**: この値を記録します。この名前は、次のタスクで必要になります。 

    >**注**: ブレードから移動する*前*に、必ず値をコピーしてください。コピーすると、クリア テキスト値を取得することができなくなります。


#### タスク 2: アプリケーションからキー コンテナーへのアクセスを許可するポリシーを作成する。

このタスクでは、新しく登録されたアプリに、キー コンテナーに格納されているシークレットにアクセスするためのアクセス許可を付与します。

1. Azure portal 上の Cloud Shell ペインで PowerShell セッションを開きます。

1. Cloud Shell ペインの左上のドロップダウン メニューで、「**PowerShell**」 が選択されていることを確認します。

1. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、前のタスクで記録した**アプリケーション (クライアント) ID** を格納する変数を作成します (`<Azure_AD_Application_ID>` プレースホルダーを **アプリケーション (クライアント) ID** の値に置き換えます)。
   
    ```powershell
    $applicationId = '<Azure_AD_Application_ID>'
    ```
1. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、キー コンテナー名を格納する変数を作成します。
	```
    $kvName = (Get-AzKeyVault -ResourceGroupName 'AZ500LAB10').VaultName

    $kvName
    ```

1. Cloud Shell ペイン内の PowerShell セッションで次のコマンドを実行して、前のタスクで登録したアプリケーションにキー コンテナーの権限を付与します。

    ```powershell
    Set-AZKeyVaultAccessPolicy -VaultName $kvName -ResourceGroupName AZ500LAB10 -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
    ```

1. 「Cloud Shell」 ペインを閉じます。 


#### タスク 3: SQL Azure データベースの ADO.NET 接続文字列を取得する 

演習 1 の ARM テンプレートのデプロイでは、Azure SQL Server インスタンスと「**medical**」と名付けられた Azure SQL データベースを保存しました。空のデータベース リソースを新しいテーブル構造で更新し、暗号化するデータ列を選択する

1. Azure portal で、Azure portal ページの上部にある 「**リソース、サービス、ドキュメントを検索**」 テキスト ボックスに「**SQL データベース**」と入力し、**Enter** キーを押します。

1. SQL データベースの一覧で、「**medical(<randomsqlservername>)**」 エントリをクリックします。

    >**注**: データベースが見つからない場合は、演習1で開始したデプロイがまだ完了していない可能性があります。これを検証するには、Azure Resource Group「AZ500LAB10」（または好みの名前）を参照し、「設定」 ウィンドウから 「**デプロイ**」 を選択します。  

1. SQL データベース ブレードの「**設定**」 セクションで、「**接続文字列**」 をクリックします。 

    >**注**: このインターフェイスには、ADO.NET、JDBC、ODBC、PHP、および Go の接続文字列が含まれています。 
   
1. **ADO.NET 接続文字列**を記録します。この情報は後で必要になります。

    >**注**: 接続文字列を使用する場合は、必ず `{your_password}` プレースホルダーを「**Pa55w.rd1234**」に置き換えてください。

#### タスク 4: Visual Studio 2019 および SQL Management Studio 2018 を実行している Azure VM にログオンします

このタスクでは、演習 1 で開始した Azure VM にログオンします。この Azure VM は、Visual Studio 2019 と SQL Server Management Studio 2018 をホストします。

>**注**: このタスクを続行する前に、最初の演習で開始したデプロイが正常に完了したことを確認してください。これを検証するには、Azure リソース グループ「Az500Lab10」（または好みの名前）のブレードに移動し、「設定」 ウィンドウから 「**デプロイ**」 を選択します。  

1. Azure portal の 「**リソース、サービス、ドキュメントの検索**」 テキスト ボックスで、Azure portal ページの上部に「**Virtual Machines**」と入力し、**Enter** キーを押します。

1. 表示されている仮想マシンの一覧で、**az500-10-vm1**のエントリを選択します。**Az500-10-vm1** ブレードの 「**必須事項**」 ウィンドウで、**パブリック IP アドレス**をメモします。これは後で使用します。 

#### タスク 5: SQL Database でテーブルを作成し、暗号化するデータ列を選択する

このタスクでは、SQL Server Management Studio を使用して SQL データベースに接続し、テーブルを作成します。次に、Azure Key Vault から自動生成されたキーを使用して 2 つのデータ列を暗号化します。 

1. Azure portal で、**医療用** SQL データベースのブレードに移動し、「**必須事項**」セクションで**サーバー名** (クリップボードにコピーする) を特定してから、ツールバーで「**サーバー ファイアウォールの設定**」をクリックします。  

    >**注**: サーバー名を記録します。このタスクの後半でサーバー名が必要になります。

1. **ファイアウォール設定**ブレードで、次の設定を指定します。 

    |設定|値|
    |---|---|
    |規則名|**Mgmt VM を許可する**|
    |開始 IP|az500-10-vm1 のパブリック IP アドレス|
    |終了 IP|az500-10-vm1 のパブリック IP アドレス|

1. 「**保存**」 と 「**OK**」 をクリックして変更を保存し、確認ウィンドウを閉じます。 

    >**注**: これにより、サーバーファイアウォールの設定が変更され、このラボでデプロイした Azure VM のパブリック IP アドレスから医療用データベースに接続できるようになります。

1. **az500-10-vm1** ブレードに戻り、「**概要**」 をクリックし、次に 「**接続**」 をクリックして、ドロップダウン メニューで 「**RDP**」 をクリックします。 

1. 「**RDP ファイルのダウンロード**」 をクリックし、これを使用して、リモート デスクトップ経由で **az500-10-vm1** Azure VM に接続します。認証を求められたら、次の資格情報を入力します。

    |設定|値|
    |---|---|
    |ユーザー名|**Student**|
    |パスワード|**Pa55w.rd1234**|

    >**注**: リモート デスクトップ セッションと **サーバー マネージャー** が読み込まれるまで待ちます。サーバー マネージャーを閉じます。 

    >**注**: このラボの残りの手順は、**az500-10-vm1** Azure VM へのリモート デスクトップ セッション内で実行されます。 

1. 「**スタート**」 をクリックし、「**スタート**」 メニューの 「**Microsoft SQL Server Tools 18**」 フォルダーを展開して、「**Micosoft SQL Server Management Studio**」 メニュー項目をクリックします。

1. 「**サーバーに接続**」 ダイアログ ボックスで、次の設定を指定します。 

    |設定|値|
    |---|---|
    |サーバー タイプ|**データベース エンジン**|
    |サーバー名|このタスクで前に指定したサーバー名|
    |認証|**SQL Server 認可**|
    |ログイン|**Student**|
    |パスワード|**Pa55w.rd1234**|

1. 「**Connect to Server**」 ダイアログ ボックスで、「**Connect**」 をクリックします。

1. 「**SQL Server Management Studio**」 コンソールの 「**Object Explorer**」 ペインで、「**Databases**」 フォルダーを展開します。

1. 「**Object Explorer**」 ペインで、**medical**データベースを右クリックし、「**New Query**」 をクリックします。

1. 次のコードをクエリ ウィンドウに貼り付け、「**Execute**」 をクリックします。これにより、**Patients**テーブルが作成されます。

     ```sql
     CREATE TABLE [dbo].[Patients](
		[PatientId] [int] IDENTITY(1,1),
		[SSN] [char](11) NOT NULL,
		[FirstName] [nvarchar](50) NULL,
		[LastName] [nvarchar](50) NULL,
		[MiddleName] [nvarchar](50) NULL,
		[StreetAddress] [nvarchar](50) NULL,
		[City] [nvarchar](50) NULL,
		[ZipCode] [char](5) NULL,
		[State] [char](2) NULL,
		[BirthDate] [date] NOT NULL 
     PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
     ```
1. テーブルが正常に作成されたら、「**Object Explorer**」 ペインで、 「**medical**」 データベース ノード、「**Tables**」 ノードを展開します。「**dbo.Patients**」 ノードを右クリックして 「**Encrypt Columns**」 をクリックします。 

    >**注**: これにより、**Always Encrypted** ウィザードが起動します。

1. **Introduction**ページで 「**Next**」 をクリックします。

1. 「**列の選択**」ページで、「**SSN**」列と「**Birthdate**」列を選択し、「**SSN**」列の「**暗号化の種類**」を「**確定的**」に、「**Birthdate**」列を「**ランダム化**」に設定し、「**次へ**」をクリックします。

**注**: 暗号化の実行中に、**Rotary(Microsoft.SQLServer.Management.ServiceManagement)** に関連する**呼び出しのターゲットによって例外**などのエラーがスローされた場合は、**ローテーション ポリシー操作**の**キー アクセス許可**の値が**オフになっている**ことを確認してください。Azure　portal にない場合は、「**Key Vault**」 >> 「**アクセス ポリシー**」 >> 「**キーのアクセス許可**」 >> 「**ローテーション ポリシーの操作**」の下のすべての値のチェックを外します。 

1. 「**Master Key Configuration**」 ページで、「**Azure Key Vault**」 を選択し、「**Sign In**」 をクリックします。認証を求められたら、このラボで行った Azure Key Vault インスタンスのプロビジョニングで使用したのと同じユーザー アカウントを使い認証します。「**Select an Azure Key Vault**」 ドロップ ダウン リストに該当のキー コンテナーが表示されるのを確認し、「**Next**」 をクリックします。

1. 「**Run Settings**」 ページで 「**Next**」 をクリックします。
	
1. 「**Summary**」 ページで、「**Finish**」をクリックして暗号化を続行します。認証を要求されたら、このラボで Azure Key Vault インスタンスをプロビジョニングするために使用したのと同じユーザー アカウントを使用して、再度サインインします。

1. 暗号化プロセスが完了したら、「**Results**」 ページで 「**Close**」 をクリックします。

1. **SQL Server Management Studio** コンソールで、 「**Object Explorer**」 ウィンドウの 「**medical**」 ノードの下にある 「**Security**」 および 「**Always Encrypted Keys**」 サブノードを展開します。 

    >**注**: 「**Always Encrypted Keys**」 サブノードには、「**Column Master Keys**」 と 「**Column Encryption Keys**」 のサブフォルダーが含まれています。


### 演習 4: Azure SQL データベースの暗号化における Azure Key Vault の使用方法を示す

この演習では、次のタスクを行います。

- タスク 1: データ駆動型アプリケーションを実行して、Azure SQL データベースの暗号化における Azure Key Vault の使用法を示す

#### タスク 1: データ駆動型アプリケーションを実行して、Azure SQL データベースの暗号化における Azure Key Vault の使用法を示す

Visual Studio を使用して暗号化列にデータを読み込むコンソール アプリケーションを作成し、Key Vault のキーにアクセスする接続文字列を使用してそのデータに安全にアクセスします。

1. RDP セッションから **az500-10-vm1** まで、**スタート メニュー**から **Visual Studio 2019** を起動します。

1. Visual Studio 2019 のウェルカム メッセージを表示するウィンドウに切り替え、「**Sign in**」 ボタンをクリックし、プロンプトが表示されたら、このラボで使用している Azure サブスクリプションへの認証に使用した資格情報を入力します。

1. **Get started** ページ で、「**Create a new project**」 をクリックします。 

1. プロジェクト テンプレートの一覧で、 **Console App (.NET Framework)** を検索し、結果の一覧から 「**Console App (.NET Framework)** for **C#**」 をクリックして 「**Next**」 をクリックします。

1. 「**Configure your new project**」ページで、次の設定を指定し (他の設定は既定値のままにします)、「**Create**」をクリックします。

    |設定|値|
    |---|---|
    |プロジェクト名|**OpsEncrypt**|
    |ソリューション名|**OpsEncrypt**|
    |フレームワーク|**.NET Framework 4.7.2.**|

1. Visual Studio コンソールで、「**Tools**」 メニューのドロップダウン メニューから 「**NuGet Package Manager**」 をクリックし、カスケード メニューの 「**Package Manager Console**」 をクリックします。

1. **「Package Manager Console」** ペインで、次を実行して、最初に必要な **NuGet** パッケージをインストールします。

    ```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    ```

1. **「Package Manager Console」** ペインで、次を実行して、2 番目に必要な **NuGet** パッケージをインストールします。

    ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```
    
1. **\\Allfiles\\Labs\\10\\program.cs** に移動してファイルをメモ帳で開き、その内容をクリップボードにコピーします。

1. Visual Studio コンソールに切り替えて、「**Solution Explorer**」 ウィンドウで 「**Program.cs**」 をクリックし、その内容をクリップボードにコピーしたコードに置き換えます。

1. Visual Studio ウィンドウにある **Program.cs** ウィンドウの 15 行目で、`<connection string noted earlier>` プレースホルダーを、このラボで前に記録した Azure SQL データベース **ADO.NET** の接続文字列に置き換えます。接続文字列で、 `{your_password}` プレースホルダーを `Pa55w.rd1234` に置き換えます。

1. Visual Studio ウィンドウにある **Program.cs** ウィンドウの 16 行目で、`<client id noted earlier>` プレースホルダーを、このラボで前に記録した、登録済みアプリの**Application (client) ID** の値に置き換えます。 

1. Visual Studio ウィンドウにある **Program.cs** ウィンドウの 17 行目で、`<key value noted earlier>` プレースホルダーを、このラボで前に記録した、登録済みアプリの **Key1** の値に置き換えます。 

1. Visual Studio コンソールで、「**Start**」 ボタンをクリックして、コンソール アプリケーションの作成を開始します。

1. アプリケーションはコマンド プロンプト ウィンドウを起動します。パスワードの入力を求められたら、「**Pa55w.rd1234**」と入力して Azure SQL データベースに接続します。 

1. コンソール アプリを実行したまま、**SQL Management Studio** コンソールに切り替えます。 

1. 「**オブジェクト エクスプローラー**」ペインで、医療用データベースを右クリックし、右クリック メニューで、「**新しいクエリ**」をクリックします。

1. クエリ ウィンドウから次のクエリを実行して、コンソール アプリからデータベースに読み込まれたデータが暗号化されていることを確認します。

    ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
    ```

1. 有効な SSN の入力を求められるコンソール アプリに切り替えます。これにより、データの暗号化された列がクエリされます。コマンド プロンプトで、次のように入力し、Enter キーを押します。

    ```cmd
    999-99-0003
    ```

    >**注**: クエリによって返されたデータが暗号化されていないことを確認します。

1. コンソール アプリを終了するには、Enter キーを押します。

**リソースをクリーン アップする**

> 新しく作成した Azure リソースのうち、使用しないリソースは必ず削除してください。使用していないリソースを削除することで、予期しないコストが発生しなくなります。

1. Azure portal から、Azure portal の右上にあるアイコンをクリックして、 Cloud Shell を開きます。 

1. 「Cloud Shell」 ウィンドウの左上のドロップダウン メニューで 「**PowerShell**」 を選択し、メッセージが表示されたら 「**確認**」 をクリックします。

1. 「Cloud Shell」 ウィンドウ内の PowerShell セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB10" -Force -AsJob
    ```

1.  **「Cloud Shell」** ペインを閉じます。 

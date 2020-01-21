

# モジュール 4：常に暗号化を設定して、セキュアなデータを実装する


**シナリオ**

このモジュールには、次のタスクが含まれています。

 - Azure 機密コンピューティング
 - Azure Azure Key Vault


## 演習 1：Azure Key Vault の概要


**シナリオ**

このラボでは、Azure Key Vault を使用して、Azure で強化されたコンテナー（ボールト）を作成し、Azure で暗号化キーとシークレットを保存および管理します。まず、Azure PowerShell を使用します。次に、パスワードをシークレットとして保存し、Azure アプリケーションで使用できるようにします。

### タスク 1：SQL Server Management Studio をダウンロードする

1.  このラボに必要な SQL Management Studio の最新バージョンをダウンロードするには、次のリンクにアクセスして、SQL Management Studioのダウンロード **「https://docs.microsoft.com/ja-jp/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017」** を選択します。 



### タスク 2：PowerShell を使用して Key Vault を作成する


この演習では、PowerShell を使用して Azure Key Vault を作成します。


1.  **スタート > PowerShell** をクリックして PowerShell を起動する

2.  次のコマンドを使用して、Azure サブスクリプションのアカウントを使用して Azure を認証します。

     ```powershell
    Login-AzureRMAccount
     ```

4.  新しいリソース グループの作成 

     ```powershell
    New-AzureRmResourceGroup -Name 'KeyVaultPSRG' -Location 'eastus'
     ```


5.  リソース グループに Key Vault を作成します。**VaultName は一意である必要があります。**

     ```powershell
    New-AzureRmKeyVault -VaultName 'KeyVaultPS' -ResourceGroupName    'KeyVaultPSRG' -Location 'eastus'
     ```

    **注記**：この出力には、重要な情報が表示されます。この場合の Vault Name は KeyVaultPS および Vault URI です。「https://KeyVaultPS.vault.azure.net」



6.  Azure ポータルで、**KeyVaultPSRG** リソース グループを開きます。

7.  KeyVaultPS をクリックして、作成したものを調べます。

### タスク 3：Key Vault にキーとシークレットを追加する

1.  PowerShell ウィンドウに戻ります。

2.  このコマンドを使用して、ソフトウェアで保護されたキーを Key Vault に追加します。プレースホルダー テキストを必ず vault 名に変更してください。

     ```powershell
    $key = Add-AzureKeyVaultKey -VaultName '<YourVaultName>' -Name 'MyLabKey'     -Destination 'Software'
     ```

3.  Azure portal で **KeyVaultPS** に戻ります。左側のナビゲーションの「設定」で「キー」をクリックします。

4.  **MyLabKey** をクリックする

5.  現在のバージョンをクリックする

6.  作成したキーに関する情報を調べます。

    **注記**：URI を使用して、いつでもこのキーを参照できます。最新バージョンを取得するには、単に「https://keyvaultps.vault.azure.net/keys/MyLabKey/」を参照するか、正確なバージョンが必要な場合：「https://keyvaultps.vault.azure.net/keys/MyLabKey/da1a3a1efa5dxxxxxxxxxxxxxd53c5959e」


7.  PowerShell ウィンドウに戻ります。キーの現在のバージョンを表示するには、次のコマンドを入力します。

     ```powershell
    $Key.key.kid
     ```


8.  作成したキーを表示するには、Get-AzureKeyVaultKey コマンドレットを使用できます。プレースホルダー テキストを必ず vault 名に変更してください。

     ```powershell
    Get-AzureKeyVaultKey -VaultName '<YourVaultName>'
     ```


### タスク 4：Key Vault にシークレットを追加する

1.  次に、**KeyVaultPS** にシークレットを追加します。これを行うには、次のコードを使用する **$secretvalue** という変数を追加します。

     ```powershell
    $secretvalue = ConvertTo-SecureString 'Pa55w.rd1234' -AsPlainText -Force
     ```


2.  次に、このコマンドでシークレットを Vault に追加します。プレースホルダー テキストを必ず vault 名に変更してください。

     ```powershell
    $secret = Set-AzureKeyVaultSecret -VaultName '<YourVaultName>' -Name      'SQLPassword' -SecretValue $secretvalue
     ```

3.  **KeyVaultPS** で Azure Portal に戻り、**シークレット** をクリックする

4.  シークレット **SQLPassword** をクリックする

5.  現在のバージョンをクリックする

6.  作成したシークレットを調べる

    **注記**：URI を使用して、いつでもこのキーを参照できます。最新バージョンを取得するには、「https://keyvaultps.vault.azure.net/secrets/SQLPassword」を参照するか、正確なバージョンが必要な場合：「https://keyvaultps.vault.azure.net/secrets/SQLPassword/c5aada85d3acxxxxxxxxxxe8701efafcf3」


7.  **シークレットの値を示す** ボタンをクリックします。パスワードが表示されることに注意してください。

8.  シークレットを表示するには、Get-AzureKeyVaultSecret コマンドレットを使用します。プレースホルダー テキストを必ず vault 名に変更してください。

     ```powershell
    Get-AzureKeyVaultSecret -VaultName '<YourVaultName>'
     ```

### タスク 5：クライアントアプリケーションを有効にする


クライアント アプリケーションを有効にして Azure SQL Database サービスにアクセスできるようにします。これは、必要な認証を設定し、アプリケーションの認証に必要なアプリケーション ID とシークレットを取得することにより行われます。これらの手順は、次の PowerShell コードで実行されます。


1.  Azure portal を開き、Azure Active Directory に移動します。

2.  左側のナビゲーションの [管理] で [アプリの登録] をクリックします。

3.  **[+ 新しいアプリの登録] をクリックする**

1.  アプリケーションに **sqlApp** と名前を入力し、**Webapp API** を選択し、サインオン URL に、**「http：//sqlapp」** を入力する

5.  **作成** をクリックします。

6.  自動的に表示されない場合、アプリの登録が完了したら、**sqlApp** をクリックします。

7.  後で必要になるため、アプリケーション ID をコピーします。

8.  **設定** をクリックする

9.  **キー** をクリックする

10.  「キー」セクションで、説明に **Key1** と入力します。有効期限ドロップダウン リストから **1 年** を選択します。


1.  「キー値」を「値」ボックスに貼り付けて、「保存」をクリックします。ブレードを閉じます。ブレードを再度開くと、値は非表示として表示されます。

### タスク 6：アプリケーションを許可する Key Vault ポリシーを追加して、Key Vault にアクセスします。

1.  **Azure portal** で、ラボの最初で作成されている **リソース グループ** を開く

1.  **Azure Key** vault を選択する

1.  **アクセス ポリシー** をクリックする

1.  Azure サブスクリプションに関連付けられたアカウントを選択する

1.  **キーのアクセス許可** ドロップダウンで、**すべて選択** を選択し、すべての権限を強調表示する

1.  OK を選択する

1.  ユーザーリストに戻ったら、**保存** をクリックする

    **重要**！保存しないと、アクセス許可がコミットされない 


8.  **Powershell ISE** で以下の PowerShell を実行し、プレースホルダー テキストを **アカウントの詳細** に置き換える sqlApp キーのアクセス許可を設定する

    
     ```powershell
    $subscriptionName = '[Azure_Subscription_Name]'
    $applicationId = '[Azure_AD_Application_ID]'
    $resourceGroupName = '[Resource_Group_with_KeyVault]'
    $location = '[Azure_Region_of_KeyVault]'
    $vaultName = '[KeyVault_Name]' 
     ```
    
     ```powershell
    Login-AzureRmAccount
     ```
    
     ```powershell
    Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName `-ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
     ```


 
### タスク 7：Key Vault を使用して Azure SQL Database でデータを暗号化する


**シナリオ**

このタスクでは、空の Azure SQL Database を作成し、SQL Management Studio で接続してテーブルを作成します。次に、Azure Key Vault から自動生成されたキーを使用して 2 つのデータ列を暗号化します。次に、Visual Studio を使用して暗号化列にデータを読み込むコンソールアプリケーションを作成し、Key Vault を介してキーにアクセスする接続文字列を使用してそのデータに安全にアクセスします。


1.  Azure Portal から、**リソースの作成 > データベース > SQL データベース** をクリックする

2.  「SQL Database」ブレードで次の詳細を指定し、[作成] をクリックします。

      - データベース名： **医療**

      - リソース グループ：（新規作成） **SQLEncryptRG**

      - ソースを選択：**空白のデータベース**

      - サーバー **設定の構成が必須**：**新しいサーバーを作成する**

          - サーバー名: **[一意のサーバー名]**

          - サーバー管理者のログイン：**demouser**

          - パスワード：**Pa55w.rd1234**

          - 場所: **[KeyVaultPS と同じ場所]**

          - 次に**選択** をクリックします

      - 価格レベル: S0



1.  上記をすべて設定したら、**作成** を選択する

3.  SQL データベースがデプロイされたら、Azure Portal で開き、接続文字列を見つけてコピーします。
警告
**注記**：将来の使用のために接続文字列を保存するときは、{your_username} を **demouser** に必ず置き換え、{your_password} を **Pa55w.rd1234** に置き換えてください。


### タスク 8：SQL Database にテーブルを作成する

1.  Azure portal を使用して、Medical Database があるサーバー名を見つけ、名前をコピーします。



2.  この同じブレードで、[サーバーファイアウォールの設定] をクリックします。



3.  次に **+ クライアント IP を追加** をクリックし、**保存** をクリックします。



4.  LABVM で SQL Server Management Studio を開きます。これらのプロパティを使用するサーバーに接続して、サーバーのダイアログに接続します。

  - サーバータイプ：**データベースエンジン**

  - サーバー名: **[データベース概要ブレードにあります]**

  - 認証：**SQL Server の認証**

  - ログイン： **demouser**

  - パスワード：**Pa55w.rd1234**


### タスク 9：テーブルを作成して暗号化する

1.  SQL Management Studioで、**データベース > 医療を右クリック > 新しいクエリ** を展開します。

2.  次のコードをクエリ ウィンドウに貼り付けて、実行をクリックする

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


3.  テーブルが正常に作成されたら、 **医療 > テーブル > dbo.Patients を右クリック** を展開し、**列の暗号化** を選択します。



4.  [次へ] をクリックします。

5.  列選択画面で、**SSN** と **誕生日** をチェックします。次に、SSN の暗号化タイプを **確定** に設定し、誕生日を **ランダム** に設定します。**次へ** をクリックします。



6.  「マスター キー構成」ページの「キー ストアプロバイダーの選択」で、**Azure Key Vault** をクリックします。**サインイン** をクリックして、認証します。Azure Key vault を選択します。**次へ** をクリックします。



7.  実行設定画面で、**次へ** をクリックしたら、**完了** をクリックして、暗号化を続行します。



8.  **医療 > セキュリティ > 常に暗号化されたキー** を展開し、キーが発見されていることに注意してください。



### タスク 10：暗号化された列を操作するコンソールアプリケーションをビルドする

1.  LABVM で Visual Studio 2019 を開き、サインインします。

2.  **ファイル > 新規 > プロジェクト** をクリックする

3.  **Visual C＃>コンソールアプリ（.NET Framework）** を選択して、名前 **OpsEncrypt** を場所 **C：** に入力して、**OK** をクリックします。


4.  **OpsEncrypt** プロジェクトを **右クリック** して、**プロパティ** をクリックします。



5.  **ターゲット フレームワーク** を **NET Framework 4.6.2** に変更し、**ターゲット フレームワーク** を変更するように求められたら、**はい** をクリックします。



6.  **NuGet パッケージ マネージャー コンソール** を **開きます**。



7.  **ツール** > **NuGet パッケージ マネージャー** > **パッケージ マネージャー コンソール** に移動し、以下の **NuGet** をインストールします。

     ```powershell
    Install-Package     Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
     ```

     ```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
     ```

1.  ブラウザを開いて、**「https://aka.gd/az300t03mod5code」** に移動し、コードをコピーします。

8.  **Program.cs** のコードをコピーしたコードに置き換えます。

9.  Main メソッドで接続文字列設定を見つけ、前の手順でコピーした値に置き換えます。

10.  **Visual Studio** で **スタートボタン** を **クリック** します。


1.  **コンソールアプリケーション** は **ビルド** して、開始します。最初にアプリケーションにデータを追加してから、パスワードを要求します。

    - サーバーパスワード：**Pa55w.rd1234**

1.  **コンソール アプリケーションを実行中** の **ままにして**、**SQL Management Studio** に移動します。医療データベースを右クリックして、**新しいクエリ** をクリックします。



1.  次のクエリを実行して、データベースにロードされたデータが暗号化されていることを確認します。

     ```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
     ```


1.  次に、要求されるコンソールアプリケーションに戻ります。**有効なSSN** を **入力** するように求められます。これにより、暗号化された列のデータが照会されます。Key Vault から呼び出されたキーを使用すると、データは暗号化されずにコンソールウィンドウに表示されます。

     ```sql
    999-99-0003
     ```

1.  **終了** するには、Enterキーを押します。


| 警告：続行する前に、このラボで使用したすべてのリソースを削除する必要があります。  **Azure Portal** でこれを行うには、**リソース グループ** をクリックします。  作成したリソース グループを選択します。  リソース グループ ブレードで、**リソース グループを削除** をクリックし、リソース グループ名を入力して、**削除** をクリックします。  作成した可能性のある追加のリソース グループに対してプロセスを繰り返します。**これを行わないと、他のラボで問題が発生する可能性があります。** |
| --- |

**結果** : このコースを修了しました。



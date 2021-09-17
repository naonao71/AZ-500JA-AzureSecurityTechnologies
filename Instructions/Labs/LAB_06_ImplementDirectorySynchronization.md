---
lab:
    title: '06 - ディレクトリ同期の導入'
    module: 'モジュール 01 - ID とアクセスの管理'
---

# 課題 06: ディレクトリ同期の導入
# 受講生用ラボ マニュアル

## ラボのシナリオ

オンプレミスの Active Directory Domain Services (AD DS) 環境を Azure Active Directory (Azure AD) テナントに統合する方法を示す概念実証を作成するよう依頼されました。具体的には、次のことを行います。

- AD DS ドメイン コントローラーをホストする Azure VM をデプロイして単一ドメイン AD DS フォレストを導入する
- Azure AD ユーザーを作成して構成する
- AD DS フォレストを Azure AD テナントと同期する

> このラボのすべてのリソースについて、**米国東部** リージョンを使用しています。クラスに使用する地域であることを講師に確認します。 

## ラボの目的

このラボでは、次の演習を行います:

- 演習 1: Active Directory ドメイン コントローラーをホストする Azure VM をデプロイする
- 演習 2: Azure Active Directory テナントを作成および構成する
- 演習 3: Active Directory フォレストを Azure Active Directory テナントと同期する

### 演習 1: Active Directory ドメイン コントローラーをホストする Azure VM をデプロイする

### 予想時間: 10 分

この演習では、次のタスクを行います。

- タスク 1: Azure VM のデプロイで利用可能な DNS 名を特定する
- タスク 2: ARM テンプレートを使用して、Active Directory ドメイン コントローラーをホストする Azure VM をデプロイする

#### タスク 1: Azure VM のデプロイで利用可能な DNS 名を特定する

このタスクでは、Azure VM デプロイの DNS 名を識別します。 

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注意**: このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure ポータルにサインインします。

1. Azure portal の右上にある最初のアイコンをクリックして、Cloud Shell を開きます。メッセージが表示されたら、「**PowerShell**」 と 「**ストレージの作成**」 をクリックします。

1. 「Cloud Shell」 ウィンドウの左上隅にあるドロップダウン メニューで 「**PowerShell**」 が選択されていることを確認します。

1. Cloud Shell ウィンドウ内の PowerShell セッションで、次の手順を実行して、この演習の次のタスクで Azure VM のデプロイに使用できる利用可能な DNS 名を特定します:

    ```powershell
    Test-AzDnsAvailability -DomainNameLabel <custom-label> -Location '<location>'
    ```

    >**注意**: `<custom-label>`プレースホルダーを、グローバルに一意である可能性がある有効な DNS 名に置き換えます。`<location>` プレースホルダーを、このラボで使用する Active Directory ドメイン コントローラーをホストする Azure VM をデプロイするリージョンの名前に置き換えます。

    >**注意**: Azure VM をプロビジョニングできる Azure リージョンを識別するには、[**https://azure.microsoft.com/ja-jp/regions/offers/**](https://azure.microsoft.com/ja-jp/regions/offers/) を参照してください。

1. コマンドが **True** へ戻したことを確認します。そうでない場合は、コマンドが **True**へ戻すまで、`<custom-label>`の異なる値で同じコマンドを再実行します。

1. **True** になった`<custom-label>` の値を記録します。これは、次のタスクで必要になります。

1. Cloud Shell ペインを閉じます。

#### タスク 2: ARM テンプレートを使用して、Active Directory ドメイン コントローラーをホストする Azure VM をデプロイする

このタスクでは、Active Directory ドメイン コントローラーをホストする Azure VM をデプロイします

1. 同じブラウザーの画面で別のブラウザー タブを開き、[https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/active-directory/active-directory-new-domain](`https://github.com/Azure/azure-quickstart-templates/tree/master/application-workloads/active-directory/active-directory-new-domain`) に移動します。 

1. 「**Create a new Windows VM and create a new AD Forest, Domain and DC**」 ページで、「**Deploy to Azure**」 をクリックします。これにより、ブラウザーが Azure portal の 「**カスタムテンプレートからのデプロイ**」 ブレードに自動的にリダイレクトされます。

1. 「**カスタム デプロイ**」 ブレードで、「**パラメーターの編集**」 をクリックします。

1. 「**パラメーターの編集**」 ブレードで、「**ファイルの読み込み**」 をクリックし、「**開く**」 ダイアログ ボックスで、**\\\\AllFiles\Labs\\06\\active-directory-new-domain\\azuredeploy.parameters.json** をクリックし、「**開く**」 をクリックして、「**保存**」 をクリックします。 

1. 「**新しい AD フォレストを使用して Azure VM を作成する**」 ブレードで、次の設定を指定します (他のユーザーには既存の値を残します)。

   |設定|値|
   |---|---|
   |サブスクリプション|Azure サブスクリプションの名前|
   |リソース グループ|「**新規作成**」 をクリックし、「**AZ500Lab06**」という名前を入力します|
   |リージョン|前のタスクで特定した Azure リージョン|
   |管理者ユーザー名|**student**|
   |管理者のパスワード|**Pa55w.rd1234**|
   |ドメイン名|**adatum.com**|
   |DNS プレフィックス|前のタスクで特定した DNS ホスト名|
   |VM サイズ|**Standard_D2s_v3**|

1. 「**カスタム デプロイ**」 ブレードで、「**確認と作成**」 をクリックし、「**作成**」 をクリックします。

    >**注意**: デプロイが完了するのを待たず、代わりに次の演習に進みます。デプロイには約 15 分間かかります。このラボの 3 番目の演習では、このタスクでデプロイされた仮想マシンを使用します。

> 結果: この演習を完了した後、Azure Resource Manager テンプレートを使用して、Active Directory ドメイン コントローラーをホストする Azure VM のデプロイを開始しました。


### 演習 2: Azure Active Directory テナントを作成および構成する 

### 予想時間: 20 分

この演習では、次のタスクを行います。

- タスク 1: Azure Active Directory (AD) テナントを作成する
- タスク 2: 新しい Azure AD テナントにカスタム DNS 名を追加する
- タスク 3: グローバル管理者の役割を持つ Azure AD ユーザーを作成する

#### タスク 1: Azure Active Directory (AD) テナントを作成する

このタスクでは、このラボで使用する新しい Azure AD テナントを作成します。 

1. Azure portal の 「**リソース、サービス、ドキュメントの検索**」 テキスト ボックスで、Azure portal ページの上部に「**Azure Active Directory**」と入力し、**Enter** キーを押します。

1. 現在の Azure AD テナントの**概要**を表示しているブレードで、「**テナントの管理**」をクリックし、次の画面で「**+ 作成**」をクリックします。

1. 「**ディレクトリの作成**」 ブレードの 「**基本**」 タブで、オプション 「**Azure Active Directory**」 が選択されていることを確認し、「**次:構成**」 をクリックします。

1. 「**ディレクトリの作成**」 ブレードの 「**構成**」 タブで、次の設定を指定します。

   |設定|値|
   |---|---|
   |組織名|**AdatumSync**|
   |初期ドメイン名|文字と数字の組み合わせから成る一意の名前|
   |国/地域|**米国**|

    >**注意**: 初期ドメイン名を記録します。このラボの後半で必要になります。

    >**注意**: 「**初期ドメイン名**」 テキスト ボックスの緑色のチェック マークは、入力したドメイン名が有効で一意であるかどうかを示します。(後で使用するために、初期ドメイン名を記録してください)。

1. **「確認および作成」** をクリックし、**「作成」** をクリックします。

    >**注意**: 新しいテナントが作成されるのを待ちます。**通知** アイコンを使用して、デプロイ ステータスを監視します。 

#### タスク 2: 新しい Azure AD テナントにカスタム DNS 名を追加する

このタスクでは、新しい Azure AD テナントにカスタム DNS 名を追加します。 

1. Azure portal のツール バーで、Cloud Shell アイコンの右側にある 「**ディレクトリ + サブスクリプション**」 アイコンをクリックします。 

1. 「**ディレクトリ + サブスクリプション**」 ブレードで、新しく作成したテナント、**AdatumSync** をクリックします。

    >**注意**: **AdatumSync** エントリが **ディレクトリ + サブスクリプション** のフィルター リストに表示されない場合は、ブラウザーの画面を更新する必要があります。

1. 「**AdatumSync \| Azure Active Directory**」ブレードの「**管理**」セクションで、「**カスタム ドメイン名**」をクリックします。

1. 「**AdatumSync \| カスタム ドメイン名**」 ブレードで、「**+ カスタム ドメインの追加**」 をクリックします。

1. 「**カスタム ドメイン名**」 ブレードの 「**カスタム ドメイン名**」 テキスト ボックスに「**adatum.com**」と入力し、「**ドメインの追加**」 をクリックします。

1. 「**adatum.com**」 ブレードで、Azure AD ドメイン名の検証を実行するために必要な情報を確認します。

    >**注意**: **adatum.com** DNS ドメイン名を所有していないため、検証プロセスを完了できません。これにより **adatum.com** AD DS ドメインと Azure AD テナントとの同期が妨げになるわけではありません。この目的のために、前のタスクで特定した Azure AD テナントの最初の DNS 名 (**onmicrosoft.com** サフィックスで終わる名前) を使用します。ただし、その結果、AD DS ドメインの DNS ドメイン名と Azure AD テナントの DNS 名は異なることを覚えておいてください。つまり、AD DS ドメインにサインインするときと Azure AD テナントにサインインするときに、Adatum ユーザーは異なる名前を使用する必要があります。

#### タスク 3: グローバル管理者の役割を持つ Azure AD ユーザーを作成する

このタスクでは、新しい Azure AD ユーザーを追加し、グローバル管理者の役割に割り当てます。 

1. **AdatumSync** Azure AD テナント ブレードの 「**管理**」 セクションで、「**ユーザー**」 をクリック します。

1. 「**ユーザー \| すべてのユーザー**」 ブレードで、「**+ 新しいユーザー**」 をクリックします。 

1. 「**新しいユーザー**」 ブレードで、「**ユーザーの作成**」 オプションが選択されていることを確認し、次の設定を指定し (その他の設定はすべて既定値のままにします)、「**作成**」 をクリックします。

   |設定|値|
   |---|---|
   |ユーザー名|**syncadmin**|
   |名前|**syncadmin**|
   |パスワード|「**パスワードの自動生成**」 オプションが選択されていることを確認し、「**パスワードの表示**」 をクリックします。|
   |グループ|**0 グループが選択されました**|
   |役割|「**ユーザー**」 をクリックし、「**グローバル管理者**」 をクリックし、「**選択**」 をクリックします。|
   |利用場所|**United States**|  

    >**注意**: 完全なユーザー名を記録します。ドロップダウン リストの右側にある 「**クリップボードにコピー**」 ボタンをクリックして、ドメイン名を表示することで、値をコピーできます。 

    >**注意**: ユーザーのパスワードを記録します。これは、このラボの後半で必要になります。 

    >**注意**: Azure AD Connect を導入するには、グローバル管理者の役割を持つ Azure AD ユーザーが必要です。

1. InPrivate ブラウザーの画面を開きます。

1. Azure portal に移動し **syncadmin** ユーザー アカウントを使用してサインインします。プロンプトが表示されたら、このタスクで前に記録したパスワードを **Pa55w.rd1234** に変更します。

    >**注意**: サインインするには、このタスクの前半で記録した Azure AD テナントの DNS ドメイン名を含む、**syncadmin** ユーザー アカウントの完全修飾名を指定する必要があります。このユーザー名のファイル形式は、syncadmin@`<your_tenant_name>`.onmicrosoft.com です。ここで、 `<your_tenant_name>` は、一意の Azure AD テナント名を表すプレースホルダーです。 

1. **syncadmin** としてサインアウトし、InPrivate ブラウザーの画面を閉じます。

> **結果**: この演習を完了したら、Azure AD テナントを作成し、新しい Azure AD テナントにカスタム DNS 名を追加し、グローバル管理者の役割を持つ Azure AD ユーザーを作成しました。


### 演習 3: Active Directory フォレストを Azure Active Directory テナントと同期する

### 予想時間: 20 分

この演習では、次のタスクを行います。

- タスク 1: ディレクトリ同期用の AD DS を準備する
- タスク 2: Azure AD Connect をインストールする
- タスク 3: ディレクトリ同期を確認する

#### タスク 1: ディレクトリ同期用の AD DS を準備する

このタスクでは、AD DS ドメイン コントローラーを実行している Azure VM に接続し、ディレクトリ同期アカウントを作成します。 

   > このタスクを開始する前に、このラボの最初の演習で開始した Template deployment が正常に完了したことを確認します。

1. Azure portal で、このラボの最初の演習で Azure VM をデプロイした Azure サブスクリプションに関連付けられている Azure AD テナントに **ディレクトリ  + サブスクリプション** フィルターを設定します。

1. Azure portal の 「**リソース、サービス、ドキュメントの検索**」 テキスト ボックスで、Azure portal ページの上部に「**Virtual Machines**」と入力し、**Enter** キーを押します。

1. 「**Virtual Machines**」 ブレードで **adVM** エントリをクリックします。 

1. 「**adVM**」 ブレードで 「**接続**」 をクリックし、ドロップ ダウン メニューの 「**RDP**」 をクリックします。 

1. 「**IPアドレス**」 パラメーターで、「**ロード バランサーのパブリック IP アドレス**」 を選択し、「**RDP ファイルのダウンロード**」 をクリックして、リモート デスクトップ経由で **adVM** AzureVM に接続します。認証を求められたら、次の資格情報を入力します。

   |設定|値|
   |---|---|
   |ユーザー名|**student**|
   |パスワード|**Pa55w.rd1234**|

    >**注意**: リモート デスクトップ セッションと **Server Manager** が読み込まれるまで待ちます。  

    >**注意**: **adVM** Azure VM へのリモート デスクトップ セッションでは、次の手順を実行します。 

1. **Server Manager** で 「**Local Server**」 をクリックし、「**IE Enhanced Security Configuration**」 をクリックします。

1. 「**IE Enhanced Security Configuration**」 ダイアログ ボックスで、両方のオプションを 「**Off**」 に設定し、「**OK**」 をクリックします。

1. Internet Explorer を起動して、[https://www.microsoft.com/ja-jp/edge/business/download](`https://www.microsoft.com/en-us/edge/business/download`) に移動します。Microsoft Edge インストール バイナリをダウンロードし、インストールを実行し、既定の設定で Web ブラウザーを構成します。

1. **Server Manager** で、「**Tools**」 をクリックし、ドロップダウン メニューの 「**Active Directory Administratice Center**」 をクリックします。

1. **Active Directory Administratice Center** で、「**adatum (local)**」 をクリックし、「**Tasks**」 ウィンドウの「**adatum (local)**」配下で「**New**」 をクリックして、カスケード メニューで、**Organizational Unit** をクリックします。

1. 「**Organizational Unit**」 ウィンドウの 「**Name**」 テキスト ボックスに「**ToSync**」と入力し、「**OK**」 をクリックします。

1. 新しく作成された **ToSync** 組織単位をダブルクリックして、Active Directory Administratice Center コンソールの詳細ウィンドウにその内容が表示されるようにします。 

1. 「**Tasks**」 ウィンドウの 「**ToSync**」 セクションで、「**New**」 をクリックし、カスケード メニューの 「**User**」 をクリックします。

1. 「**Crreate User**」 ウィンドウで、次の設定で新しいユーザー アカウントを作成し (他のユーザーの既存の値をそのまま使用)、「**OK**」 をクリックします。

   |設定|値|
   |---|---|
   |First name|**aduser1**|
   |User UPN Logon|**aduser1**|
   |User SamAccountName Logon|**aduser1**|
   |Password|**Pa55w.rd1234**|
   |Other password options|**Password never expires**|

#### タスク 2: Azure AD Connect をインストールする

このタスクでは、仮想マシンに AD Connect をインストールします。 

1. **adVM** へのリモート デスクトップ セッション内で Microsoft Edge を使用し、[Azure portal](https://portal.azure.com) に移動して、前の演習で作成した **syncadmin** ユーザー アカウントを使用してサインインします。メッセージが表示されたら、記録した完全なユーザー名と **Pa55w.rd1234** パスワードを指定します。

1. Azure portal の 「**リソース、サービス、ドキュメントの検索**」テキスト ボックスで、Azure portal ページの上部に「**Azure Active Directory**」と入力し、**Enter** キーを押します。

1. Azure portal の **AdatumSync \| Overview**」 ブレードで、「**Azure AD Connect**」 をクリックします。

1. 「**AdatumSync \| Azure AD Connect**」 ブレードで、「**Download Azure AD Connect**」 リンクをクリックします。「**Microsoft Azure Active Directory Connect**」 ダウンロード ページにリダイレクトされます。

1. 「**Microsoft Azure Active Directory Connect**」 ダウンロード ページで、「**Download**」 をクリックします。

1. メッセージが表示されたら、「**Run**」 をクリックして、**Microsoft Azure Active Directory Connect** ウィザードを開始します。

1. **Microsoft Azure Active Directory Connect** ウィザードの 「**Welcome to Azure AD Connect**」 ページで、「**I agree to the license terms and privacy notice.**」 チェック ボックスをオンにし、「**Continue**」 をクリックします。

1. **Microsoft Azure Active Directory Connect** ウィザードの 「**Express Settings**」 ページ で、「**Customize**」 オプションをクリックします。

1. 「**Install required components**」 ページで、すべてのオプション設定を解除し、「**install**」 をクリックします。

1. 「**User sign-in**」 ページで、「**Password Hash Synchronization**」 のみが有効になっていることを確認し、「**Next**」 をクリックします。

1. 「**Connect to Azure AD**」 ページで、前の演習で作成した **syncadmin** ユーザー アカウントの資格情報を使用して認証し、「**次へ**」 をクリックします。 

1. 「**Connect to Azure AD**」 ページで、**adatum.com** フォレスト エントリの右側にある 「**Add Directory**」 ボタンをクリックします。

1. 「**AD forest account**」 ウィンドウで、「**Create new AD account**」 オプションが選択されていることを確認し、次の資格情報を指定して、「**OK**」 をクリックします。

   |設定|値|
   |---|---|
   |ユーザー名|**ADATUM\\Student**|
   |パスワード|**Pa55w.rd1234**|

1. 「**Connect your directories**」 ページに戻り、**adatum.com** エントリが構成されたディレクトリとして表示されていることを確認し、「**Next**」 をクリックします。

1. 「**Azure AD sign-in configuration**」 ページで、**UPN サフィックスが検証済みのドメイン名と一致しない場合は、ユーザーがオンプレミスの資格情報を使用して Azure AD にサインインできないことを示す**警告を確認し、「**Continue without matching all UPN suffixes to cerified domains**」 チェック ボックスを**オン**にし、「**Next**」 をクリックします。

    >**注意**: 前述したように、カスタムの Azure AD DNS ドメイン **adatum.com** を確認できなかったため、これが予想されます。

1. 「**Domain and OU filtering**」 ページで、「**Sync selected domains and OUs**」 のオプションをクリックし、すべてのチェックボックスをオフにして、「**ToSync**」 OU の横にあるチェックボックスのみをクリックして、「**Next**」 をクリックします。

1. 「**Uniquely identifying your users**」 ページで、既定の設定を受け入れ、「**Next**」 をクリックします。

1. 「**Filter users and devices**」 ページで、既定の設定を受け入れて、「**Next**」 をクリックします。

1. 「**Optional features**」 ページで、既定の設定を受け入れ、「**Next**」 をクリックします。

1. 「**Ready to configure**」 ページで、「**Start the synchronization process when configuration completes.**」 チェック ボックスがオンになっていることを確認し、「**Install**」 をクリックします。

    >**注意**: インストールにはおよそ 2 分かかります。

1. 「**Configuration complete**」 ページの情報を確認し、「**Exit**」 をクリック して 「**Microsoft Azure Active Directory Connect**」 ウィンドウを閉じます。


#### タスク 3: ディレクトリ同期を確認する

このタスクでは、ディレクトリ同期が機能していることを確認します。 

1. **adVM** へのリモート デスクトップ セッション内の、Azure portal を表示する Microsoft Edge ウィンドウで 、Adatum Lab Azure AD テナントの「**ユーザー - すべてのユーザー**」ブレードに移動します。

1. 「**ユーザー \| すべてのユーザー**」ブレードで、ユーザー オブジェクトのリストに **aduser1** アカウントが含まれていることを確認してください。 

1. **aduser1** アカウントを選択し、「**プロファイル > ID**」セクションで、「**ソース**」属性が **Windows Server AD** に設定されていることを確認してください。

    >**注**: **aduser1** ユーザー アカウントが表示されるまで、数分待ってから 「**更新**」を選択する必要がある場合があります。

1. 「**ユーザー \| すべてのユーザー**」 ブレードで **aduser1** エントリを選択します。

1. 「**aduser1 \| プロファイル**」ブレードの 「**ジョブ情報**」セクションで、「**部署**」属性が設定されていません。

1. **adVM** へのリモート デスクトップ セッションで、**Active Directory 管理センター** に切り替え、**ToSync** OU のオブジェクトのリストで **aduser1** エントリを選択し、「**タスク**」ペインの「**ToSync**」セクションで、「**プロパティ**」を選択します。

1. 「**aduser1**」ウィンドウの「**Organization**」セクションの「**Department**」テキスト ボックスに「**Sales**」と入力し、「**OK**」をクリックします。

1. **adVM** へのリモート デスクトップ セッション内で、**Windows PowerShell** を起動します。

1. 「**Administrator: Windows PowerShell**」 コンソールから、次のコマンドを実行して、Azure AD Connect の累計同期を開始します。

    ```powershell
    Import-Module -Name 'C:\Program Files\Microsoft Azure AD Sync\Bin\ADSync\ADSync.psd1'

    Start-ADSyncSyncCycle -PolicyType Delta
    ```

1. 「**aduser1 \| Profile**」 ブレードを表示している Microsoft Edge ウィンドウに切り替え、ページを更新して、「**Department**」 プロパティが 「**Sales**」 に設定されていることを確認します。

    >**注意**: 「**Department**」 属性が設定されていない場合は、もう 1 分待ってからページを再度更新する必要がある場合があります。

> **結果**: この演習を完了したら、ディレクトリ同期用の AD DS を準備し、Azure AD Connect をインストールし、ディレクトリ同期を検証しました。


**リソースをクリーン アップします**

>**注意**: Azure AD 同期を無効にして開始する

1. **adVM** へのリモート デスクトップ セッション内で、管理者として Windows PowerShell を起動します。

1. Windows PowerShell コンソールから、次のコマンドを実行して MsOnline PowerShell モジュールをインストールします (メッセージが表示されたら、「続行するには NuGet プロバイダーが必要です」 ダイアログ ボックスで、「**Yes**」 と入力して Enter キーを押します)。

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
    Install-Module MsOnline -Force
    ```

1. Windows PowerShell コンソールから、次のコマンドを実行して AdatumSync Azure AD テナントに接続します (メッセージが表示されたら、**syncadmin** の資格情報でサインインします)。

    ```powershell
    Connect-MsolService
    ```

1. Windows PowerShell コンソールから、次のコマンドを実行して Azure AD Connect の同期を無効にします。

    ```powershell
    Set-MsolDirSyncEnabled -EnableDirSync $false -Force
    ```

1. Windows PowerShell コンソールで、次の操作を実行して操作が正常に完了したことを確認します。

    ```powershell
    (Get-MSOLCompanyInformation).DirectorySynchronizationEnabled
    ```

    >**注意**: 結果は「False」にする必要があります。そうでない場合は、少し待ってからコマンドを再実行してください。

    >**注意**: 次に、Azure リソースを削除します

1. Azure portal で、**Directory + Subscription** フィルターを、**adVM** Azure VM をデプロイした Azure サブスクリプションに関連付けられている Azure AD テナントに設定します。

1. Azure portal から、Azure portal の右上にあるアイコンをクリックして、 Cloud Shell を開きます。 

1. Cloud Shell ペインの左上隅にあるドロップダウン メニューで 「**PowerShell**」を選択し、メッセージが表示されたら 「**確認**」をクリックします。 

1. Cloud Shell ペイン内の PowerShell セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500LAB06" -Force -AsJob
    ```
1. **「Cloud Shell」** ウィンドウを閉じます。

    >**注意**: 最後に、Azure AD テナントを削除します。
    
    >**注 2**: テナントの削除は非常に難しいプロセスであるため、誤ってまたは悪意を持って削除されることはありません。  つまり、このラボの一部としてテナントを削除しても、うまくいかないことがよくあります。  ここにはテナントを削除する手順がありますが、このラボを完了したと考える必要はありません。現実の世界でテナントを削除する必要がある場合は、DOCS.Microsoft.com に役立つ記事があります。

1. Azure portal に戻り、**ディレクトリ + サブスクリプション** フィルターを使用して **AdatumSync** Azure Active Directory テナントに切り替えます。

1. Azure portal で、「**ユーザー \| すべてのユーザー**」 ブレードに移動し、**syncadmin** ユーザー アカウントを表すエントリをクリックし、「**syncadmin \| プロファイル**」 ブレードで 「**削除**」 をクリックして、確認を求められたら、「**OK**」 をクリックします。

1. **aduser1** ユーザー アカウントを削除するには、同じ手順を繰り返します。

1. Azure AD テナントの 「**AdatumSync \| 概要**」 ブレードに移動し、「**テナントの削除**」 をクリックし、「**'AdatumSync' ディレクトリの削除**」 ブレードで 「**Azure リソースを削除するアクセス許可を取得します**」 のリンクをクリックし、Azure Active Directory の 「**プロパティ**」 ブレードの 「**Azure リソースのアクセス管理**」 で 「**はい**」 をクリックして、「**保存**」 をクリックします。

1. Azure portal からサインアウトし、サインインし直します。 

1. 「**'AdatumSync' ディレクトリの削除**」 ブレードに戻り、「**削除**」 をクリックします。

> このタスクに関する追加情報については、[https://docs.microsoft.com/ja-jp/azure/active-directory/users-groups-roles/directory-delete-howto](https://docs.microsoft.com/ja-jp/azure/active-directory/users-groups-roles/directory-delete-howto) を参照してください。

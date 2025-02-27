---
lab:
    title: '08 - Azure Firewall'
    module: 'モジュール 02 プラットフォーム保護の実装'
---

# 課題 08: Azure Firewall
# 受講生用ラボ マニュアル

## ラボ シナリオ

Azure Firewall のインストールを求められました。これは、組織が全体的なネットワーク セキュリティ計画の重要な部分である受信および送信ネットワーク アクセスを制御するのに役立ちます。具体的には、次のインフラストラクチャ コンポーネントを作成してテストします。

- ワークロード サブネットとジャンプ ホスト サブネットを持つ仮想ネットワーク。
- 仮想マシンは各サブネットです。 
- ワークロード サブネットからのすべてのアウトバウンド ワークロード トラフィックがファイアウォールを使用することを保証するカスタム ルート。
- www.bing.com への送信トラフィックのみを許可するファイアウォール アプリケーション ルール。 
- 外部 DNS サーバーのルックアップを許可するファイアウォール ネットワーク ルール。

> このラボのすべてのリソースについて、**米国東部** リージョンを使用しています。クラスに使用する地域であることを講師に確認します。 

## ラボの目的

このラボでは、次の演習を行います。

- 演習 1: Azure Firewall をデプロイしてテストする

## ラボ ファイル:

- **\\Allfiles\\Labs\\08\\template.json**

### 演習 1: Azure Firewall をデプロイしてテストする

### 予想時間: 40 分

> このラボのすべてのリソースに対して、**米国東部** リージョンを使用しています。クラスで使用する地域であることを講師に確認します。 

この演習では、次のタスクを行います。

- タスク 1: テンプレートを使用してラボ環境を展開します。 
- タスク 2: Azure Firewall をデプロイします。
- タスク 3: 既定のルートを作成します。
- タスク 4: アプリケーション ルールを構成します。
- タスク 5: ネットワーク ルールを構成します。 
- タスク 6: DNS サーバーを構成します。
- タスク 7: ファイアウォールをテストします。 

#### タスク 1: テンプレートを使用してラボ環境を展開します。 

このタスクでは、ラボ環境を確認してデプロイします。 

このタスクでは、ARM テンプレートを使用して仮想マシンを作成します。この仮想マシンは、このラボの最後の演習で使用されます。 

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注**: このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure ポータルにサインインします。

1. Azure portal で Azure portal ページの上部にある **「リソース、サービス、ドキュメントの検索」** テキスト ボックスで、**「カスタム テンプレートのデプロイ」** と入力し、**「Enter」** キーを押します。

1. **カスタム デプロイ** ブレードで、**エディターで独自のテンプレートを作成する** のオプションをクリックします。

1. 「**テンプレートの編集**」 ブレードで、「**ファイルの読み込み**」 をクリックし、**\\Allfiles\\Labs\\08\\template.json** ファイルを見つけて、「**開く**」 をクリックします。

    >**注**: テンプレートの内容を確認し、Windows Server 2019 の Datacenter をホストする Azure VM をデプロイしていることを確認してください。

1. **「テンプレートの編集」** ブレードで、**「保存」** をクリックします。

1. **「カスタム デプロイ」** ブレードで、次の設定が構成されていることを確認します (既定値を使用して他の設定を行います)。

   |設定|値|
   |---|---|
   |サブスクリプション|このラボで使用する Azure サブスクリプションの名前|
   |リソース グループ|「**新規作成**」 をクリックして、「**AZ500Lab08**」という名前を入力します|
   |場所|**米国東部**|

    >**注**: Azure VM をプロビジョニングできる Azureリージョンを特定するには、次を参照してください。[**https://azure.microsoft.com/ja-jp/regions/offers/**](https://azure.microsoft.com/ja-jp/regions/offers/)

1. **「確認と作成」** をクリックし、**「作成」** をクリックします。

    >**注**: デプロイが完了するのを待ちます。これにはおよそ 2 分かかります。 

#### タスク 2: Azure Firewall をデプロイする

このタスクでは、Azure Firewall を Virtual Network にデプロイします。 

1. Azure portal で、Azure portal ページの上部にある 「**リソース、サービス、ドキュメントの検索**」 テキスト ボックスに「**ファイアウォール**」と入力し、**Enter** キーを押します。

1. 「**ファイアウォール**」 ブレードで、「**+ 追加**」 をクリックします。

1. 「**ファイアウォールの作成**」ブレードの 「**基本**」タブで、次の設定を指定します (他の設定は既定値のままにします)。

   |設定|値|
   |---|---|
   |リソース グループ|**AZ500Lab08**|
   |名前|**Test-FW01**|
   |地域|**米国東部**|
   |ファイアウォール管理|**ファイアウォール規則 (クラシック) を使用してこのファイアウォールを管理する**|
   |仮想ネットワークの選択|「**既存のものを使用**」 オプションをクリック し、ドロップダウン リストで 「**Test-FW-VN**」 を選択します|
   |パブリック IP アドレス|「**新規追加**」 をクリックし、「 **Test-FW-PIP**」という名前を入力し、「**OK**」 をクリックします。|

1. **「確認および作成」** をクリックし、**「作成」** をクリックします。 

    >**注**: デプロイが完了するのを待ちます。これにはおよそ 5 分かかります。 

1. Azure portal の、Azure portal ページの上部にある **「ソース、サービス、ドキュメントの検索」** テキスト ボックスで、 **「リソース グループ」**と入力し、**「Enter」** キーを押します。

1. 「**リソース グループ**」 ブレードのリソース グループの一覧で 、「**AZ500Lab08**」 エントリをクリックします。

    >**注**: 「**AZ500Lab08**」 リソース グループ ブレードで、リソースのリストを確認します。「**種類**」 で並べ替えることができます。

1. リソースの一覧で 、「**Test-FW01**」 ファイアウォールを表すエントリをクリックします。

1. 「**Test-FW01**」 ブレードで、ファイアウォールに割り当てられた **プライベート IP** アドレスを特定します。 

    >**注**: この情報は次のタスクで必要になります。


#### タスク 3: デフォルト ルートを作成する

このタスクでは、**Workload-SN** サブネットの既定のルートを作成します。このルートは、ファイアウォールを経由する送信トラフィックを構成します。

1. Azure portal で、Azure portal ページの上部にある 「**リソース、サービス、ドキュメントの検索**」 テキスト ボックスに「**ルート テーブル**」と入力し、**Enter** キーを押します。

1. 「**ルート テーブル**」ブレードで、「**+ 新規**」をクリックします。

1. 「**ルート テーブルの作成**」 ブレードで、次の設定を指定します。

   |設定|値|
   |---|---|
   |リソース グループ|**AZ500Lab08**|
   |Region| "East US"|
   |名前|**Firewall-route**|

1. 「**確認および作成**」 をクリックし、「**作成**」 をクリックして、保存が完了するのを待ちます。 

1. 「**ルート テーブル**」 ブレードで 「**更新**」 をクリックし、ルート テーブルのリストで 「**Firewall-route**」 エントリをクリックします。

1. 「**Firewall-route**」 ブレードの 「**設定**」 セクションで、「**サブネット**」 をクリックし、次に 「**Firewall-route \| サブネット**」 ブレードで、「**+ 関連付け**」 をクリックします。

1. 「**サブネットの関連付け**」 ブレードで、次の設定を指定します。

   |設定|値|
   |---|---|
   |バーチャル ネットワーク|**Test-FW-VN**|
   |サブネット|**Workload-SN**|

    >**注**: このルートに **Workload-SN** サブネットが選択されていることを確認してください。選択されていない場合、ファイアウォールは正しく機能しません。

1. 「**OK**」 をクリックして、ファイアウォールを仮想ネットワーク サブネットに関連付けます。 

1. 「**Firewall-route**」 ブレードに戻り 、「**設定**」 セクションで 「**ルート**」 をクリックし、「**+ 追加**」 をクリックします。 

1. 「**ルートの追加**」 ブレードで、次の設定を指定します。  

   |設定|値|
   |---|---|
   |ルート名|**FW-DG**|
   |アドレス プレフィックス|**0.0.0.0/0**
   |次のホップのタイプ|**仮想アプライアンス**|
   |次のホップ アドレス|前のタスクで特定したファイアウォールのプライベート IP アドレス|

    >**注**: Azure Firewall は実際には管理サービスですが、この状況では仮想アプライアンスが機能します。
	
1.  「**OK**」 をクリックしてルートを追加します。 


#### タスク 4: アプリケーション ルールを構成する

このタスクでは、`www.bing.com` への送信アクセスを許可するアプリケーション ルールを作成します。

1. Azure portal で、**Test-FW01** ファイアウォールに戻ります。

1. 「**Test-FW01**」ブレードの「**設定**」セクションで、「**ルール (従来)**」をクリックします。

1. 「**Test-FW01 \| ルール (従来)**」ブレードで、「**アプリケーション ルール コレクション**」タブをクリックし、「**+ アプリケーション ルール コレクションの追加**」をクリックします。

1. 「**アプリケーション ルール コレクションの追加**」 ブレードで、次の設定を指定します (他の設定は既定値のままにします)。

   |設定|値|
   |---|---|
   |名前|**App-Coll01**|
   |優先度|**200**|
   |アクション|**許可**|

1. 「**アプリケーション ルール コレクションの追加**」 ブレードで、「**ターゲットのFQDN**」 セクションに次の設定で新しいエントリを作成します (他の設定は既定値のままにします)。

   |設定|値|
   |---|---|
   |名前|**AllowGH**|
   |ソース タイプ|**IP アドレス**|
   |ソース|**10.0.2.0/24**|
   |プロトコル ポート|**http:80、 https:443**|
   |ターゲット FQDN|**www.bing.com**|

1. 「**追加**」 をクリックして、ターゲット FQDN ベースのアプリケーション ルールを追加します。

    >**注**: Azure Firewall には、既定で許可されているインフラストラクチャ FQDN 用の組み込みのルール コレクションが含まれています。これらの FQDN はプラットフォームに固有のものであり、他の目的には使用できません。 

#### タスク 5: ネットワーク ルールを構成する

このタスクでは、ポート 53 (DNS) の 2 つの IP アドレスへの送信アクセスを許可するネットワーク規則を作成します。

1. Azure portal で、「**Test-FW01 \| Rules (従来)**」ブレードに戻ります。

1. 「**Test-FW01 \| ルール (従来)**」ブレードで、「**ネットワーク ルール コレクション**」タブをクリックし、「**+ ネットワーク ルール コレクションを追加**」をクリックします。

1. 「**ネットワーク ルール コレクションの追加**」 ブレードで、次の設定を指定します (他の設定は既定値のままにします)。

   |設定|値|
   |---|---|
   |名前|**Net-Coll01**|
   |優先度|**200**|
   |アクション|**許可**|

1. 「**ネットワーク ルール コレクションの追加**」 ブレードで、「**IP アドレス**」 セクションに次の設定で新しいエントリを作成します (他の設定は既定値のままにします)。

   |設定|値|
   |---|---|
   |名前|**AllowDNS**|
   |プロトコル|**UDP**|
   |Source type|**IP アドレス**|
   |Source|**10.0.2.0/24**|
   |Destination type|**IP アドレス**|
   |宛先アドレス|**209.244.0.3,209.244.0.4**|
   |宛先ポート|**53**|

1. 「**追加**」 をクリック して、ネットワーク ルールを追加します。

    >**注**: この場合に使用される宛先アドレスは、既知のパブリック DNS サーバーです。 

#### タスク 6: 仮想マシンの DNS サーバーを構成する

このタスクでは、仮想マシンのプライマリおよびセカンダリ DNS アドレスを構成します。これはファイアウォール要件ではありません。 

1. Azure portal で、**AZ500Lab08** リソース グループに戻ります。

1. 「**AZ500Lab08**」 ブレードのリソースの一覧で **Srv-Work** 仮想マシンをクリックします。

1. 「**Srv-Work**」 ブレードの 「**設定**」 セクションで、「**ネットワーク**」 をクリックします。

1. 「**Srv-Work \| ネットワーク**」 ブレードで、「**ネットワーク インターフェイス**」 エントリの横にあるリンクをクリックします。

1. ネットワーク インターフェイス ブレードの 「**設定**」 セクションで 「**DNS サーバー**」の「**カスタム**」 オプションを選択して、ネットワーク ルールで参照されている 2 つの DNS サーバー: **209.244.0.3** および **209.244.0.4** を追加し、「**保存**」 をクリックして変更を保存します。

1. **Srv-Work** 仮想マシンのページに戻ります。

    >**注**: 更新が完了するまで待ちます。

    >**注**: ネットワーク インターフェイスの DNS サーバーを更新すると、そのインターフェイスが接続されている仮想マシンが自動的に再起動され、該当する場合は、同じ可用性セット内の他の仮想マシンが再起動されます。

#### タスク 7: ファイアウォールをテストする

このタスクでは、ファイアウォールをテストして、期待どおりに機能することを確認します。

1. Azure portal で、**AZ500Lab08** リソース グループに戻ります。

1. 「**AZ500Lab08**」 ブレードのリソースのリストで、**Srv-Jump** 仮想マシンをクリックします。

1. 「**Srv-Jump**」 ブレードで 「**接続**」 をクリックし、ドロップダウン メニューの 「**RDP**」 をクリックします。 

1. 「**RDP ファイルのダウンロード**」 をクリックし、それを使用してリモートデスクトップ経由で **Srv-Jump** Azure VM に接続します。認証を求められたら、次の資格情報を入力します。

   |設定|値|
   |---|---|
   |ユーザー名|**localadmin**|
   |パスワード|**Pa55w.rd1234**|

    >**注**: 次の手順は、**Srv-Jump** Azure VM へのリモート デスクトップ セッションで実行されます。 

    >**注**: **Srv-Work** 仮想マシンに接続します。これは、bing.com の Web サイトにアクセスする機能をテストできるように行われています。  

1. **Srv-Jump** へのリモート デスクトップ セッション内で、「**スタート**」 を右クリックし、右クリック メニューで 「**Run**」 をクリックし、 「**Run**」 ダイアログ ボックスで、次のコマンドを実行して **Srv-Work** に接続します。 

    ```
    mstsc /v:Srv-Work
    ```

1. 認証のプロンプトが表示されたら、次の資格情報を入力します。

   |設定|値|
   |---|---|
   |ユーザー名|**localadmin**|
   |パスワード|**Pa55w.rd1234**|

    >**注**: リモート デスクトップ セッションが確立され、サーバー マネージャー インターフェイスが読み込まれるまで待ちます。

1. 「**Srv-Work**」 へのリモート デスクトップ セッションの 「**Server Manager**」 で 「**Local Server**」 をクリックし 、「**IE Enhanced Security Configuration**」 をクリックします。

1. 「**IE Enhanced Security Configuration**」 ダイアログ ボックスで、両方のオプションを 「**Off**」 に設定し、「**OK**」 をクリックします。

1. **Srv-Work** へのリモート デスクトップ セッション内で 、Internet Explorer を起動し、**`https://www.bing.com`** を参照します。 

    >**注**: Web サイトが正常に表示されます。ファイアウォールでは、アクセスできます。

1. **`http://www.microsoft.com/`** を参照する

    >**注**: ブラウザー ページ内で、次のようなテキストを含むメッセージが表示されます。`HTTP request from 10.0.2.4:xxxxx to microsoft.com:80. Action: Deny. No rule matched. Proceeding with default action` ファイアウォールがこの Web サイトへのアクセスをブロックしているため、これは予期された動作です。 

1. 両方のリモート デスクトップ セッションを終了します。

> 結果: Azure Firewall の構成とテストが正常に完了しました。

**リソースをクリーン アップする**

> 新しく作成した Azure リソースのうち、使用しないリソースは必ず削除してください。使用していないリソースを削除することで、予期しないコストが発生しなくなります。 

1. Azure portal から、Azure portal の右上にあるアイコンをクリックして、 Cloud Shell を開きます。メッセージが表示されたら、「**PowerShell**」 と 「**ストレージの作成**」 をクリックします。

1. 「Cloud Shell」 ウィンドウの左上隅にあるドロップダウン メニューで 「**PowerShell**」 が選択されていることを確認します。

1. 「クラウド シェル」 ウィンドウ内の 「PowerShell」 セッションで、次の手順を実行して、このラボで作成したリソース グループを削除します。
  
    ```powershell
    Remove-AzResourceGroup -Name "AZ500Lab08" -Force -AsJob
    ```
1. **「Cloud Shell」** ペインを閉じます。 

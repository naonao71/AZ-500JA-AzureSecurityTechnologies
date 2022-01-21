---
lab:
    title: '14 - Microsoft Defender for Cloud'
    module: 'モジュール 04 - Microsoft Defender for Cloud'
---

# ラボ 14: Microsoft Defender for Cloud
# 受講生用ラボ マニュアル

## ラボのシナリオ

Microsoft Defender for Cloud ベースの環境の概念の証明を作成するように依頼されました。具体的には、次のことを行います。

- 仮想マシンを監視するように Microsoft Defender for Cloud を構成します
- 仮想マシンに対する Microsoft Defender for Cloud の推奨事項を確認します。
- ゲスト構成と Just In Time VM アクセスの推奨事項を実装します。 
- セキュア スコアを使用して、より安全なインフラストラクチャの作成に向けた進捗状況を判断する方法を確認します。

> このラボのすべてのリソースについて、**米国東部** リージョンを使用しています。クラスに使用する地域であることを講師に確認します。 

## ラボの目的

このラボでは、次の演習を行います。

- 演習 1: Microsoft Defender for Cloud を実装する

### 演習 1: Microsoft Defender for Cloud を実装する

この演習では、次のタスクを行います。

- タスク 1: Microsoft Defender for Cloud を構成する
- タスク 2: Microsoft Defender for Cloud の推奨事項を確認する
- タスク 3: Microsoft Defender for Cloud の推奨事項を実装して、ジャスト イン タイム VM アクセスを有効化する

#### タスク 1: Microsoft Defender for Cloud を構成する

このタスクでは、Microsoft Defender for Cloud をオンボーディングおよび構成します。

1. Azure portal **`https://portal.azure.com/`** にサインインします。

    >**注意**: このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure ポータルにサインインします。

1. Azure portal で Azure portal ページの上部にある「**リソース、サービス、ドキュメントの検索**」テキスト ボックスで、「**Microsoft Defender for Cloud**」と入力し、**Enter** キーを押します。

1. 「**Microsoft Defender for Cloud \| 「概要**」ブレードで、「**アップグレード**」をクリックします。
     
1. 「**Microsoft Defender for Cloud \| 「概要**」ブレードの「**エージェントのインストール**」タブで、下にスクロールして「**エージェントのインストール**」をクリックします。

1. 「**Microsoft Defender for Cloud \| 概要**」ブレードの「**アップグレード**」タブで、「セキュリティ機能が強化されたワークスペースを選択し、**Microsoft Defender プラン**をオンにします。 

    >**注**: Microsoft Defender プランの一部として利用できるすべての機能を確認します。 

1. 「**Defenderプラン**」ブレードで、「**すべての Microsoft Defender for Cloud プランを有効にする**」を選択し、「**保存**」をクリックします。

1. **Microsoft Defender for Cloud** に移動し、左側の垂直メニューバーの「管理設定」の下にある「**環境設定**」をクリックします。

1. 「**Microsoft Defender for Cloud \ | 環境設定**」ブレードで、関連するサブスクリプションをクリックします。 

1. 「**設定 | Defender プラン**」ブレードの左側にある垂直方向のメニュー バーで、「**自動プロビジョニング**」をクリックします。

1. 「**設定 | 自動プロビジョニング**」ブレードで、最初の項目である「**Azure VM の Log Analytics エージェント**」の自動プロビジョニングが**オン**に設定されていることを確認します。

1. 「**設定 \| ワークフローの自動化**」ブレードで「**+ ワークフローの自動化の追加**」をクリックします。

1. 「**ワークフロー自動化の追加**」 ブレードで、使用可能な設定を確認します。 

    > **注**: 脅威検出アラートおよび Microsoft Defender for Cloud の推奨事項に基づいて、アクションをトリガーできます。Logic Apps に基づいてアクションを構成することもできます。 

1. 「**ワークフロー自動化の追加**」ブレードで、「**キャンセル**」をクリックします。

    > **注**: Microsoft Defender for Cloud は、システム更新プログラムの状態、OS セキュリティ構成、エンドポイント保護を含む仮想マシンに関する多く分析情報を提供します。

1. **Microsoft Defender for Cloud**」ブレードに戻り、前のラボで作成した Log Analytics ワークスペースを表すエントリをクリックします。

1. 「**設定 \| Microsoft Defender プラン**」ブレードで、「**Microsoft Defender オン**」が選択されていることを確認し、「**保存**」をクリックします。


#### タスク 2: Microsoft Defender for Cloud の推奨事項を確認する

このタスクでは、Microsoft Defender for Cloud の推奨事項を確認します。 

1. Azure portal で、「**Microsoft Defender for Cloud \| 概要**」ブレードに戻ります。 

1. 「**Microsoft Defender for Cloud \| 概要**」ブレードで、「**セキュア スコア**」タイルを確認します。

    >**注意**: あれば、現在のスコアを記録します。

1. 「**Microsoft Defender for Cloud \| 概要**」ブレードに戻り、「**評価済みリソース**」を選択します。

1. 「**インベントリ**」ブレードで、「**myVM**」エントリを選択します。

    > **注**: エントリが表示されるまで数分待ってから、ブラウザ ページを更新する必要がある場合があります。
    
1. 「**リソース正常性**」ブレードの「**推奨事項**」タブで、**myVM** の推奨事項のリストを確認します。


#### タスク 3: Microsoft Defender for Cloud の推奨事項を実装して、ジャスト イン タイム VM アクセスを有効化する

このタスクでは、Microsoft Defender for Cloud の推奨事項を実装して、仮想マシン上でジャスト イン タイム VM アクセスを有効化します。 

1. Azure portal で、「**Microsoft Defender for Cloud \| 概要**」ブレードに戻り、「**Azure Defender**」タイルを選択します。

1. 「**ワークロード保護**」ブレードの「**高度な保護**」セクションで「**Just-In-Time VM アクセス**」タイルをクリックし、「**Just-In-Time VM アクセス**」ブレードで「**Just In Time VM アクセスを試す**」をクリックします。

**注:** VM が表示されない場合は、「**仮想マシン**」ブレードに移動して、「**構成**」をクリックし、「**Just-In-Time VM アクセス**」の下にある「**Just In Time VM を有効にする**」オプションをクリックします。上記の手順を繰り返して **Microsoft Defender for Cloud** に戻り、ページを更新すると、VM が表示されます。

1. 「**Just In Time VM アクセス**」で、「**構成されていません**」タブを選択し、**myVM** エントリをクリックします。

    > **注**: **myVM** エントリが利用可能になるまで、数分待つ必要があるかもしれません。

1. 「**1台の VM で JIT を有効にする**」をクリックします。

1. 「**JIT VM アクセス構成**」 ブレードの、ポート **22** を参照する行の右端で、省略記号ボタンをクリックしてから、「**削除**」 をクリックします。

1. 「**JIT VM アクセス構成**」 ブレードで、「**保存**」 をクリックします。

    >**注意**: ツールバーの 「**通知**」 アイコンをクリックして、「**通知**」 ブレードを表示して、構成の進行状況を監視します。 

    >**注意**: このラボでの推奨事項の実装がセキュア スコアに反映されるまでに時間がかかる場合があります。セキュア スコアを定期的にチェックして、これらの機能の実装による影響を判断します。 

> 結果: Microsoft Defender for Cloud をオンボーディングし、仮想マシンの推奨事項を実装しました。 


>**注意**: Azure Sentinel ラボで必要ですので、リソースをこのラボから削除しないでください。

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

> このラボのすべてのリソースについて、**米国東部** リージョンを使用しています。これがクラスで使用するリージョンであることを講師に確認します。 

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

    > **注**: このラボで使用している Azure サブスクリプションで所有者ロールまたは共同作成者ロールを持つアカウントを使用して Azure portal にサインインします。

1. Azure portal で Azure portal ページの上部にある「**リソース、サービス、ドキュメントの検索**」テキスト ボックスで、「**Microsoft Defender for Cloud**」と入力し、**Enter** キーを押します。

1. 「**Microsoft Defender for Cloud**」ブレードで、「**アップグレード**」をクリックします。
     
1. 「**Microsoft Defender for Cloud**」の管理セクションの「環境設定」で、サブスクリプションを表すエントリをクリックします。

1. 「**設定 \| Defenderプラン**」ブレードで、Defenderプランの状態を確認します。 

    > **注**: Azure Defender レベルの一部として使用できるすべての機能を確認し、各リソースの種類で Azure Defender がオンになっていることを確認します。 

1. 変更を行った場合は、「**保存**」をクリックします。

1. 「**設定 \| Defenderプラン**」ブレードで、「**自動プロビジョニング**」をクリックします。

1. 「**Azure VM の Log Analytics エージェント/Azure VM の Azure Monitor エージェント (プレビュー)**」の状態を確認します。オンにした場合は、自動的にエージェントがインストールされます。 

1. 「**設定 \| Defenderプラン**」ブレードに戻り、「**ワークフローの自動化**」をクリックします。

1. 「**設定 \| ワークフローの自動化**」ブレードで「**+ ワークフローの自動化の追加**」をクリックします。

1. 「**ワークフロー自動化の追加**」ブレードで、使用可能な設定を確認します。 

    > **注**: 脅威検出アラートおよび Microsoft Defender for Cloud の推奨事項に基づいて、アクションをトリガーできます。Logic Apps に基づいてアクションを構成することもできます。 

1. 「**ワークフロー自動化の追加**」ブレードで、「**キャンセル**」をクリックします。

    > **注**: Microsoft Defender for Cloud は、システム更新プログラムの状態、OS セキュリティ構成、エンドポイント保護を含む仮想マシンに関する多く分析情報を提供します。

#### タスク 2: Microsoft Defender for Cloud の推奨事項を確認する

このタスクでは、Microsoft Defender for Cloud の推奨事項を確認します。 

1. Azure portal で、「**Microsoft Defender for Cloud \| 概要**」ブレードに戻ります。 

1. 「**Microsoft Defender for Cloud \| 概要**」ブレードで、「**セキュリティ態勢**」タイルを確認します。

    > **注**: あれば、現在のスコアを記録します。

1. 「**Microsoft Defender for Cloud \| 概要**」ブレードに戻り、「**インベントリ**」を選択します。

1. 「**インベントリ**」ブレードで、「**myVM**」エントリを選択します。

    > **注**: エントリが表示されるまで数分待ってから、ブラウザ ページを更新する必要がある場合があります。
    
1. 「**推奨事項**」タブで、**myVM** の推奨事項のリストを確認します。


#### タスク 3: Microsoft Defender for Cloud の推奨事項を実装して、ジャスト イン タイム VM アクセスを有効化する

このタスクでは、Microsoft Defender for Cloud の推奨事項を実装して、仮想マシン上でジャスト イン タイム VM アクセスを有効化します。 

1. Azure portal で、「**Microsoft Defender for Cloud \| 概要**」ブレードに戻り、「**クラウド セキュリティ**」タイルの下で、「**ワークロード保護**」を選択します。

1. 「**ワークロード保護**」ブレードの「**高度な保護**」セクションで「**Just-In-Time VM アクセス**」タイルをクリックします。

1. 「**Just In Time VM アクセス**」で、「**構成されていません**」を選択し、**myVM** エントリをチェックします。

    > **注**: **myVM** エントリが利用可能になるまで、数分待つ必要があるかもしれません。

1. 「**1つの VM で JIT を有効にする**」を選択します。

1. 「**JIT VM アクセス構成**」ブレードの、ポート **22** を参照する行の右端で、省略記号ボタンをクリックしてから、「**削除**」をクリックします。同様に、ポートの**5985**と**5986**を削除します。

1. 「**JIT VM アクセス構成**」ブレードで、「**保存**」をクリックします。

    > **注**: ツールバーの「**通知**」アイコンをクリックして、「**通知**」ブレードを表示して、構成の進行状況を監視します。 

    > **注**: このラボでの推奨事項の実装がセキュア スコアに反映されるまでに時間がかかる場合があります。セキュア スコアを定期的にチェックして、これらの機能の実装による影響を判断します。 

> 結果: Microsoft Defender for Cloud をオンボーディングし、仮想マシンの推奨事項を実装しました。 


> **注**: Microsoft Sentinel ラボで必要ですので、リソースをこのラボから削除しないでください。

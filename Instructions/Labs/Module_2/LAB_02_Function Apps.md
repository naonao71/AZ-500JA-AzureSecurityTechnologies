---
lab:
    title: 'ラボ 2 - 関数アプリ'
    module: 'モジュール 2 - プラットフォーム保護を実装する'
---

# モジュール 2：ラボ 2 - 関数アプリ


Azure Function Apps は Azure App Service インフラストラクチャを使用します。このトピックでは、Azure portal で関数アプリを作成する方法を示します。関数アプリは、個々の関数の実行をホストするコンテナーです。App Service ホスティングプランで関数アプリを作成すると、関数アプリは App Service のすべての機能を使用できます。

## 演習 1：関数とトリガーを作成する

### タスク 1：ラボの設定

1.  PowerShell を開いて、次のコマンドを実行します。

     ```powershell
    start "https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoftLearning%2FAZ-500-Azure-Security%2Fmaster%2FAllfiles%2FLabs%2FMod2_Lab02%2Ftemplate.json" 
     ```

2.  [リソース グループ]セクションで **新規作成** をクリックします。
3.  **myResourceGroup** を名前として入力し、**OK** をクリックします。
4.  ブレードの最下部にあるチェックボックスを選択して、ご契約条件に同意します。
5.  **購入** を選択します。


### タスク 2：関数アプリに HTTP トリガーを追加します

1.  リソース グループを選択します。

1.  ラボのセットアップで作成したリソース グループを選択します

1.  リソース グループで作成された関数アプリ テンプレートを選択します
警告
**注記**：現在、関数アプリに割り当てられている関数はありません


1.  **Functions** をクリックします。

1.  **新しい関数** をクリックし、

1.  右上で、Experimental Language サポートのスライドボタンをクリックし、
警告
**注意**：他の言語がトリガーに追加されました


1.  **HTTP トリガー** を選択します。

1.  言語を **PowerShell** に変更します。

1.  名前をデフォルトのままにして、

1.  **認可** レベルが関数に設定されていることを確認し、

1.  **作成** をクリックします。

 テンプレートコードがページを最新の情報に更新しない場合、これでテンプレート PS1 HTTPトリガーが作成されました。 

### タスク 3：HTTP トリガーへの REST 呼び出しをテストする

1.  関数アプリの名前に注意してください（**関数アプリ アイコンの横**）。

1.  **HTTPトリガー** 関数で、**管理** をクリックします。

1.  関数キーで、デフォルトの関数キーのアクションにあるコピーを選択し、これをメモ帳ファイルに保存します

1.  **PowerShell ISE** を開き、以下の **URL** からPowerShellコードをコピーします

     ```cli
    https://github.com/GoDeploy/AZ500/blob/master/AZ500%20Mod2%20Lab%202/RESTgetHTTPtrigger.ps1
     ```

1.  変数 $ functionappname = "" に関数アプリの名前を入力します

1.  前の手順でポータルからコピーした長い関数キーを変数 $ functionkey = "" に設定します

1.  PowerShell スクリプトを実行する

1.  **ISE** の結果では、次の出力が表示されるはずです

    ```powershell
    This is result of  a normal GET operation calling your HTTP trigger
    Hello 

    This is result of  a normal GET operation calling your HTTP trigger with an extra parameter passed to the trigger
    Hello World!

    This is result of a normal PUT operation calling your HTTP trigger that feeds a hash table converted to JSON to the HTTP triggger
    Hello Max Power
    ```

**結果**：HTTP トリガー機能アプリを正常に構築し、REST ベースのコマンドを使用して通信しました。


| 警告：続行する前に、このラボで使用したすべてのリソースを削除する必要があります。  **Azure Portal** でこれを行うには、**リソース グループ** をクリックします。  作成したリソース グループを選択します。  リソース グループ ブレードで、**リソース グループを削除**をクリックし、リソース グループ名を入力して、**削除** をクリックします。  作成した可能性のある追加のリソース グループに対してプロセスを繰り返します。**これを行わないと、他のラボで問題が発生する可能性があります。** |
| --- |

**お疲れさまでした**。これで、このラボを完了しました。

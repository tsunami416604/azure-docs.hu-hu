<properties
    pageTitle="我在 Azure 自動化中的第一個 PowerShell 工作流程 Runbook | Microsoft Azure"
    description="本教學課程逐步引導您使用 PowerShell 工作流程建立、測試和發佈簡單的文字 Runbook。  涵蓋數個概念，例如向 Azure 資源進行驗證和輸入參數。"
    services="automation"
    documentationCenter=""
    authors="bwren"
    manager="stevenka"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article" 
    ms.date="09/17/2015"
    ms.author="bwren"/>


# 我的第一個 PowerShell 工作流程 Runbook

> [AZURE.SELECTOR]
- [圖形化](automation-first-runbook-graphical.md)
- [PowerShell 工作流程](automation-first-runbook-textual.md)

本教學課程將逐步引導您完成建立 [PowerShell 工作流程 runbook](automation-runbook-types.md#powerShell-workflow-runbooks) Azure 自動化中。  讓我們先從將測試和發佈的簡單 Runbook 開始，同時說明如何追蹤 Runbook 工作的狀態。  然後我們要修改 Runbook 以實際上管理 Azure 資源，在此情況下是啟動 Azure 虛擬機器。  接著我們要藉由加入 Runbook 參數，讓 Runbook 更穩固。  

## 必要條件

若要完成本教學課程，您需要下列項目。

- 。 如果您尚無其中一個，您可以 [啟用 MSDN 訂戶的權益](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) 或 <a href="/pricing/free-trial/" target="_blank">[申請免費試用版](http://azure.microsoft.com/pricing/free-trial/)。
- [自動化帳戶](automation-configuring.md) 以保存 runbook。
- Azure 虛擬機器。  我們將會停止並啟動這台電腦，因此它不應該是生產環境。
- [Azure Active Directory 使用者和自動化認證資產](automation-configuring.md) 來向 Azure 資源。  此使用者必須擁有權限來啟動和停止虛擬機器。

## 步驟 1 - 建立新的 Runbook

我們一開始先建立輸出文字的簡單 runbook *Hello World*。

1. 在 Azure Preview 入口網站中，開啟您的自動化帳戶。  
[自動化帳戶] 頁面提供這個帳戶中資源的快速檢視。  您應該已經有一些資產。  其中大部分是會自動包含在新自動化帳戶的模組。  您也應該擁有所述的認證資產 [必要條件](#prerequisites)。
2. 按一下 [ **Runbook** 磚以開啟 runbook 的清單。<br>
![Runbook 控制](media/automation-first-runbook-textual/runbooks-control.png)
2. 上的 [建立新的 runbook **加入 runbook** ] 按鈕，然後 **建立新的 runbook**。
3. 為 runbook 提供名稱 *MyFirstRunbook 工作流程*。
4. 在此情況下，我們要建立 [PowerShell 工作流程 runbook](automation-runbook-types.md#powerShell-workflow-runbooks) 因此選取 **Powershell 工作流程** 的 **Runbook 類型**。<br>
![新的 runbook](media/automation-first-runbook-textual/new-runbook.png)
5. 按一下 [ **建立** 建立 runbook 並開啟文字編輯器。

## 步驟 2 - 將程式碼加入至 runbook

您可以直接將程式碼輸入到 runbook 中，或從程式庫控制項選取 cmdlet、runbook 和資產，並利用任何相關的參數將它們加入至 runbook。  在此逐步解說中，我們將直接輸入至 runbook。

1. 我們的 runbook 是目前空的只需要 *工作流程* 關鍵字，我們的 runbook 和大括號，以包封整個工作流程的名稱。 <br>
![Runbook 控制](media/automation-first-runbook-textual/empty-runbook.png)
2. 型別 *Write-output"Hello World."* 括號之間。 <br>
![Hello world](media/automation-first-runbook-textual/hello-world.png)
3.   按一下以儲存 runbook **儲存**。<br>
![儲存 runbook](media/automation-first-runbook-textual/runbook-edit-toolbar-save.png)

## 步驟 3 - 測試 Runbook

在我們發佈 Runbook 之前，為了使其可用於生產環境，我們想要測試以確定它可以正常運作。  當您測試 runbook 時，執行其 **草稿** 版本，並以互動方式檢視其輸出。  
 
2. 按一下 [ **測試窗格** 若要開啟 [測試] 窗格。<br>
![測試窗格](media/automation-first-runbook-textual/runbook-edit-toolbar-test-pane.png)
2. 按一下 [ **啟動** 以開始測試。  這應該是唯一啟用的選項。
3. A [runbook 工作](automation-runbook-execution) 建立並顯示其狀態。  
作業的狀態將啟動為 *已排入佇列* 指出等候 runbook worker 定域機組中可用。  它將移到 *起始*  當背景工作宣告該作業，然後 *執行* runbook 實際開始執行時。  
4. Runbook 工作完成時，會顯示其輸出。  在本例中，我們應該會看到 *Hello World*。<br>
![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)
5. 關閉 [測試] 窗格以返回畫布。

## 步驟 4 - 發佈和啟動 Runbook

我們剛剛建立的 Runbook 仍處於草稿模式。 我們需要將它發佈，才能在生產環境中執行它。  當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。  在我們的情況中，因為我們剛剛建立 Runbook，因此還沒有已發佈版本。 

1. 按一下 [ **發行** 發佈 runbook，然後 **是** 出現提示時。<br>
![發行](media/automation-first-runbook-textual/runbook-edit-toolbar-publish.png)
2. 如果您向左捲動以檢視中的 runbook **Runbook** 窗格現在會顯示 **撰寫狀態** 的 **發佈**。
3. 捲動回右方以檢視窗格 **MyFirstRunbook 工作流程**。  
在頂端的選項，讓我們啟動 runbook、 排程它，在未來某個時間點啟動，或建立 [webhook](automation-webhooks.md) 以便啟動它透過 HTTP 呼叫。 
4. 我們只想要啟動 runbook 時，因此請按 **啟動** 然後 **是** 出現提示時。<br>
![啟動 runbook](media/automation-first-runbook-textual/runbook-toolbar-start.png)
5. 工作窗格會開啟我們剛剛建立的 Runbook 工作。  我們可以關閉此窗格，但在此情況下，我們要將它開啟，使得我們可以觀看工作的進度。
6.  中會顯示工作狀態 **工作摘要** 且符合我們看到當我們測試 runbook 的狀態。<br>
![工作摘要](media/automation-first-runbook-textual/job-pane-summary.png)
7.  一旦 runbook 狀態顯示 *已完成*, ，按一下 [ **輸出**。  [輸出] 窗格會開啟，而且我們可以看到我們 *Hello World*。<br>
![工作摘要](media/automation-first-runbook-textual/job-pane-output.png)  
8.  關閉 [輸出] 窗格。
9.  按一下 [ **資料流** 開啟 runbook 工作的資料流] 窗格。  應該只會看到 *Hello World* 在輸出資料流，但這會顯示 runbook 工作，例如詳細資訊和錯誤的其他資料流 runbook 寫入它們。<br>
![工作摘要](media/automation-first-runbook-textual/job-pane-streams.png) 
9. 關閉 [資料流] 窗格和 [工作] 窗格，以返回 MyFirstRunbook 窗格。
9.  按一下 [ **工作** 若要開啟此 runbook 的 [工作] 窗格。  這樣會列出此 Runbook 所建立的所有工作。  應該只會看到列出，因為我們只執行工作一次一項工作。<br>
![工作](media/automation-first-runbook-textual/runbook-control-jobs.png) 
9. 您可以按一下此工作以開啟我們啟動 Runbook 時所檢視的相同 [工作] 窗格。  這可讓您回到過去的時間並檢視針對特定 Runbook 所建立的任何工作的詳細資料。

## 步驟 5 - 加入驗證來管理 Azure 資源

我們已經測試並發行我們 Runbook，但是到目前為止，它似乎並不實用。  我們想要讓它管理 Azure 資源。  它無法這麼做，但除非我們驗證使用的認證，指在 [必要條件](#prerequisites)。  這是與 **Add-azureaccount** 指令程式。

1.  開啟文字編輯器] 中，依序按一下 **編輯** MyFirstRunbook 工作流程] 窗格上。<br>
![編輯 runbook](media/automation-first-runbook-textual/runbook-toolbar-edit.png) 
2.  我們不需要 **Write-output** 行不再那麼，直接將它刪除。
3.  將游標放在大括弧之間的空白行。
3.  在 [程式庫] 控制項中，展開 **資產** 然後 **認證**。
4.  以滑鼠右鍵按一下您的認證，按一下 [ **加入至畫布**。  這會新增 **Get-automationpscredential** 您認證的活動。
5.  前面的 **Get-automationpscredential**, ，型別 *$Credential =* 將認證指派給變數。 
3.  在下一行中，輸入 *Add-azureaccount-認證 $Credential*。 <br>
![驗證](media/automation-first-runbook-textual/authentication.png) 
3. 按一下 [ **測試窗格** ，以便我們可以測試 runbook。
10. 按一下 [ **啟動** 以開始測試。  當它完成時，您應該會收到類似下列輸出，它會傳回認證中的使用者資訊。  這可確認認證無效。<br>
![驗證](media/automation-first-runbook-textual/authentication-test.png) 

## 步驟 6 - 加入程式碼以啟動虛擬機器

由於我們的 runbook 正在驗證我們的 Azure 訂用帳戶，所以我們可以管理資源。  我們將新增一個命令以啟動虛擬機器。  您可以在您的 Azure 訂用帳戶中挑選任何虛擬機器，而現在我們會將該名稱硬式編碼成 Cmdlet。 


1. 之後 *Add-azureaccount*, ，型別 *Start-azurevm-名稱 '為 VMName'-ServiceName 'VMServiceName'* 提供名稱和虛擬機器啟動的服務名稱。 <br>
![驗證](media/automation-first-runbook-textual/start-azurevm.png) 
9. 儲存 runbook，然後按一下 [ **測試窗格** ，以便我們可以測試它。
10. 按一下 [ **啟動** 以開始測試。  當它完成時，請檢查虛擬機器已啟動。


## 步驟 7 - 將輸入參數加入至 Runbook

我們的 Runbook 目前會啟動我們在 runbook 中硬式編碼的虛擬機器，但如果可以在啟動 runbook 時指定虛擬機器，它會更有用。  我們現在會將輸入參數加入 Runbook，以提供該功能。

1. 加入參數 *VMName* 和 *VMServiceName* runbook，並使用這些變數 **Start-azurevm** 指令程式，如下列影像所示。 <br>
![驗證](media/automation-first-runbook-textual/params.png) 
9. 儲存 Runbook 並開啟 [測試] 窗格。  請注意，您現在可以提供測試中將使用的兩個輸入變數的值。 
11.  關閉 [測試] 窗格。
12.  按一下 [ **發行** 發行新版本的 runbook。
13.  停止您在上一個步驟中啟動的虛擬機器。
13.  按一下 [ **啟動** 啟動 runbook。  輸入 **VMName** 和 **VMServiceName** 你要啟動虛擬機器。<br>
![啟動 Runbook](media/automation-first-runbook-textual/start-runbook-input-params.png) 

14.  Runbook 完成時，請檢查虛擬機器已啟動。


## 相關文章

- [我的第一個圖形化 Runbook](automation-first-runbook-graphical.md)

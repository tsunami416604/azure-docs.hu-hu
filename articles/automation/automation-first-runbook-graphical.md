<properties
    pageTitle="我在 Azure 自動化中的第一個圖形化 Runbook"
    description="教學課程將逐步引導您建立、測試和發佈簡單的圖形化 Runbook。  涵蓋數個概念，例如向 Azure 資源進行驗證、輸入參數和條件式連結。"
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


# 我的第一個圖形化 Runbook

> [AZURE.SELECTOR]
- [圖形化](automation-first-runbook-graphical.md)
- [PowerShell 工作流程](automation-first-runbook-textual.md)

本教學課程將逐步引導您完成建立 [圖形化 runbook](automation-runbook-types.md#graphical-runbooks) Azure 自動化中。  讓我們先從將測試和發佈的簡單 Runbook 開始，同時說明如何追蹤 Runbook 工作的狀態。  然後我們要修改 Runbook 以實際上管理 Azure 資源，在此情況下是啟動 Azure 虛擬機器。  接著我們要藉由加入 Runbook 參數和條件式連結，讓 Runbook 更穩固。  

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
![Runbook 控制](media/automation-first-runbook-graphical/runbooks-control.png)
2. 上的 [建立新的 runbook **加入 runbook** ] 按鈕，然後 **建立新的 runbook**。
3. 為 runbook 提供名稱 *Myfirstrunbook-graphical*。
4. 在此情況下，我們要建立 [圖形化 runbook](automation-graphical-authoring-intro.md) 因此選取 **圖形** 的 **Runbook 類型**。<br>
![新的 runbook](media/automation-first-runbook-graphical/new-runbook.png)
5. 按一下 [ **建立** 建立 runbook 並開啟圖形化編輯器。

## 步驟 2 - 將活動加入至 Runbook

編輯器左邊的 [程式庫] 控制項可讓您選取要加入到 Runbook 的活動。  我們要加入 **Write-output** cmdlet 來輸出我們從 runbook 的文字。

1.   在 [程式庫] 控制項中，展開 **Cmdlet** 節點，然後 **Microsoft.PowerShell.Utility**。<br>
![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/cmdlets-powershell-utility.png)
2.   向下捲動到清單底部。  以滑鼠右鍵按一下 **Write-output** 按一下 **加入至畫布**。
4.   按一下 [  **Write-output** 活動在畫布上的。  這會開啟可讓您設定活動的組態控制項。
5.    **標籤** 預設為 cmdlet 的名稱，但我們可以將它變更為更好記。 將它變更為 *撰寫 Hello World 到輸出*。
6.   按一下 [ **參數** 來提供此指令程式參數的值。  
某些 Cmdlet 有多個參數集，並且您必須選取您要使用的部分。  在此情況下， **Write-output** 有只有一個參數集，因此您不需要選取其中一個。 <br>
![寫入輸出屬性](media/automation-first-runbook-graphical/write-output-properties.png)
7.   選取 **InputObject** 參數。  這是我們將在其中指定要傳送至輸出資料流的文字的參數。
9.   在 **資料來源** 下拉式清單中，選取 **PowerShell 運算式**。
 **資料來源** ] 下拉式清單提供您用來填入參數值的不同來源。  您可以使用來自這類來源的輸出，例如另一個活動、自動化資產或 PowerShell 運算式。  在此情況下，我們只想要將文字輸出 *Hello World*。  我們可以使用 PowerShell 運算式，並指定字串。
10.   在 **運算式** 方塊中，輸入 *"Hello World"* 然後按一下 [ **確定** 兩次以返回畫布。<br>
![PowerShell 運算式](media/automation-first-runbook-graphical/expression-hello-world.png)
11.   按一下以儲存 runbook **儲存**。<br>
![儲存 runbook](media/automation-first-runbook-graphical/runbook-edit-toolbar-save.png)

## 步驟 3 - 測試 Runbook

在我們發佈 Runbook 之前，為了使其可用於生產環境，我們想要測試以確定它可以正常運作。  當您測試 runbook 時，執行其 **草稿** 版本，並以互動方式檢視其輸出。  
 
2. 按一下 [ **測試窗格** 若要開啟 [測試] 窗格。<br>
![測試窗格](media/automation-first-runbook-graphical/runbook-edit-toolbar-test-pane.png)
2. 按一下 [ **啟動** 以開始測試。  這應該是唯一啟用的選項。
3. A [runbook 工作](automation-runbook-execution) 建立並在窗格中顯示其狀態。  
作業的狀態將啟動為 *已排入佇列* 指出等候 runbook worker 定域機組中可用。  它將移到 *起始*  當背景工作宣告該作業，然後 *執行* runbook 實際開始執行時。  
4. Runbook 工作完成時，會顯示其輸出。  在本例中，我們應該會看到 *Hello World*。<br>
![Hello World](media/automation-first-runbook-graphical/test-output-hello-world.png)
5. 關閉 [測試] 窗格以返回畫布。


## 步驟 4 - 發佈和啟動 Runbook

我們剛剛建立的 Runbook 仍處於草稿模式。 我們需要將它發佈，才能在生產環境中執行它。  當您發佈 Runbook 時，您會使用草稿版本覆寫現有的已發佈版本。  在我們的情況中，因為我們剛剛建立 Runbook，因此還沒有已發佈版本。 

1. 按一下 [ **發行** 發佈 runbook，然後 **是** 出現提示時。<br>
![發行](media/automation-first-runbook-graphical/runbook-edit-toolbar-publish.png)
2. 如果您向左捲動以檢視中的 runbook **Runbook** 窗格現在會顯示 **撰寫狀態** 的 **發佈**。
3. 捲動回右方以檢視窗格 **MyFirstRunbook**。  
在頂端的選項，讓我們啟動 runbook、 排程它，在未來某個時間點啟動，或建立 [webhook](automation-webhooks.md) 以便啟動它透過 HTTP 呼叫。 
4. 我們只想要啟動 runbook 時，因此請按 **啟動** 然後 **是** 出現提示時。<br>
![啟動 runbook](media/automation-first-runbook-graphical/runbook-toolbar-start.png)
5. 工作窗格會開啟我們剛剛建立的 Runbook 工作。  我們可以關閉此窗格，但在此情況下，我們要將它開啟，使得我們可以觀看工作的進度。
6.  中會顯示工作狀態 **工作摘要** 且符合我們看到當我們測試 runbook 的狀態。<br>
![工作摘要](media/automation-first-runbook-graphical/job-pane-summary.png)
7.  一旦 runbook 狀態顯示 *已完成*, ，按一下 [ **輸出**。   **輸出** 窗格會開啟，而且我們可以看到我們 *Hello World*。<br>
![工作摘要](media/automation-first-runbook-graphical/job-pane-output.png)  
8.  關閉 [輸出] 窗格。
9.  按一下 [ **資料流** 開啟 runbook 工作的資料流] 窗格。  應該只會看到 *Hello World* 在輸出資料流，但這會顯示 runbook 工作，例如詳細資訊和錯誤的其他資料流 runbook 寫入它們。<br>
![工作摘要](media/automation-first-runbook-graphical/job-pane-streams.png) 
9. 關閉 [資料流] 窗格和 [工作] 窗格，以返回 MyFirstRunbook 窗格。
9.  按一下 [ **工作** 若要開啟此 runbook 的 [工作] 窗格。  這樣會列出此 Runbook 所建立的所有工作。  應該只會看到列出，因為我們只執行工作一次一項工作。<br>
![工作](media/automation-first-runbook-graphical/runbook-control-jobs.png) 
9. 您可以按一下此工作以開啟我們啟動 Runbook 時所檢視的相同 [工作] 窗格。  這可讓您回到過去的時間並檢視針對特定 Runbook 所建立的任何工作的詳細資料。

## 步驟 5 - 加入驗證來管理 Azure 資源

我們已經測試並發行我們 Runbook，但是到目前為止，它似乎並不實用。  我們想要讓它管理 Azure 資源。  它無法這麼做，但除非我們驗證使用的認證，指在 [必要條件](#prerequisites)。  這是與 **Set-azureaccount** 指令程式。

1.  開啟圖形化編輯器，依序按一下 **編輯** MyFirstRunbook 窗格上。<br>
![編輯 runbook](media/automation-first-runbook-graphical/runbook-toolbar-edit.png) 
2.  我們不需要 **撰寫 Hello World 到輸出** 失效，因此以滑鼠右鍵按一下並選取 **刪除**。
8.  在 [程式庫] 控制項中，展開 **Cmdlet** 然後 **Azure**。 
9.  新增 **Add-azureaccount** 在畫布上。<br>
![Add-azureaccount](media/automation-first-runbook-graphical/add-azureaccount.png) 
12.  選取 **Add-azureaccount** 然後按一下 [ **參數** [組態] 窗格中。
13.  **Add-azureaccount** 有多個參數集，因此我們之前必須先選取一個我們可以提供參數值。  按一下 [ **參數設定** ，然後選取 **使用者** 參數集。
14.  一旦您選取參數集，參數會在 [活動參數組態] 窗格中顯示。  按一下 [ **認證**。<br>
![加入 Azure 帳戶參數](media/automation-first-runbook-graphical/add-azureaccount-parameters.png)
15.  我們想要使用的認證資產，所以請選擇我們自動化帳戶中的這個指令程式 **認證資產** 的 **資料來源**。
16.  選取具有啟動和停止 Azure 環境中的虛擬機器的存取權的認證資產。<br>
![加入 Azure 帳戶資料來源](media/automation-first-runbook-graphical/credential-data-source.png)
17.  按一下 [ **確定** 兩次以返回畫布。


## 步驟 6 - 加入活動以啟動虛擬機器

我們現在要新增 **Start-azurevm** 活動來啟動虛擬機器。  您可以在您的 Azure 訂用帳戶中挑選任何虛擬機器，而現在我們會將該名稱硬式編碼成 Cmdlet。  

1. 展開 **Cmdlet** 然後 **Azure**。
2. 新增 **Start-azurevm** 至畫布，然後拖曳它到 **Add-azureaccount**。
11.  將滑鼠停留在 **Add-azureaccount** 直到圖形的底端出現圓形。  按一下圓形，並將箭頭拖曳到 **Start-azurevm**。  
您剛剛建立的箭號是 *連結*。  Runbook 將會開始處理 **Add-azureaccount** ，然後再執行 **Start-azurevm**。<br>
![Start-azurevm](media/automation-first-runbook-graphical/start-azurevm.png)
5. 選取 **Start-azurevm**。  按一下 [ **參數** 然後 **參數設定** 若要檢視的集合 **Start-azurevm**。  選取 **ByName** 參數集。 
請注意， **名稱** 和 **ServiceName** 旁邊具有驚嘆號它們。  這表示它們是必要的參數。
7. 選取 **名稱**。  使用 **常數值** 的 **資料來源** 並輸入来啟動虛擬機器名稱。  按一下 [ **確定**。
8. 選取 **ServiceName**。  使用 **常數值** 的 **資料來源** 並輸入来啟動虛擬機器名稱。  按一下 [ **確定**。<br>
![Start-azurevm 參數](media/automation-first-runbook-graphical/start-azurevm-params.png)
9. 按一下 [測試] 窗格，如此我們可以測試 Runbook。
10. 按一下 [ **啟動** 以開始測試。  當它完成時，請檢查虛擬機器已啟動。


## 步驟 7 - 將輸入參數加入至 Runbook

我們的 runbook 目前會啟動虛擬機器中所指定的 **Start-azurevm** 指令程式，但我們的 runbook 會啟動 runbook 時，我們可以指定虛擬機器的更有用。  我們現在會將輸入參數加入 Runbook，以提供該功能。

1. 開啟圖形化編輯器，依序按一下 **編輯** 上 **MyFirstRunbook** 窗格。
2. 按一下 [ **輸入和輸出** 然後 **加入輸入** 以開啟 Runbook 的輸入參數] 窗格。<br>
![Runbook 輸入和輸出](media/automation-first-runbook-graphical/runbook-edit-toolbar-inputoutput.png)
4. 指定 *VMName* 的 **名稱**。  保留 *字串* 的 **類型**, ，但變更 **強制** 至 *是*。  按一下 [ **確定**。
5. 建立第二個必要的輸入的參數呼叫 *VMServiceName* 然後按一下 [ **確定** 關閉 **輸入和輸出** 窗格。<br>
![Runbook 輸入參數](media/automation-first-runbook-graphical/input-parameters.png) 
6. 選取 **Start-azurevm** 活動，然後再按一下 **參數**。
7. 變更 **資料來源** 的 **名稱** 至 **Runbook 輸入** ，然後選取 **VMName**。<br>
![Start-azurevm Name 參數](media/automation-first-runbook-graphical/vmname-property.png) 
8. 變更 **資料來源** 的 **ServiceName** 至 **Runbook 輸入** ，然後選取 **VMServiceName**。<br>
![Start-azurevm 參數](media/automation-first-runbook-graphical/start-azurevm-params-inputs.png) 
9. 儲存 Runbook 並開啟 [測試] 窗格。  請注意，您現在可以提供測試中將使用的兩個輸入變數的值。 
11.  關閉 [測試] 窗格。
12.  按一下 [ **發行** 發行新版本的 runbook。
13.  停止您在上一個步驟中啟動的虛擬機器。
13.  按一下 [ **啟動** 啟動 runbook。  輸入 **VMName** 和 **VMServiceName** 你要啟動虛擬機器。<br>
![啟動 Runbook](media/automation-first-runbook-graphical/start-runbook-input-params.png) 
14.  Runbook 完成時，請檢查虛擬機器已啟動。

## 步驟 8 - 建立條件式連結

我們現在將修改 Runbook，讓它只會在未啟動時嘗試啟動 Runbook。  我們藉由新增這樣 **Get-azurevm** 指令程式可將包含虛擬機器的目前狀態的 runbook。  然後，我們將新增的條件式連結只會執行 **Start-azurevm** 如果已停止的目前執行狀態。  如果 Runbook 尚未停止，則輸出訊息。

1. 開啟 **MyFirstRunbook** 圖形化編輯器中。
2. 移除之間的連結 **Add-azureaccount** 和 **Start-azurevm** 按一下它，然後按 *刪除* 機碼。
3. 在 [程式庫] 控制項中，展開 **Cmdlet** 然後 **Azure**。
4. 新增 **Get-azurevm** 在畫布上。
5. 建立連結，以從 **Add-azureaccount** 至 **Get-azurevm**。  建立從另一個連結 **Get-azurevm** 至 **Start-azurevm**。<br>
![Get-azurevm 的 Runbook](media/automation-first-runbook-graphical/get-azurevm.png)   
6.  選取 **Get-azurevm** 按一下 **參數**。  選取參數集 *GetVMByServiceAndVMName*。
7.  如 **名稱**, ，請將 **日期來源** 至 **Runbook 輸入** ，然後選取 **VMName**。   
7.  如 **ServiceName**, ，請將 **日期來源** 至 **Runbook 輸入** ，然後選取 **VMServiceName**。  
8.  選取之間的連結 **Get-azurevm** 和 **Start-azurevm**。
9.  在 [組態] 窗格中，變更 **套用條件** 至 **True**。  請注意，連結轉變為虛線表示目標活動只有在條件解析為 true 時才會執行。
10.  如 **條件運算式**, ，型別 *$ActivityOutput [' Get-azurevm ']。PowerState-eq"Stopped"*。  **Start-azurevm** 現在才會執行虛擬機器已停止。<br>
![連結條件](media/automation-first-runbook-graphical/link-condition.png) 
11.  在 [程式庫] 控制項中，展開 **Cmdlet** 然後 **Microsoft.PowerShell.Utility**。
12.  新增 **Write-output** 在畫布上。
13.  建立連結，以從 **Get-azurevm** 至 **Write-output**。
14.  選取連結，並變更 **套用條件** 至 **True**。
14.  如 **條件運算式**, ，型別 *$ActivityOutput [' Get-azurevm ']。PowerState-ne"Stopped"*。  **寫入輸出** 現在才會執行虛擬機器未停止。<br>
![Write-output 的 Runbook](media/automation-first-runbook-graphical/write-output-link.png) 
15.  選取 **Write-output** 按一下 **參數**。
16.  如 **InputObject**, ，變更 **資料來源** 至 **PowerShell 運算式** 和運算式中的型別 *"$VMName.Name 已啟動 」。*。
17.  儲存 Runbook 並開啟 [測試] 窗格。
18.  在虛擬機器停止下啟動 Runbook，它應該會啟動。
19.  在虛擬機器啟動下啟動 Runbook，您應該會得到它已啟動的輸出。
 

## 相關文章

- [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)
- [我的第一個 PowerShell 工作流程 Runbook](automation-first-runbook-textual.md)

 

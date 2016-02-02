<properties 
    pageTitle="Azure 自動化的 Runbook 和模組資源庫 | Microsoft Azure"
    description="來自 Microsoft 和社群的 Runbook 和模組可供您在 Azure 自動化環境中安裝及使用。本文說明如何存取這些資源以及將您的 Runbook 貢獻至資源庫。"
    services="automation"
    documentationCenter=""
    authors="bwren"
    manager="stevenka"
    editor="tysonn" />
<tags 
    ms.service="automation"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/23/2015"
    ms.author="bwren" />



# Azure 自動化的 Runbook 和模組資源庫

並非在 Azure 自動化中建立您自己的 Runbook 和模組，您可以存取各種已由 Microsoft 和社群建置的解決方案。 您可以不加修改地使用這些解決方案，或者您可以使用它們做為起點並針對您的特定需求進行編輯。

您可以取得從 runbook [Runbook 庫](#runbooks-in-runbook-gallery) 和模組從 [PowerShell 組件庫](#modules-in-powerShell-gallery)。 您也可以藉由共用您開發的解決方案來參與社群。

## Runbook 資源庫中的 Runbook

[Runbook 庫](http://gallery.technet.microsoft.com/scriptcenter/site/search?f[0].Type=RootCategory&f[0].Value=WindowsAzure&f[1].Type=SubCategory&f[1].Value=WindowsAzure_automation&f[1].Text=Automation) 從 Microsoft 和社群，您可以匯入至 Azure 自動化提供各種不同的 runbook。您可以從裝載於組件庫下載 runbook [TechNet 指令碼中心](http://gallery.technet.microsoft.com/), ，或您可以從組件庫，從 Azure 入口網站和 Azure preview 入口網站直接匯入 runbook。

您只能使用 Azure 入口網站或 Azure Preview 入口網站直接從 Runbook 資源庫匯入。 您無法使用 Windows PowerShell 執行此函式。
>[AZURE.NOTE] 您應該驗證您從 Runbook 資源庫取得的任何 Runbook 的內容，並且在生產環境中安裝和執行它們時小心謹慎。

### 使用 Azure 入口網站從 Runbook 資源庫匯入 Runbook

1. 在 Azure 管理入口網站中，按一下 [新增]**** > [應用程式服務]**** > [自動化]**** > [Runbook]**** > [從資源庫]****。
2. 選取類別以檢視相關的 Runbook，然後選取 Runbook 以檢視其詳細資料。當您選取您想的 runbook 時，按一下向右箭號按鈕。<br>
![Runbook 資源庫](media/automation-runbook-gallery/runbook-gallery.png)
3. 檢閱 Runbook 的內容並記下說明中的任何需求。 當您完成時按一下向右箭頭按鈕。
4. 輸入 Runbook 詳細資料，然後按一下勾選記號按鈕。 Runbook 名稱已填入。
5. Runbook 會出現在 [自動化帳戶] 的 [Runbook]**** 索引標籤。

### 使用 Azure Preview 入口網站從 Runbook 資源庫匯入 Runbook

1. 在 Azure Preview 入口網站中，開啟您的自動化帳戶。
2. 按一下 [Runbook]**** 磚以開啟 Runbook 的清單。
3. 按一下 [ **瀏覽組件庫** ] 按鈕。 <br>
![瀏覽資源庫按鈕](media/automation-runbook-gallery/browse-gallery-button.png)
4. 找出主機庫項目，並選取它，以便檢視其詳細資料。   <br>
![瀏覽資源庫](media/automation-runbook-gallery/browse-gallery.png)
4. 按一下 [ **檢視原始碼專案** 檢視中的項目 [TechNet 指令碼中心](http://gallery.technet.microsoft.com/)。
5. 若要匯入項目，請按一下以檢視其詳細資料，然後按一下 **匯入** ] 按鈕。<br>
![匯入按鈕](media/automation-runbook-gallery/gallery-item-detail.png)
6. 選擇性變更 Runbook 的名稱，然後按一下 [確定]**** 以匯入 Runbook。
5. Runbook 會出現在 [自動化帳戶] 的 [Runbook]**** 索引標籤。


### 將 Runbook 新增至 Runbook 資源庫

Microsoft 鼓勵您將您認為可能有助於其他客戶的 Runbook 新增至 Runbook 資源庫。 您可以新增 runbook [上傳至指令碼中心](http://gallery.technet.microsoft.com/site/upload) 考慮到下列詳細資料。

- 您必須指定 *Windows Azure* 的 **類別** 和 *自動化* 的 **子類別目錄** runbook 精靈中顯示。

- 上傳必須是單一 .ps1 或 .graphrunbook 檔案。 如果 Runbook 需要任何模組、子 Runbook 或資產，則您應該在提交的說明中和 Runbook 的註解區段中列出。 如果您有需要多個 Runbook 的解決方案，則分別將每個上傳並且在各自的說明中列出相關 Runbook 的名稱。 請確定您使用相同的標記，這樣它們就會在相同的類別中顯示。 使用者必須閱讀說明以便知道解決方案需要其他 Runbook 才能運作。

- 使用 [**插入程式碼區段**] 圖示將 PowerShell 或 PowerShell 工作流程程式碼片段插入說明中。

- 上傳的摘要會顯示在 Runbook 資源庫結果，因此您應該提供詳細資訊，幫助使用者識別 Runbook 的功能。

- 您應該對上傳項目指派 1 到 3 個下列標記。 Runbook 會列在精靈中符合其標記的類別底下。 精靈會略過不在此清單上的任何標記。 如果您未指定任何相符的標記，Runbook 會列在 [其他] 類別底下。

 - 備份
 - 產能管理
 - 變更控制
 - 法規遵循
 - 開發/測試環境
 - 災害復原
 - 監視
 - 修補
 - 佈建
 - 補救
 - VM 生命週期管理

- 一小時更新自動化資源庫一次，因此您不會立即看到您的貢獻。 如果您沒有看到您的 runbook 資源庫中一個小時之後，檢查需求 [將 Runbook 加入至 Runbook 庫](#AddRunbook) 一節。

## PowerShell 資源庫中的模組

PowerShell 模組包含 cmdlet，可用於您的 runbook，而且可在現有的模組，您可以在 Azure 自動化中安裝 [PowerShell 組件庫](http://www.powershellgallery.com)。 您可以從 Azure Preview 入口網站啟動這個資源庫，並且直接安裝至 Azure 自動化，或者您可以手動下載及安裝。 您無法直接從 Azure 入口網站安裝模組，但是您可以下載及安裝它們，就像任何其他模組一樣。

### 使用 Azure Preview 入口網站從 PowerShell 資源庫匯入模組

1. 在 Azure Preview 入口網站中，開啟您的自動化帳戶。
2. 按一下 [**資產**] 磚以開啟資產的清單。
3. 按一下 [**模組**] 磚以開啟模組的清單。
3. 按一下 [ **PowerShell 組件庫** 按鈕來啟動另一個瀏覽器視窗中的 PowerShell 組件庫。 <br>
![PowerShell 資源庫](media/automation-runbook-gallery/powershell-gallery-button.png)
4. 按一下 [ **模組** 功能表以存取可用的模組清單。<br>
![PowerShell 資源庫按鈕](media/automation-runbook-gallery/powershell-gallery.png)
4. 尋找您感興趣的模組，並選取以檢視其詳細資料。
5. 若要直接在 Azure 自動化中安裝的模組，按一下 [ **部署至 Azure 自動化** ] 按鈕。<br>
![PowerShell 資源庫按鈕](media/automation-runbook-gallery/powershell-gallery-detail.png)
6. 您會返回 Azure Preview 入口網站的 [**自訂部署**] 窗格。 指定您要在 [**新的或現有的自動化帳戶**] 中安裝模組，以及指定 [**自動化帳戶名稱**]。 如果您使用現有帳戶，則會略過 [**自動化帳戶位置**]。
7. 選取 [**資源群組**]，並為模組指定現有資源群組或建立新群組。
6. 您必須選取 [**法律條款**]，然後按一下 [**購買**]。 請注意，雖然這個按鈕的名稱為購買，但是實際上不會因為安裝模組而向您收費。
7. 按一下 [**建立**] 以匯入模組。 因為必須解壓縮每個活動，此步驟可能需要幾分鐘的時間。
8. 您會在模組已部署以及模組完成時收到通知。


## 要求 Runbook 或模組

您可以傳送要求至 [User Voice](http://feedback.azure.com/forums/246290-azure-automation)。如果您需要協助您撰寫的 runbook，或對於 PowerShell 有問題，若要將問題張貼我們 [論壇](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc)。

## 相關文章

- [建立或匯入 Azure 自動化中 runbook](automation-creating-importing-runbook.md)
- [了解 PowerShell 工作流程](automation-powershell-workflow.md)





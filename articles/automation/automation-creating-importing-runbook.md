<properties 
    pageTitle="在 Azure 自動化中建立或匯入 Runbook"
    description="本文說明如何在 Azure 自動化中建立新的 Runbook，或從檔案匯入新的 Runbook。"
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
    ms.date="09/22/2015"
    ms.author="bwren" />

# 在 Azure 自動化中建立或匯入 Runbook

可以透過下列任一方式將 runbook 加入至 Azure 自動化 [建立一個新](#creating-a-new-runbook) 或從檔案或從匯入現有 runbook [Runbook 庫](automation-runbook-gallery.md)。 本文提供從檔案建立和匯入 Runbook 的資訊。  您可以取得有關存取社群 runbook 和模組中的所有 [Azure 自動化的 Runbook 和模組組件庫](automation-runbook-gallery.md)。

## 建立新的 Runbook

您可以使用其中一個 Azure 入口網站或 Windows PowerShell，在 Azure 自動化中建立新的 Runbook。 一旦建立 runbook 之後，您可以使用編輯中的資訊 [了解 PowerShell 工作流程](automation-powershell-workflow.md) 和 [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)。

### 使用 Azure 入口網站建立新的 Azure 自動化 Runbook

您只能使用 [PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks) Azure 入口網站中。

1. 在 Azure 網站中，按一下， **新增**, ，**應用程式服務**, ，**自動化**, ，**Runbook**, ，**快速建立**。
2. 輸入必要的資訊，然後按一下 **建立**。 Runbook 名稱必須以字母開頭，可以具有字母、數字、底線和連字號。
3. 如果您想要立即編輯 runbook，然後按一下 [ **編輯 Runbook**。 否則，請按一下 **確定**。
4. 新的 runbook 會出現在 **Runbook** ] 索引標籤。


### 使用 Azure Preview 入口網站建立新的 Azure 自動化 Runbook

1. 在 Azure Preview 入口網站中，開啟您的自動化帳戶。 
2. 按一下 [ **Runbook** 磚以開啟 runbook 的清單。
3. 按一下 [ **加入 runbook** ] 按鈕，然後 **建立新的 runbook**。
2. 型別 a **名稱** runbook 並選取其 [類型](automation-runbook-types.md)。 Runbook 名稱必須以字母開頭，可以具有字母、數字、底線和連字號。
3. 按一下 [ **建立** 建立 runbook 並開啟編輯器] 中。


### 使用 Windows PowerShell 建立新的 Azure 自動化 Runbook

您可以使用 [新增 AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690272.aspx) 指令程式來建立空白 [PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。 您可以指定 **名稱** 參數來建立空的 runbook，您可以稍後再編輯，或者您可以指定 **路徑** 參數來匯入的指令碼檔案。 

下列範例命令顯示如何建立新的空白 Runbook。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    New-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName 

## 將 Runbook 從檔案匯入 Azure 自動化

您可以在 Azure 自動化中建立新的 Runbook，方法是匯入 PowerShell 指令碼或 PowerShell 工作流程 (.ps1 延伸模組)，或匯入已匯出的圖形化 Runbook (.graphrunbook)。  您必須指定 [的 runbook 類型](automation-runbook-types.md) ，才會考慮到下列考量事項的匯入。 

- .Graphrunbook 檔案只會匯入到新 [圖形化 runbook](automation-runbook-types.md#graphical-runbooks), ，與只能從.graphrunbook 檔案建立圖形化 runbook。
- 包含 PowerShell 工作流程的.ps1 檔案只能匯入 [PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。  如果檔案包含多個 PowerShell 工作流程，則匯入將會失敗。 您必須將每個工作流程儲存到它們各自的檔案，並且個別匯入。
- .Ps1 檔案不包含工作流程可以匯入到 [ [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) 或 [PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks)。  如果它匯入 PowerShell 工作流程 Runbook，則它會轉換為工作流程，註解會包含在 Runbook 中，指定所做的變更。

### 使用 Azure 入口網站從檔案匯入 Runbook
您可以使用下列程序，將指令碼檔案匯入到 Azure 自動化。  請注意，您只能使用此入口網站將 .ps1 檔案匯入 PowerShell 工作流程 Runbook。  對於其他類型您必須使用 Azure Preview 入口網站。

1. 在 Azure 管理入口網站中，選取 **自動化** ，然後選取 [自動化帳戶。
2. 按一下 [ **匯入**。
3. 按一下 [ **瀏覽檔案** 並找出要匯入指令碼檔案。
4. 如果您想要立即編輯 runbook，然後按一下 [ **編輯 Runbook**。 否則，請按一下 [確定]。
5. 新的 runbook 會出現在 **Runbook** 自動化帳戶] 索引標籤。
6. 您必須 [發佈 runbook](#publishing-a-runbook) 可以執行之前。


### 使用 Azure Preview 入口網站從檔案匯入 Runbook
您可以使用下列程序，將指令碼檔案匯入到 Azure 自動化。  請注意，您只能使用此入口網站將 .ps1 檔案匯入 PowerShell 工作流程 Runbook。

1. 在 Azure Preview 入口網站中，開啟您的自動化帳戶。 
2. 按一下 [ **Runbook** 磚以開啟 runbook 的清單。
3. 按一下 [ **加入 runbook** ] 按鈕，然後 **匯入**。
4. 按一下 [ **Runbook 檔案** 來選取要匯入檔案
2. 如果 **名稱** 欄位已啟用，則您可以選擇要進行變更。  Runbook 名稱必須以字母開頭，可以具有字母、數字、底線和連字號。
3. 選取 [runbook 類型](automation-runbook-types.md) 上面所列的限制納入考量。
3. 新的 Runbook 會出現在 [自動化帳戶] 的 Runbook 清單中。
4. 您必須 [發佈 runbook](#publishing-a-runbook) 可以執行之前。

### 使用 Windows PowerShell 從指令碼檔案匯入 Runbook

您可以使用 [組 AzureAutomationRunbookDefinition](https://msdn.microsoft.com/library/dn690267.aspx) 指令程式的指令碼檔案匯入現有 runbook 的草稿版本。 指令碼檔案必須包含單一 Windows PowerShell 工作流程。 如果 Runbook 已經有草稿版本，則除非您使用 Overwrite 參數，否則匯入作業將會失敗。 匯入 runbook 後，您可以將其與發行 [發行 AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx)。

以下範例命令顯示如何將指令碼檔案匯入現有 Runbook，然後發佈。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    $scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

    Set-AzureAutomationRunbookDefinition –AutomationAccountName $automationAccountName –Name $runbookName –Path $ scriptPath -Overwrite
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName


## 發佈 Runbook

當您建立或匯入新的 Runbook 時，您必須發佈才能執行它。  Azure 自動化中的每個 Runbook 有草稿和已發行的版本。 只可執行已發行版本，而且只可編輯草稿版本。 已發行版本不會受到草稿版本的任何變更影響。 草稿版本應該已可供使用時，您會將它發佈，以草稿版本覆寫已發佈版本。

## 使用 Azure 入口網站發佈 Runbook

1. 在 Azure 入口網站中開啟 Runbook。
1. 在畫面頂端，按一下 [ **作者**。
1. 在畫面底部，按一下 [ **發行** 然後 **是** 驗證訊息。

## 使用 Azure Preview 入口網站發佈 Runbook

1. 在 Azure Preview 入口網站中開啟 Runbook。
1. 按一下 [ **編輯** ] 按鈕。
1. 按一下 [ **發行** ] 按鈕，然後 **是** 驗證訊息。


## 使用 Windows PowerShell 發佈 Runbook

您可以使用 [發行 AzureAutomationRunbook](https://msdn.microsoft.com/library/dn690266.aspx) 發佈 runbook，以使用 Windows PowerShell 指令程式。 下列範例命令顯示如何發佈範例 Runbook。

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Sample-TestRunbook"
    
    Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName



## 相關文章

- [Azure 自動化的 Runbook 和模組資源庫](automation-runbook-gallery.md)
- [在 Azure 自動化中編輯文字式 Runbook](automation-edit-textual-runbook.md)
- [Azure 自動化中的圖形化編寫](automation-graphical-authoring-intro.md)

<properties 
    pageTitle="在 Azure 自動化中測試 Runbook"
    description="在 Azure 自動化中發佈 Runbook 之前，您可以先加以測試，以確保 Runbook 能夠如預期般運作。  本文說明如何測試 Runbook 及檢視其輸出。"
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

# 在 Azure 自動化中測試 Runbook
當您測試 runbook， [草稿版本](automation-creating-importing-runbook.md#publishing-a-runbook) 執行並且完成之後，它會執行任何動作。 不建立任何工作歷程記錄，但 [輸出](automation-runbook-output-and-messages.md#output-stream) 和 [警告和錯誤](automation-runbook-output-and-messages.md#message-streams) 資料流會顯示在測試輸出窗格。 訊息傳送至 [詳細資料流](automation-runbook-output-and-messages.md#message-streams) 會顯示在輸出窗格才 [$VerbosePreference 變數](automation-runbook-output-and-messages.md#preference-variables) 設為 [繼續]。

即使執行的是草稿版本，Runbook 仍會正常執行工作流程，並會針對環境中的資源執行任何動作。 因此，您只能在非生產資源中測試 Runbook。

每個測試的程序 [的 runbook 類型](automation-runbook-types.md) 是相同，但沒有任何差異測試之間的文字編輯器，在 Azure 預覽入口網站中的圖形化編輯器。  


## 在 Azure 預覽入口網站中測試 Runbook

您可以使用任何 [runbook 類型](automation-runbook-types.md) Azure preview 入口網站。

1. 在開啟 runbook 的草稿版本 [文字編輯器](automation-editing-a-runbook#Portal) 或 [圖形化編輯器](automation-graphical-authoring-intro.md)。
2. 按一下 [ **測試** ] 按鈕以開啟 [測試] 分頁。
3. 如果 Runbook 含有參數，這些參數會列在左窗格中，您可以在此提供值，以供測試使用。
4. 如果您想要在執行測試 [混合式 Runbook 背景工作](automation-hybrid), ，然後變更 **回合設定** 至 **混合式背景工作** ，然後選取 [目標群組的名稱。  否則，請保留預設 **Azure** 定域機組中執行測試。
5. 按一下 [ **啟動** ] 按鈕以開始測試。
6. 如果 runbook [PowerShell 工作流程](automation-runbook-types.md#powershell-workflow-runbooks) 或 [圖形](automation-runbook-types.md#graphical-runbooks), ，則您可以停止或暫停它時測試輸出窗格] 下的按鈕。 暫停 Runbook 時，它會先完成目前的活動後才暫停。 Runbook 一旦暫停，您可以選擇停止或重新啟動。
7. 您可以在 [輸出] 窗格中檢查 Runbook 的輸出。



## 在 Azure 入口網站中測試 Runbook

您只能使用 [PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks) Azure 入口網站中。


1. [開啟 runbook 的草稿版本](automation-edit-textual-runbook.md#to-edit-a-runbook-with-the-azure-portal)。
2. 按一下 [ **測試** ] 按鈕以開始測試。  如果 Runbook 含有參數，您會看到一個對話方塊，要求您提供測試所需的值。
6. 測試過程中，您可以使用 [輸出] 窗格下方的按鈕來停止或暫停 Runbook。 暫停 Runbook 時，它會先完成目前的活動後才暫停。 Runbook 一旦暫停，您可以選擇停止或重新啟動。
7. 您可以在 [輸出] 窗格中檢查 Runbook 的輸出。


## 相關文章

- [在 Azure 自動化中建立或匯入 Runbook](automation-creating-importing-runbook.md)
- [Azure 自動化中的圖形化 Runbook](automation-graphical-authoring-intro.md)
- [在 Azure 自動化中編輯文字式 Runbook](automation-edit-textual-runbook.md)
- [Azure 自動化中的 Runbook 輸出與訊息](automation-runbook-output-and-messages.md)


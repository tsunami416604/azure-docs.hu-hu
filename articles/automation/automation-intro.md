<properties
    pageTitle="什麼是 Azure 自動化 |Microsoft Azure"
    description="了解 Azure 自動化提供的價值，並取得常見問題的解答，以便您可以開始建立及使用 Runbook 和 Azure Automation DSC。"
    services="automation"
    documentationCenter=""
    authors="SnehaGunda"
    manager="stevenka"
    editor=""/>

<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="11/05/2015"
    ms.author="bwren;sngun"/>

# Azure 自動化概觀


Microsoft Azure 自動化可讓使用者將執行於雲端和企業環境中的手動、長時間執行、易發生錯誤且重複性高的工作自動化。 它可以節省時間並提高日常管理工作的可靠性，甚至將它們排程為定期自動執行。 您可以使用 Runbook 自動執行程序，或使用「期望狀態設定」自動進行組態管理。 本文提供 Azure 自動化的簡短概觀，並回答一些常見問題。 您可以參考此文件庫中的其他文件，以取得不同主題的詳細資訊。


## 使用 Runbook 自動執行程序

Runbook 是可在 Azure 自動化中執行一些自動程序的一組工作。 它可能是簡單的程序 (例如啟動虛擬機器及建立記錄項目)，或您可能會有一個結合其他較小的 Runbook 的複雜 Runbook，用來對多個資源或甚至是多個雲端和內部部署環境執行複雜的程序。  

例如，當資料庫接近大小上限時，您可能有用來截斷 SQL Database 的現有手動程序，該程序包含多個步驟，例如連接到伺服器、連接到資料庫、取得目前的資料庫大小、檢查是否超過臨界值，然後進行截斷並通知使用者。 不需手動執行每個步驟，您可以建立會以單一程序的形式執行這所有工作的 Runbook。 您會啟動 Runbook、提供所需的資訊，例如 SQL 伺服器名稱、資料庫名稱和收件者電子郵件，然後在程序完成時在一旁休息。 


## Runbook 可以自動化什麼？

Azure 自動化中的 Runbook 是以 Windows PowerShell 或 Windows PowerShell 工作流程為基礎，因此它們會執行 PowerShell 能做的一切功能。 如果應用程式或服務具有 API，則 Runbook 處理它。 如果您有應用程式適用的 PowerShell 模組，可以將該模組載入到 Azure 自動化，並且在 Runbook 中包含這些 Cmdlet。 Azure 自動化 Runbook 會在 Azure 雲端執行，而且可以存取任何雲端資源或可從雲端存取的外部資源。 使用 [混合式 Runbook 背景工作](automation-hybrid-runbook-worker.md), ，runbook 可以在您要管理本機資源的本機資料中心中執行。 


## 從社群取得 Runbook

 [Runbook 庫](automation-runbook-gallery.md#runbooks-in-runbook-gallery) 包含由 Microsoft 和社群，您可以在您的環境中的使用不變的 runbook，或自訂它們自己的目的。 它們也可做為參考以了解如何建立您自己的 Runbook。 您甚至可以將您認為其他使用者可能覺得很有用的自己的 Runbook 貢獻到資源庫中。 


## 利用 Azure 自動化建立 Runbook 

您可以 [建立您自己的 runbook](automation-creating-importing-runbook.md) 從草稿或修改 runbook 從 [Runbook 庫](http://msdn.microsoft.com/library/azure/dn781422.aspx) 您的需求。 有三個不同 [runbook 類型](automation-runbook-types.md) ，您可以選擇從取決於您的需求和 PowerShell 的經驗。 如果您想要直接使用 PowerShell 程式碼中，則您可以使用 [PowerShell runbook](automation-runbook-types.md#powershell-runbooks) 或 [PowerShell 工作流程 runbook](automation-runbook-types.md#powershell-workflow-runbooks) 編輯離線，或使用 [文字編輯器](http://msdn.microsoft.com/library/azure/dn879137.aspx) Azure 入口網站中。 如果您想要編輯 runbook，而不要看到基礎程式碼，則您可以建立 [圖形化 runbook](automation-runbook-types.md#graphical-runbooks) 使用 [圖形化編輯器](automation-graphical-authoring-intro.md) Azure preview 入口網站。 

寧可觀賞也不要閱讀？ 看看以下在 2015 年 5 月的 Microsoft Ignite 活動影片。 附註：雖然這個影片中討論的概念和功能是正確的，但自從這個影片錄製以來，Azure 自動化已經有很大的進展，現在它在 Azure 入口網站中具有更豐富的 UI，並支援其他的功能。

> [AZURE.VIDEO microsoft-ignite-2015-automating-operational-and-management-tasks-using-azure-automation]


## 使用「預期狀態設定」自動進行組態管理 

[PowerShell DSC](https://technet.microsoft.com/library/dn249912.aspx) 是管理平台，可讓您管理、 部署和強制執行設定的實體主機和虛擬機器使用的宣告式 PowerShell 語法。 您可以在中央 DSC 提取伺服器上定義組態，該伺服器是以可自動擷取和套用的電腦為目標。 DSC 提供一組 PowerShell Cmdlet，讓您用來管理組態和資源。  

[Azure 自動化 DSC](automation-dsc-overview.md) 為提供服務所需的企業環境的 PowerShell DSC 的雲端架構解決方案。  您可以在 Azure 自動化中管理 DSC 資源，並將組態套用至虛擬或實體機器，以便它們從 Azure 雲端中的 DSC 提取伺服器擷取組態。  它也提供報告服務，以通知您重要的事件，例如當節點偏離其指派的組態時以及已套用新的組態時。 


## 使用 Azure 自動化建立自己的 DSC 組態

[DSC 組態](automation-dsc-overview.md#azure-automation-dsc-terms) 指定的節點所需的狀態。  多個節點可以套用相同的組態，以確保它們全都維持相同的狀態。  您可以在本機電腦上使用任何文字編輯器來建立組態，然後將它匯入至 Azure 自動化加以編譯並套用至節點。


## 取得模組和組態 

您可以取得 [PowerShell 模組](automation-runbook-gallery.md#modules-in-powershell-gallery) 包含您可以使用您的 runbook 和從 DSC 組態中的 cmdlet [PowerShell 組件庫](http://www.powershellgallery.com/)。 您可以從 Azure Preview 入口網站啟動這個資源庫，並且將模組直接匯入 Azure 自動化，或者您可以手動下載及匯入。 您無法直接從 Azure 入口網站安裝模組，但是您可以下載及安裝它們，就像任何其他模組一樣。 


## Azure 自動化的實際應用範例 

以下是一些您可以使用 Azure 自動化自動進行的案例種類範例。 

* 在不同的 Azure 訂用帳戶中建立和複製虛擬機器。 
* 本機電腦中的檔案複本排程到 Azure Blob 儲存體容器。 
* 在偵測到阻斷服務攻擊時自動執行安全性功能，例如來自用戶端的拒絕要求。 
* 確定機器持續依循設定的安全性原則。
* 跨雲端和內部部署基礎結構來管理應用程式程式碼的連續部署。 
* 在 Azure 中針對您的實驗室環境建置 Active Directory 樹系。 
* 如果資料庫已接近大小上限，請截斷 SQL Database 中的資料表。 
* 遠端更新 Azure 網站的環境設定。 


## Azure 自動化與其他自動化工具如何產生關聯？

[服務管理自動化 (SMA)](http://technet.microsoft.com/library/dn469260.aspx) 可供自動化私人雲端中的管理工作。 安裝在本機資料中心內的元件 [Microsoft Azure Pack](https://www.microsoft.com/en-us/server-cloud/)。 SMA 和 Azure 自動化使用相同的 runbook 格式，根據 Windows PowerShell 和 Windows PowerShell 工作流程，但 SMA 不支援 [圖形化 runbook](automation-graphical-authoring-intro.md)。  

[System Center 2012 Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) 僅供內部部署資源自動化。 它會使用與 Azure 自動化和服務管理自動化不同的 Runbook 格式，並具有圖形化介面，用來建立 Runbook，而不需要編寫任何指令碼。 其 Runbook 是由來自專門為 Orchestrator 編寫的整合套件的活動組成。 


## 我可以在哪裡取得詳細資訊？ 

您可利用各種資源，深入了解 Azure 自動化，並建立自己的 Runbook。 

* **Azure 自動化程式庫** 是您所在的權限。 本文件庫中的文章提供完整的文件，說明如何設定和管理 Azure 自動化，以及撰寫自己的 Runbook。 
* [Azure PowerShell cmdlet](http://msdn.microsoft.com/library/jj156055.aspx) 提供自動化 Azure 作業，使用 Windows PowerShell 的資訊。 Runbook 會使用這些 Cmdlet 來處理 Azure 資源。 
* [管理部落格](https://azure.microsoft.com/blog/tag/azure-automation/) 提供 Microsoft Azure 自動化和其他管理技術，在最新的資訊。 您應該訂閱此部落格，隨時掌握 Azure 自動化團隊的最新消息。 
* [自動化論壇](http://go.microsoft.com/fwlink/p/?LinkId=390561) 可讓您張貼有關要由 Microsoft 和自動化社群解決的 Azure 自動化問題。 
* [Azure 自動化 Cmdlet](https://msdn.microsoft.com/library/mt244122.aspx) 提供自動化管理工作的資訊。 它包含各種 Cmdlet 來管理自動化帳戶、資產、Runbook、DSC。


## 可以提供意見嗎？ 

**請不吝提供意見。**如果您要尋找 Azure 自動化 Runbook 解決方案或整合模組，請在指令碼中心提出指令碼要求。 如果您有任何意見或功能要求 Azure 自動化時，請將問題張貼在 [User Voice](http://feedback.windowsazure.com/forums/34192--general-feedback)。 感謝您！ 




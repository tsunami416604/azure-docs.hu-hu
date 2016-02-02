<properties
   pageTitle="從 Orchestrator 移轉到 Azure 自動化 |Microsoft Azure"
   description="描述如何將 Runbook 和整合套件從 System Center Orchestrator 移轉到 Azure 自動化。"
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
   ms.date="11/11/2015"
   ms.author="bwren" />



# 從 Orchestrator 移轉到 Azure 自動化 (Beta)

中的 Runbook [System Center Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) 根據來自而 Azure 自動化中的 runbook 根據 Windows PowerShell，專為 Orchestrator 編寫的整合套件的活動。 [圖形化 runbook](automation-runbook-types#graphical-runbooks) Azure 自動化中外觀都類似 Orchestrator runbook，其活動代表 PowerShell cmdlet、 子 runbook 和資產。

[System Center Orchestrator 遷移工具組](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) 包含工具，可協助您將 runbook 從 Orchestrator 轉換成 Azure 自動化。除了轉換 Runbook 本身，您必須將整合模組與 Runbook 用來整合模組與 Windows PowerShell Cmdlet 的活動轉換。

以下是將 Orchestrator Runbook 轉換為 Azure 自動化的基本程序。 下列各節將詳細說明這些步驟。

1.  下載 [System Center Orchestrator 遷移工具組](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) 其中包含的工具和本文所討論的模組。
2.  匯入 [標準活動模組](#standard-activities-module) 到 Azure 自動化。 這包括已轉換的 Runbook 可能使用的標準 Orchestrator 活動的轉換版本。
3.  匯入 [System Center Orchestrator 整合模組](#system-center-orchestrator-integration-modules) 到 Azure 自動化，供您存取 System Center 的 runbook 所整合套件。
4.  將自訂和使用協力廠商的整合套件轉換 [整合套件轉換器](#integration-pack-converter) 並匯入至 Azure 自動化。
5.  轉換 Orchestrator runbook 使用 [Runbook 轉換器](#runbook-converter) 並安裝在 Azure 自動化中。
6.  因為 Runbook Converter不會轉換這些資源，請在 Azure 自動化中手動建立必要的 Orchestrator 資產。
7.  設定 [混合式 Runbook 背景工作](#hybrid-runbook-worker) 本機資料中心執行轉換的 runbook 將會存取本機資源。

## 服務管理自動化

[Service Management Automation](http://technet.microsoft.com/library/dn469260.aspx) (SMA) 儲存，並像 Orchestrator，本機資料中心內執行 runbook 並使用相同的整合模組與 Azure 自動化。  [Runbook 轉換器](#runbook-converter) 會將 Orchestrator runbook 的圖形化 runbook 不過 SMA 中並不支援。 您仍然可以安裝 [標準活動模組](#standard-activities-module) 和 [System Center Orchestrator 整合模組](#system-center-orchestrator-integration-modules) 到 SMA，但是您必須手動 [重寫您的 runbook](http://technet.microsoft.com/library/dn469262.aspx)。

## Hybrid Runbook Worker

Orchestrator 中的 Runbook 會儲存在資料庫伺服器上，並在 Runbook 伺服器上執行，都是在本機資料中心中。 Azure 自動化中的 Runbook 會儲存在 Azure 雲端，且可以執行在您本機資料中心使用 [混合式 Runbook 背景工作](automation-hybrid-runbook-worker.md)。 這是您將通常會執行從 Orchestrator 轉換的 Runbook 的方式，因為它們的設計是用來在本機伺服器上執行的 。

## 整合套件轉換器

整合套件轉換器會將轉換使用所建立的整合套件 [Orchestrator 整合工具組 (OIT)](http://technet.microsoft.com/library/hh855853.aspx) 可以匯入 Azure 自動化或服務管理自動化的 Windows PowerShell 為基礎的整合模組。

執行整合套件轉換器時，您會看到一個精靈，可讓您選取整合套件 (.oip) 檔案。 然後精靈會列出該整合套件所包含的活動，並可讓您選取要移轉的項目。 完成精靈時，它會建立整合模組，其中包含針對原始整合套件中的每個活動相對應的 Cmdlet。


### 參數

整合套件中的活動的任何屬性都會轉換成整合模組中對應的 Cmdlet 的參數。 Windows PowerShell cmdlet 有一組 [一般參數](http://technet.microsoft.com/library/hh847884.aspx) ，可以搭配所有 cmdlet。 例如，-Verbose 參數會導致 Cmdlet 輸出其工作的詳細資訊。 沒有 Cmdlet 可以具有與一般參數同名的參數。 如果活動具有與一般參數同名的屬性，精靈會提示您為參數提供另一個名稱。

### 監視器活動

監視以開頭的 Orchestrator 中的 runbook [監視活動](http://technet.microsoft.com/library/hh403827.aspx) 並持續執行等候特定事件來叫用。 Azure 自動化不支援監視 Runbook，因此，將不會轉換整合套件中的任何監視活動。 相反地，會在監視活動的整合模組中建立預留位置 Cmdlet。 此 Cmdlet 不具任何功能，但它可讓您安裝使用它的任何轉換的 Runbook。 此 Runbook 將無法在 Azure 自動化中執行，但可加以安裝，使得您可以修改它。

### 無法轉換的整合套件

未使用 OIT 建立的整合套件，無法使用整合套件轉換器來進行轉換。 另外還有一些 Microsoft 提供的整合套件目前無法使用此工具轉換。 這些整合套件的已轉換的版本已被 [供使用者下載](#system-center-orchestrator-integration-modules) ，讓它們可以安裝在 Azure 自動化或服務管理自動化。


## 標準活動模組

Orchestrator 包含一組 [標準活動](http://technet.microsoft.com/library/hh403832.aspx) ，不包含在整合套件，但會使用許多 runbook。 標準活動模組是包含這些活動的每個對等的 Cmdlet 的整合模組。 您必須在匯入使用標準活動的任何轉換的 Runbook 之前，於 Azure 自動化中安裝此整合模組。

除了支援轉換的 Runbook，標準活動模組中的 Cmdlet 也可由熟悉 Orchestrator 的其他人在 Azure 自動化中建置新的 Runbook。 雖然所有標準活動的功能都可使用 Cmdlet 執行的，它們的運作方式可能不同。 已轉換的標準活動模組中的 Cmdlet 的運作將會與對應的活動相同，並使用相同的參數。 這可以幫助現有 Orchestrator Runbook 作者轉換至 Azure 自動化 Runbook。

## System Center Orchestrator 整合模組

Microsoft 提供 [整合套件](http://technet.microsoft.com/library/hh295851.aspx) 用於建立 runbook 以自動化 System Center 元件和其他產品。 這些整合套件有一些目前是基於 OIT，但因為已知的問題，目前無法轉換為整合模組。 [System Center Orchestrator 整合模組](https://www.microsoft.com/download/details.aspx?id=49555) 包含可以匯入 Azure 自動化和服務管理自動化這些整合套件的已轉換的版本。

在這個工具的 RTM 版本時，將會發行可使用整合套件轉換器轉換、基於 OIT 之整合套件的更新版本。 也會提供指引，協助您使用來自非基於 OIT 之整合套件的活動轉換 Runbook。

## Runbook Converter

Runbook 轉換器轉換將 Orchestrator runbook [圖形化 runbook](automation-runbook-types.md#graph-runbooks) 可以匯入 Azure 自動化。

Runbook Converter 會使用可執行轉換、稱為 **ConvertFrom-SCORunbook** 的 Cmdlet 實作為 PowerShell 模組。 安裝工具時，它會建立可載入 Cmdlet 的 PowerShell 工作階段的捷徑。

以下是將 Orchestrator Runbook 轉換並匯入 Azure 自動化的基本程序。 以下各節提供使用工具和處理已轉換的 Runbook 的進一步詳細資料。

1. 從 Orchestrator 匯出一或多個 Runbook。
2. 取得 Runbook 中所有活動的整合模組。
3. 轉換所匯出檔案中的 Orchestrator Runbook。
4. 檢閱記錄檔中的資訊，以驗證轉換並判斷任何必要的手動工作。
4. 將轉換的 Runbook 匯入至 Azure 自動化。
5. 在 Azure 自動化中建立任何必要的資產。
6. 在 Azure 自動化中編輯 Runbook，以修改任何所需的活動。

### 使用 Runbook Converter

**ConvertFrom-SCORunbook** 的語法如下所示：

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string> 

- RunbookPath - 包含要轉換之 Runbook 的匯出檔案的路徑。
- Module - 逗號分隔的整合模組清單，其中包含 Runbook 中的活動。
- OutputFolder - 建立所轉換圖形的 Runbook 之資料夾的路徑。


下列範例命令會在名為 **MyRunbooks.ois_export** 的匯出檔案中轉換 Runbook。 這些 Runbook 使用 Active Directory 和 Data Protection Manager 整合套件。

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks" 

### 記錄檔

Runbook Converter 會在與所轉換的 Runbook 的相同位置中建立下列記錄檔。 如果檔案已經存在，則會以最後一個轉換的資訊加以複寫。

| 檔案| 內容|
|:---|:---|
| Runbook Converter - Progress.log| 轉換的詳細步驟包括成功轉換的每個活動的資訊，以及未轉換的每個活動的警告。|
| Runbook Converter - Summary.log| 最後一個轉換的摘要包括任何警告，以及您需要執行的後續工作，例如：建立變數轉換的 Runbook 所需的變數。|

### 從 Orchestrator 匯出 Runbook

Runbook Converter 可與來自包含一或多個 Runbook 的 Orchestrator 匯出檔案搭配使用。 它會為匯出檔案中的每個 Orchestrator Runbook 建立對應的 Azure 自動化 Runbook。

若要從 Orchestrator 匯出 Runbook，請在 Runbook Designer 中的 Runbook 名稱上按一下滑鼠右鍵，然後選取 [匯出]****。 若要匯出資料夾中的所有 Runbook，請在資料夾名稱上按一下滑鼠右鍵，並選取 [匯出]****。


### Runbook 活動

Runbook Converter 會將 Orchestrator Runbook 中的每個活動轉換成 Azure 自動化中的對應活動。 針對無法轉換的這些活動，會在 Runbook 中建立預留位置活動，並帶有警告文字。 將轉換的 Runbook 匯入 Azure 自動化之後，您必須將這些活動取代為可執行所需功能的有效活動。

中的任何 Orchestrator 活動 [標準活動模組](#standard-activities-module) 會轉換。 不過，在此模組有一些標準 Orchestrator 活動，並不會被轉換。 例如，**傳送平台事件**沒有 Azure 自動化對等項目，因為該事件是 Orchestrator 特有。

[監視活動](https://technet.microsoft.com/library/hh403827.aspx) 不會轉換，因為它們在 Azure 自動化中沒有對等。 例外狀況的監視中的活動 [轉換整合套件](#integration-pack-converter) ，將會轉換為預留位置的活動。

從任何活動 [轉換整合套件](#integration-pack-converter) 會在您提供的整合模組的路徑時轉換 **模組** 參數。 System Center 整合套件，您可以使用 [System Center Orchestrator 整合模組](#system-center-orchestrator-integration-modules)。


### Orchestrator 資源

Runbook Converter 只會轉換 Runbook，而不會轉換其他 Orchestrator 資源，例如計數器、變數或連線。 Azure 自動化中不支援計數器。 支援變數和連線，但是您必須手動建立它們。 記錄檔將會通知您 Runbook 是否需要這類資源，並指定您需要在 Azure 自動化中建立的對應資源，轉換後的 Runbook 才能正常運作。

比方說，Runbook 可能會使用變數來填入活動中的特定值。 已轉換的 Runbook 會轉換活動，並以與 Orchestrator 變數名稱相同的名稱指定 Azure 自動化中的變數資產。 這會記錄在轉換後建立的檔案 **Runbook Converter - Summary.log** 中。 在使用 Runbook 之前，您將必須在 Azure 自動化中手動建立此變數資產。


### 輸入參數

Orchestrator 中的 Runbook 會接受具有**初始化資料**活動的輸入參數。 如果要轉換的 runbook 中包含這個活動中，則 [輸入的參數](automation-graphical-authoring-intro.md#runbook-input-and-output) Azure 自動化中的每個參數的活動建立 runbook。 A [工作流程指令碼控制項](automation-graphical-authoring-intro.md#activities) 活動會在轉換的 runbook 會擷取並傳回每個參數。 Runbook 中使用輸入參數的任何活動會參照此活動的輸出。

使用此策略的原因是最能與 Orchestrator Runbook 中的功能對映。 新圖形化 Runbook 中的活動應該使用 Runbook 輸入資料來源直接參照輸入參數。


### 叫用 Runbook 活動

Orchestrator 中的 Runbook 會使用**叫用 Runbook** 活動來啟動其他 Runbook。 如果要轉換的 Runbook 包含此活動，並且設定了**等候完成**選項，則會為它在轉換後的 Runbook 中建立 Runbook 活動。 如果未設定**等候完成**選項，則會建立使用 **Start-AzureAutomationRunbook** 來啟動 Runbook 的工作流程指令碼活動。 將轉換的 Runbook 匯入 Azure 自動化之後，您必須以活動中指定的資訊修改此活動。




## 相關文章

- [System Center-Orchestrator 2012](http://technet.microsoft.com/library/hh237242.aspx)
- [服務管理自動化](https://technet.microsoft.com/library/dn469260.aspx)
- [混合式 Runbook 背景工作](automation-hybrid-runbook-worker.md)
- [Orchestrator 標準活動](http://technet.microsoft.com/library/hh403832.aspx)







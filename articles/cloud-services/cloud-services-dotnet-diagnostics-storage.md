<properties
  pageTitle="Azure 儲存體中的儲存和檢視診斷資料 |Microsoft Azure"
  description="將 Azure 診斷資料放入 Azure 儲存體並加以檢視"
  services="cloud-services"
  documentationCenter=".net"
  authors="rboucher"
  manager="jwhit"
  editor="tysonn" />
<tags
  ms.service="cloud-services"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="10/21/2015"
  ms.author="robb" />

# Azure 儲存體中儲存和檢視診斷資料

除非您將診斷資料傳輸至 Microsoft Azure 儲存體模擬器或 Azure 儲存體，否則不會永久儲存診斷資料。 一旦位於儲存體，即可利用其中一個可用的工具進行檢視。

## 指定儲存體帳戶

您可指定您要在 ServiceConfiguration.cscfg 檔案中使用的儲存體帳戶。 帳戶資訊會定義為組態設定中的連接字串。 下列範例會顯示為新的雲端服務專案在 Visual Studio 中建立的預設連接字串:


```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

您可以變更此連接字串，以提供 Azure 儲存體帳戶的帳戶資訊。

視所收集的診斷資料類型而定，Azure 診斷會使用 Blob 服務或資料表服務。 下表顯示保存的資料來源以及其格式。

|資料來源|儲存體格式|
|---|---|
|Azure 記錄檔|資料表|
|IIS 7.0 記錄檔|Blob|
|Azure 診斷基礎結構記錄檔|資料表|
|失敗要求追蹤記錄檔|Blob|
|Windows 事件記錄檔|資料表|
|效能計數器|資料表|
|損毀傾印|Blob|
|自訂錯誤記錄檔|Blob|

## 將診斷資料傳輸

若為 SDK 2.5 和更新版本，可以透過組態檔進行傳輸診斷資料的要求。 您可以依照組態中指定的排程間隔來傳輸診斷資料。

SDK 2.4 和前一個您可以要求透過組態檔中以程式設計的方式傳送診斷資料。 程式設計的方式也可讓您進行隨選傳輸。


>[AZURE.IMPORTANT] 將診斷資料傳輸至 Azure 儲存體帳戶時，您就會產生診斷資料所使用之儲存體資源的成本。

## 儲存診斷資料

記錄檔資料會以下列名稱儲存在 Blob 或資料表儲存體中：

**資料表**

- **WadLogsTable** -撰寫的程式碼使用追蹤接聽項的記錄。

- **WADDiagnosticInfrastructureLogsTable** -診斷監視器和組態變更。

- **WADDirectoriesTable** – 診斷監視器所監視的目錄。  這包括 IIS 記錄檔、IIS 失敗要求記錄檔和自訂目錄。  Blob 記錄檔的位置是在 [容器] 欄位中指定，而 Blob 的名稱則是在 RelativePath 欄位中指定。  AbsolutePath 欄位會指出檔案存在於 Azure 虛擬機器上的位置和名稱。

- **WADPerformanceCountersTable** – 效能計數器。

- **WADWindowsEventLogsTable** – Windows 事件記錄檔。

**Blob**

- **wad 控制容器** – (僅適用於 SDK 2.4 和前一個) 包含控制 Azure 診斷的 XML 設定檔。

- **wad {1>wad-iis-failedreqlogfiles** – 包含 IIS 失敗要求記錄檔中的資訊。

- **記錄 iis wad 檔** – 包含 IIS 記錄檔的相關資訊。

- **「 自訂 」** – 設定診斷監視器所監視的目錄為基礎的自訂容器。  將會在 WADDirectoriesTable 中指定此 Blob 容器的名稱。

## 用來檢視診斷資料的工具
有數個工具可用來檢視傳輸至儲存體後的資料。 例如：

- **在 Visual Studio 伺服器總管** -如果您已安裝 Azure Tools for Microsoft Visual Studio，您可以使用伺服器總管] 中的 Azure 儲存體節點，從 Azure 儲存體帳戶檢視唯讀 blob 和資料表資料。 您可以從您的本機儲存體模擬器帳戶顯示資料，也可以從您為 Azure 建立的儲存體帳戶顯示資料。 如需詳細資訊，請參閱 [使用伺服器總管瀏覽儲存體資源](https://msdn.microsoft.com/library/ff683677.aspx)。

- **由 Neudesic azure 存放裝置總管** - [Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/) 是一個實用的圖形化使用者介面工具，以檢查及更改 Azure 儲存體專案包括 Azure 應用程式的記錄檔中的資料。 若要下載此工具，請參閱 [Azure 儲存體總管](http://azurestorageexplorer.codeplex.com/)。

- Cerebrata-由 azure 診斷管理員 [Azure 診斷管理員](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) 是 Windows (WPF) 架構用戶端用來管理 Azure 診斷。 它可讓您檢視、 下載及管理在 Azure 中執行應用程式所收集的診斷資料。 若要下載此工具，請參閱 [Azure 診斷管理員](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx)。

## 後續步驟

[追蹤流程在雲端服務應用程式中使用 Azure 診斷](cloud-services-dotnet-diagnostics-trace-flow.md)


<properties 
    pageTitle="在網站中使用 ReportViewer | Microsoft Azure"
    description="本主題說明如何使用 Visual Studio ReportViewer 控制項 (可顯示儲存在 Microsoft Azure 虛擬機器上的報告)，建置 Microsoft Azure 網站。"
    services="virtual-machines"
    documentationCenter="na"
    authors="rothja"
    manager="jeffreyg"
    editor="monicar" 
    tags="azure-service-management" />
<tags 
    ms.service="virtual-machines"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="12/11/2015"
    ms.author="jroth" />

# 在裝載於 Azure 上的網站中使用 ReportViewer

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)] 資源管理員模型。


您可以使用 Visual Studio ReportViewer 控制項 (可顯示儲存在 Microsoft Azure 虛擬機器上的報告)，建置 Microsoft Azure 網站。 ReportViewer 控制項位於您使用 ASP.NET Web 應用程式範本建置的 Web 應用程式中。

>[AZURE.IMPORTANT] ASP.NET MVC Web 應用程式範本不支援 ReportViewer 控制項。

若要將 ReportViewer 併入 Microsoft Azure 網站中，您需要完成下列工作。

- **新增** 組件的部署套件

- **設定** 驗證和授權

- **發行** ASP.NET Web 應用程式至 Azure

## 先決條件

檢閱中的 < 一般建議和最佳作法 > 一節 [Azure 虛擬機器中 SQL Server Business Intelligence](virtual-machines-sql-server-business-intelligence.md)。

>[AZURE.NOTE] ReportViewer 控制項隨附於 Visual Studio 中，標準版或以上版本。 如果您使用 Web Developer Express 版，您必須安裝 [MICROSOFT REPORT VIEWER 2012 RUNTIME](https://www.microsoft.com/download/details.aspx?id=35747) 使用 ReportViewer 執行階段功能。
>
>Microsoft Azure 不支援在本機處理模式中設定的 ReportViewer。

檢閱這份白皮書 [Reporting Services 報表檢視器控制項和 Microsoft Azure 虛擬機器為基礎的報表伺服器](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)。

## 將組件新增至部署套件

在內部部署裝載 ASP.NET 應用程式後，ReportViewer 組件通常會在 Visual Studio 安裝期間，直接安裝在 IIS 伺服器的全域組件快取 (GAC) 中，並可直接透過應用程式存取。 不過，若您是在雲端中裝載 ASP.NET 應用程式，Microsoft Azure 不允許任何項目安裝至 GAC，因此您必須確定 ReportViewer 組件位於本機上，可供應用程式使用。 作法：將組件的參考加入您的專案中，並設定為可從本機複製。

在遠端處理模式中，ReportViewer 控制項會使用下列組件：

- **Microsoft.ReportViewer.WebForms.dll**: 包含 ReportViewer 程式碼，您必須在網頁中使用 ReportViewer。 將 ReportViewer 控制項拖曳至專案的 ASP.NET 頁面中後，此組件的參考便會加入專案之中。

- **Microsoft.ReportViewer.Common.dll**: 包含 ReportViewer 控制項在執行階段使用的類別。 此組件不會自動加入至您的專案。

### 加入 Microsoft.ReportViewer.Common 的參考

- 以滑鼠右鍵按一下您的專案 **參考** 節點，然後選取 **加入參考**, ，.NET] 索引標籤中選取的組件，然後按一下 **確定**。

### 若要讓組件可由 ASP.NET 應用程式於本機上存取

1. 在 **參考** 資料夾中，按一下 Microsoft.ReportViewer.Common 組件，使其屬性會出現在 [屬性] 窗格中。

1. 在 [屬性] 窗格中，設定 **複製到本機** 設為 True。

1. 對 Microsoft.ReportViewer.WebForms 重複步驟 1 和 2。

### 取得 ReportViewer 語言套件

1. 安裝從適當的 Microsoft Report Viewer 2012 Runtime 可轉散發套件 [Microsoft 下載中心](http://go.microsoft.com/fwlink/?LinkId=317386)。

1. 從下拉式清單中選取語言，然後頁面會重新導向至對應的下載中心頁面。

1. 按一下 [ **下載** 開始下載 ReportViewerLP.exe。

1. 下載 ReportViewerLP.exe 之後，請按一下 [ **執行** 立即安裝，或按一下 **儲存** 將它儲存至您的電腦。 如果您按一下 **儲存**, ，請記住您用來儲存檔案的資料夾名稱。

1. 尋找您儲存檔案的目的地資料夾。 以滑鼠右鍵按一下 ReportViewerLP.exe 中，按一下 **系統管理員身分執行**, ，然後按一下 [ **是**。

1. 執行 ReportViewerLP.exe 之後，您會看見 c:\windows\assembly 具有資源檔案 **{1>microsoft.reportviewer.webforms.resources** 和 **Microsoft.ReportViewer.Common.Resources**。

### 設定當地語系化的 ReportViewer 控制項

1. 依照上述的指示，下載並安裝 Microsoft Report Viewer 2012 Runtime 可轉散發套件。

1. 在專案中建立 <language> 資料夾，並複製該資料夾中的相關聯資源組件檔案。 要複製的資源組件檔案為: **{1>microsoft.reportviewer.webforms.resources.dll** 和 **Microsoft.ReportViewer.Common.Resources.dll**。選取資源組件檔案，然後在 [屬性] 窗格中，設定 **複製到輸出目錄** 至 「**永遠複製**」。

1. 設定 Web 專案的文化特性和 UI 文化特性。 如需如何設定 ASP.NET 網頁的文化特性和 UI 文化特性的詳細資訊，請參閱 [How to: 設定 ASP.NET Web 網頁全球化的文化特性和 UI 文化特性](http://go.microsoft.com/fwlink/?LinkId=237461)。

## 設定驗證和授權

ReportViewer 必須使用正確的認證對報表伺服器進行驗證，而且認證必須由報表伺服器授權，才能存取您需要的報表。 如需驗證資訊，請參閱白皮書 [Reporting Services 報表檢視器控制項和 Microsoft Azure 虛擬機器為基礎的報表伺服器](https://msdn.microsoft.com/library/azure/dn753698.aspx)。

## 將 ASP.NET Web 應用程式發佈至 Azure

在發行至 Azure 的 ASP.NET Web 應用程式上的指示，請參閱 [How to: 移轉並發行 Azure Web 應用程式從 Visual Studio](../vs-azure-tools-migrate-publish-web-app-to-cloud-service.md) 和 [開始使用 Web 應用程式和 ASP.NET](../app-service-web/web-sites-dotnet-get-started.md)。

>[AZURE.IMPORTANT] 如果新增 Azure 部署專案或加入 Azure 雲端服務專案命令不會出現在 [方案總管] 中的捷徑功能表，您可能需要將專案目標 framework 變更為.NET Framework 4。
>
>兩個命令提供的功能基本上相同。 視您安裝的 Microsoft Azure SDK 版本而定，其中一個命令會顯示在捷徑功能表中。

## 資源

[Microsoft 報告](http://go.microsoft.com/fwlink/?LinkId=205399)

[Azure 虛擬機器中的 SQL Server Business Intelligence](virtual-machines-sql-server-business-intelligence.md)

[使用 PowerShell 建立具有原生模式報表伺服器的 Azure VM](virtual-machines-sql-server-create-native-mode-report-server-powershell.md)

[Reporting Services 報告檢視器控制項和 Microsoft Azure 虛擬機器型報表伺服器](http://download.microsoft.com/download/2/2/0/220DE2F1-8AB3-474D-8F8B-C998F7C56B5D/Reporting%20Services%20report%20viewer%20control%20and%20Azure%20VM%20based%20report%20servers.docx)


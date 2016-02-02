<properties
    pageTitle="安裝 Azure Toolkit for Eclipse"
    description="了解如何安裝 Azure Toolkit for Eclipse。"
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="11/19/2015" 
    ms.author="robmcm"/>




# 安裝 Azure Toolkit for Eclipse

Azure Toolkit for Eclipse 提供範本和功能，可讓您輕鬆地使用 Eclipse 開發環境來建立、開發、測試及部署 Azure 應用程式。 它是開放原始碼專案，您可以透過 Apache 授權 2.0 從 GitHub 上的專案網站取得其原始程式碼，URL 如下：

<https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava>.

下列步驟示範如何安裝 Azure Toolkit for Eclipse。

## 必要條件

* Eclipse IDE for Java EE Developers (Indigo 或更新版本)。
* Java Developer Kit (JDK) 1.7 版或更新版本。
* 作業系統 - Azure Toolkit for Eclipse 已在下列作業系統上測試：
    * Windows 10
    * Windows 8 和 Windows 8.1
    * Windows 7
    * Windows Server 2012
    * Windows Server 2008

> [AZURE.IMPORTANT] 如果您在 Windows 上使用 Azure Toolkit for Eclipse，此工具組需要安裝 Azure SDK 2.7 或更新版本。 您有兩個選項可安裝 Azure SDK：
> 
> 
> 
> 
> 請注意，只有 Windows 才需要 Azure SDK。

## 安裝 Azure Toolkit for Eclipse

1. 啟動 Eclipse。
2. 
    ![][ic590123]
3. 
4. 
    ![][ic719482]
5. 
    * **Azure Access Control Services Filter**：這個元件支援使用 Azure ACS 來驗證應用程式使用者。
    * **Azure Common Plugin**：這個元件包含其他元件所依賴的共用功能。
    * **Azure Toolkit for Eclipse**：這個元件包含專案設定邏輯、發行至雲端精靈及使用者介面。
    * **Microsoft JDBC Driver 4.0 for SQL Server**：這個元件可簡化使用 SQL Database 開發應用程式的作業。
    * **Package for Apache Qpid Client Libraries for JMS**：這個元件提供來自 Apache Qpid 專案的 JMS 用戶端程式庫，讓應用程式得以在 Azure 中使用以進階訊息佇列通訊協定 (AMQP) 為基礎的傳訊
    * **Package for Azure Libraries for Java**：這個元件可讓您以 Java 建置 Azure 應用程式，以便充分利用可擴充的 Azure 雲端運算資源。
    * **Application Insights Plugin for Java**：這個元件可讓您將 Azure 的遙測記錄和分析服務用於應用程式和伺服器執行個體上。
6. 按 [下一步]****。 (如果您在安裝此工具組時遇到不尋常的延遲，請確認已取消核取 [在安裝期間連絡所有更新網站來尋找必要軟體]****。)
7. 在 [安裝詳細資料]**** 對話方塊中，按一下 [下一步]****。
8. 在 [檢閱授權]**** 對話方塊中，檢閱授權合約的條款。 如果您接受授權合約的條款，請按一下 [我接受授權合約的條款]****，然後按一下 [完成]****。 (其餘的步驟假設您接受授權合約的條款。 如果您不接受授權合約的條款，請結束安裝程序。)
9. 如果系統提示您重新啟動 Eclipse 以完成安裝，請按一下 [立即重新啟動]****。

## 另請參閱














[azure toolkit for eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529 
[azure java developer center]: http://go.microsoft.com/fwlink/?LinkID=699547 
[creating a hello world application for azure in eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533 
[installing the azure toolkit for eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546 
[web platform installer (webpi)]: http://go.microsoft.com/fwlink/?LinkID=252838 
[what's new in the azure toolkit for eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552 
[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png 
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png 


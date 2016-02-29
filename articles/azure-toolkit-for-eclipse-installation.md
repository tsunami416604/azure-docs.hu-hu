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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690946.aspx -->

# 安裝 Azure Toolkit for Eclipse #

Azure Toolkit for Eclipse 提供範本和功能，可讓您輕鬆地使用 Eclipse 開發環境來建立、開發、測試及部署 Azure 應用程式。 它是開放原始碼專案，您可以透過 Apache 授權 2.0 從 GitHub 上的專案網站取得其原始程式碼，URL 如下：

<https://github.com/MSOpenTech/WindowsAzureToolkitForEclipseWithJava>.

下列步驟示範如何安裝 Azure Toolkit for Eclipse。

## 必要條件 ##

* Eclipse IDE for Java EE Developers (Indigo 或更新版本)。 這可以從下載 <http://www.eclipse.org/downloads/>。
* Java Developer Kit (JDK) 1.7 版或更新版本。 
* 作業系統 - Azure Toolkit for Eclipse 已在下列作業系統上測試：
    * Windows 10
    * Windows 8 和 Windows 8.1
    * Windows 7
    * Windows Server 2012
    * Windows Server 2008

> [AZURE.IMPORTANT] 如果您使用 Azure Toolkit for Eclipse 在 Windows 上，此工具組需要安裝 Azure SDK 2.7 或更新版本。 您有兩個選項可安裝 Azure SDK：
> 
> * 您可以下載並安裝 Azure SDK 使用 [Web Platform Installer (WebPI)][]。
> * 如果您在建立第一個 Azure 部署專案時尚未安裝 Azure SDK，系統將提示您自動下載並安裝必要的 Azure SDK 版本。
> 
> 請注意，只有 Windows 才需要 Azure SDK。

## 安裝 Azure Toolkit for Eclipse ##

1. 啟動 Eclipse。
2. 在 Eclipse 內，於功能表上按一下 <strong>協助</strong>, ，然後按一下 [ <strong>安裝新軟體</strong>, ，如下圖所示。
    ![][ic590123]
3. 在 <strong>可用的軟體</strong> 對話方塊，在 <strong>使用</strong> 文字方塊中輸入 <strong>http://dl.msopentech.com/eclipse</strong> 後面接著 <strong>Enter</strong> 索引鍵。
4. 在 [名稱]<strong></strong> 窗格中，核取 [Azure Toolkit for Eclipse]<strong></strong>，然後取消核取 [在安裝期間連絡所有更新網站來尋找必要軟體]<strong></strong>。 您的畫面看起來應類似下列:
    ![][ic719482]
5. 如果您展開 <strong>適用於 Eclipse 的 Azure 工具組</strong>您會看到下列項目:
    * **Azure Access Control Services Filter**: 此元件來驗證應用程式使用者使用 Azure ACS 提供支援。
    * **Common Plugin**: 此元件包含共用功能依賴由其他元件。
    * **Azure Toolkit for Eclipse**: 此元件含有專案組態邏輯、 發行至雲端精靈] 中，與使用者介面。
    * **Microsoft JDBC Driver 4.0 for SQL Server**: 此元件可簡化應用程式開發使用 SQL 資料庫。
    * **Apache Qpid Client Libraries for JMS 套件**: 此元件提供從 Apache Qpid 專案，以啟用應用程式使用進階訊息佇列通訊協定 AMQP 為基礎的傳訊在 Azure 中的 JMS 用戶端程式庫
    * **Azure libraries for Java 封裝**: 此元件可讓您建置 Azure 應用程式在 Java 中，可讓您充分利用 Azure 可擴充的雲端運算資源。
    * **Java 的 application Insights 外掛程式**: 此元件可讓您使用 Azure 的遙測記錄和分析服務用於您的應用程式和伺服器執行個體。
6. 按一下 [ **下一步**。 (如果您在安裝此工具組時遇到不尋常的延遲，請確認 **若要尋找必要的軟體在安裝期間連絡所有更新網站** 未核取。)
7. 在 **安裝詳細資料** ] 對話方塊中，按一下 [ **下一步**。
8. 在 **檢閱授權** ] 對話方塊中，檢閱授權合約的條款。 如果您接受授權合約的條款，請按一下 [ **我接受授權合約的條款** 然後按一下 [ **完成**。 (其餘的步驟假設您接受授權合約的條款。 如果您不接受授權合約的條款，請結束安裝程序。)
9. 如果提示您重新啟動 Eclipse 以完成安裝，請按一下 [ **立即重新啟動**。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

[適用於 Eclipse 的 Azure 工具組的新功能][]

如需有關如何使用 java 的 Azure 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Web Platform Installer (WebPI)]: http://go.microsoft.com/fwlink/?LinkID=252838 
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic590123]: ./media/azure-toolkit-for-eclipse-installation/ic590123.png
[ic719482]: ./media/azure-toolkit-for-eclipse-installation/ic719482.png


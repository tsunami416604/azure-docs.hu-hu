<properties 
    pageTitle="下載 Azure SDK for Java" 
    description="了解如何下載 Azure SDK for Java，包括可供 Maven 專案使用的範例程式碼，以及 Azure Tookit for Eclipse 的基本安裝步驟。" 
    services="" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="multiple" 
    ms.workload="na" 
    ms.tgt_pltfrm="multiple" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

# 下載 Azure SDK for Java #

本文包含下載及安裝 Azure Libraries for Java。

**注意:** Azure Libraries for Java 分散在 [Apache 授權，2.0 版][license]。

## Azure Libraries for Java - 手動下載 ##

若要手動安裝 Azure Libraries for Java，請按一下 [ <http://go.microsoft.com/fwlink/?LinkId=690320> 下載 ZIP 檔案，其中包含的所有程式庫及所有相依性。

當將 zip 檔案下載到您的電腦之後，請將內容解壓縮，再使用下列選項之一，將 JAR 檔案加入您的專案中：

* 將 JAR 檔案匯入您在 Eclipse 的 Java 專案中。

* 設定 **組建路徑** 的 Eclipse 包含 JAR 檔案的路徑中的 Java 專案。

如需設定在 Eclipse 中的組建路徑的詳細資訊，請參閱 [Java 組建路徑][] Eclipse 網站的發行項。

**注意:** 的 license.txt 和 ThirdPartyNotices.txt ZIP 內的檔案檔案授權及其他資訊，請參閱。

## Azure Libraries for Java - 使用 Maven 建置 ##

### 步驟 1 - 設定使用 Maven 建置專案 ###

在 Eclipse 中使用 Java 的 Azure 程式庫中的指示建立 Maven 專案 [開始使用 Java 的 Azure 管理庫][maven-getting-started] 文件。 

### 步驟 2 - 設定您的 Maven 設定必的相依性 ###

當設定好使用 Maven 建置專案之後，您便可使用類似下列範例所示的語法，將必要的相依性加入 pom.xml 檔案中。 請注意，您無須加入下列範例中所列的每項相依性，而只需加入您專案所需的特定相依性即可。

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-compute</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-network</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-sql</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-storage</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-management-websites</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-media</artifactId>
        <version>0.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-servicebus</artifactId>
        <version>0.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-serviceruntime</artifactId>
        <version>0.6.0</version>
    </dependency>

**注意:** 在每個 `<version>` 項目，在上述範例中，在此範例中的版本號碼取代有效的版本號碼，您可以從取得 [Azure Libraries Repository on Maven][]。

## 安裝 Azure Toolkit for Eclipse ##

此章節包含基本的指示安裝 Azure Toolkit for Eclipse。如需詳細指示，請參閱 [安裝 Azure Toolkit for Eclipse][]。

### 必要條件 ###

1. 列出 Windows operting 系統 [的新功能在 Azure Toolkit for Eclipse][] 文件。
1. 列在 Macintosh 或 Linux operting 系統 [的新功能在 Azure Toolkit for Eclipse][] 文件。
1. Eclipse IDE for Java EE Developers (Indigo 或更新版本)。 這可以從下載 <http://www.eclipse.org/downloads/>。

### 基本安裝步驟 ###

1. 在 Eclipse 中，從 **協助** 功能表上，選取 **安裝新軟體**。
1. 輸入網站位置 <http://dl.msopentech.com/eclipse> 按 **Enter**。
1. 選取的項目，就按一下 **完成**。

Azure Toolkit for Eclipse 會使用最新版的 Azure sdk。 這可以下載使用 Web Platform Installer (WebPI) 從 <http://go.microsoft.com/fwlink/?LinkID=252838>。 不過，如果您沒有安裝此 SDK，則建立第一個 Azure 部署專案時，Azure Toolkit for Eclipse 會自動安裝適當版本的 Azure SDK。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[安裝 Azure Toolkit for Eclipse][] 

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

如需有關如何使用 java 的 Azure 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Libraries Repository on Maven]: http://go.microsoft.com/fwlink/?LinkID=286274
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Java Build Path]: http://help.eclipse.org/luna/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2Freference%2Fref-properties-build-path.htm
[license]: http://www.apache.org/licenses/LICENSE-2.0.html
[maven-getting-started]: http://go.microsoft.com/fwlink/?LinkID=622998
[zip-download]: http://go.microsoft.com/fwlink/?LinkId=690320
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=690333


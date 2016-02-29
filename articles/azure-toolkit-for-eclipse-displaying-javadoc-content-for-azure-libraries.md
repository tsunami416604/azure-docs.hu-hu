<properties
    pageTitle="在 Eclipse 中顯示 Azure Libraries for Java 封裝的 Javadoc 內容"
    description="如何在 Eclipse 中顯示 Azure Libraries 的 Javadoc 內容。"
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
    ms.date="11/30/2015" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh698319.aspx -->

# 在 Eclipse 中顯示 Azure Libraries for Java 封裝的 Javadoc 內容 #

Javadoc 內容與 Azure Libraries for Java 產生關聯時，即可在 Eclipse 環境中檢視 Azure Libraries for Java 的 Javadoc 內容。 下列步驟示範該如何在 Eclipse 中使用此功能。

此程序假設您已將 Azure Library for Java 新增至您的組建路徑。

## 在 Eclipse 中顯示 Azure Libraries for Java 的 Javadoc 內容 ##

* 在 Eclipse 的專案總管中，在 **參考程式庫** 區段的專案中，開啟內容功能表 Azure library for Java JAR。 例如， **microsoft windowsazure api-0.1.0.jar** (版本號碼可能會不同，取決於您所安裝的版本)。
* 按一下 [ **屬性**。
* 內 **屬性** ] 對話方塊中的，在左窗格中，按一下 [ **Javadoc 位置**。  **Javadoc 位置** ] 對話方塊隨即出現。
* 您可以指定 **Javadoc URL**, ，或 **封存中的封存**。
    * 如果您選擇指定 **Javadoc URL**, ，使用 Url，例如 **http://dl.windowsazure.com/javadoc** 或 **http://dl.windowsazure.com/storage/javadoc**。
    * 如果您選擇使用 **封存中的封存**, ，您可以指定外部檔案或工作區檔案。
    選擇後視需要進行瀏覽/驗證。 下列範例將 Azure Libraries for Java 產生關聯對應 Javadoc JAR 的已命名的資料夾在本機下載 **c:\MyAzureJARs**。
    ![][ic553487]
* *選擇性*: 按一下 **驗證**。 這裡可能會顯示 Javadoc JAR 可能發生的問題。
* 按一下 [ **確定**。

與程式庫產生關聯後，Javadoc 內容隨即會顯示在 Eclipse 整合式開發環境 (IDE) 中。 例如，如果 `blob` 在程式碼中定義為 `CloudBlockBlob` 類型，以下為您在程式碼中輸入 `blob.acquireLease` 時，會顯示的 Javadoc 內容的範例：

![][ic553488]

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

[安裝 Azure Toolkit for Eclipse][] 

如需有關如何使用 java 的 Azure 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic553487]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553487.png
[ic553488]: ./media/azure-toolkit-for-eclipse-displaying-javadoc-content-for-azure-libraries/ic553488.png

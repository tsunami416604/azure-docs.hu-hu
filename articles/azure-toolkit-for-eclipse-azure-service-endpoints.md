<properties
    pageTitle="Azure 服務端點"
    description="說明適用於 Eclipse 的 Azure 工具組中 Azure 服務端點的設定。"
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268600.aspx -->

# Azure 服務端點 #

Azure 服務端點會判斷您的應用程式是否已部署至全域 Azure 平台並由該平台管理，Azure 由位於中國的 21Vianet 或私人 Azure 平台運作。  **服務端點** 對話方塊可讓您指定您想要使用哪一個服務端點。 若要開啟 **服務端點** ] 對話方塊中的，在 Eclipse 內，按一下 [ **視窗**, ，按一下 [ **喜好設定**, ，依序展開 **Azure**, ，然後按一下 [ **服務端點**。 設定 **作用中設定** 欄位會決定哪一個 Azure 服務端點做為目前的工作區中的 Azure 專案。

下列顯示 **服務端點** ] 對話方塊。

![][ic719493]

## 設定服務端點 ##

在 **服務端點** 對話方塊中，執行下列動作之一 ︰

* 如果想要使用全域 Azure 平台，從 **作用中設定** 下拉式清單中，選取 **windowsazure.com** 按一下 **確定**。
* 如果您想要使用 Azure 由 21vianet 在中國，提供從 **作用中設定** 下拉式清單中，選取 **windowsazure.cn （中國）** 按一下 **確定**。
* 如果想使用私人 Azure 平台：
    1. 按一下 [ **編輯**。
    2. 會開啟一個對話方塊，通知您， **服務端點** 會關閉對話方塊，並將開啟喜好設定設定檔。 按一下 [ **確定**。
    3. 在 preferencesets.xml 檔案中，建立新的 `preferenceset` 元素。 針對此新元素，建立 `name`、`blob`、`management`、`portalURL` 和 `publishsettings` 屬性，再為其新增對應至私人 Azure 平台的值。 您可使用為現有 `preferenceset` 元素所提供的值作為範本。 **請注意**︰ 所使用的值 `blob` 屬性必須包含在 URL 中的"blob"的文字。
    4. 儲存並關閉 preferencesets.xml。
    5. 重新開啟 **服務端點** ] 對話方塊。
    6. 從 **作用中設定** 下拉式清單中，選取使用中設定您建立，並按一下 [ **確定**。
    7. 一旦您已建立您的私人 Azure 平台 `preferenceset` 項目，您可以變更的值指派給它即可 **編輯** 按鈕 **服務端點** ] 對話方塊。 如有需要，也可以建立多個私人 Azure 平台 `preferenceset` 元素。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[安裝 Azure Toolkit for Eclipse][] 

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

如需有關如何使用 java 的 Azure 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546

<!-- IMG List -->

[ic719493]: ./media/azure-toolkit-for-eclipse-azure-service-endpoints/ic719493.png



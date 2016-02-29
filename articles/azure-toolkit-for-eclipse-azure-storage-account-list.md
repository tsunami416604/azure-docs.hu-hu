<properties
    pageTitle="Azure 儲存體帳戶清單"
    description="使用 Azure Toolkit for Eclipse 來管理您的儲存體帳戶設定"
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

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn205108.aspx -->

# Azure 儲存體帳戶清單 #

Azure 儲存體帳戶可讓下載位置用於您的 JDK、應用程式伺服器和任意元件，以及在使用快取時用於儲存狀態。 Eclipse 維護一份已知儲存體帳戶清單，可供 Eclipse 工作區中的專案使用。 若要開啟 **儲存體帳戶** ] 對話方塊中，用來管理該清單中的，在 Eclipse 內，按一下 [ **視窗**, ，按一下 [ **喜好設定**, ，依序展開 **Azure**, ，然後按一下 [ **儲存體帳戶**。

下列顯示 **儲存體帳戶** ] 對話方塊。

![][ic719496]

您也可以從開啟這個對話方塊 **帳戶** 使用儲存體帳戶，如下所示的對話方塊上的連結:

*  **JDK** ] 索引標籤的 **伺服器組態** ] 對話方塊。
*  **伺服器** ] 索引標籤的 **伺服器組態** ] 對話方塊。
*  **加入元件** ] 對話方塊。
*  **快取** 屬性] 對話方塊。

## 使用發行設定檔匯入您的儲存體帳戶 ##

1. 內 **儲存體帳戶** ] 對話方塊中，按一下 [ **從發行設定檔案匯入**。
2. (如果您已將發行設定檔儲存至本機電腦，則略過這個步驟)。在 **匯入的訂閱資訊** ] 對話方塊中，按一下 [ **下載發行設定檔案**。 如果您尚未登入 Azure 帳戶，系統會提示您登入。 然後會提示您儲存 Azure 發行設定檔 (您可以忽略登入頁面上所顯示的產生指示，這是由 Azure 入口網站提供給 Visual Studio 使用者的指示)。請將其儲存至本機電腦。
3. 仍在 **匯入的訂閱資訊** ] 對話方塊中，按一下 [ **瀏覽** ] 按鈕，選取您在本機儲存之前，發佈設定檔，然後按一下 **開啟**。
4. 按一下 [ **確定** 關閉 **匯入的訂閱資訊** ] 對話方塊。

## 建立新的儲存體帳戶 ##

1. 內 **儲存體帳戶** ] 對話方塊中，按一下 [ **新增**。
2. 內 **加入儲存體帳戶** ] 對話方塊中，按一下 [ **新增**。
3. 內 **新儲存體帳戶** ] 對話方塊中，指定下列值:
    * 儲存體帳戶名稱。
    * 儲存體帳戶的位置。
    * 儲存體帳戶的描述。
    * 儲存體帳戶所屬的訂用帳戶。
4. 按一下 [ **確定** 關閉 **新儲存體帳戶** ] 對話方塊。

建立儲存體帳戶可能需要幾分鐘的時間。 建立之後，請按一下 **確定** 關閉 **加入儲存體帳戶** ] 對話方塊中和新的儲存體帳戶會加入至可用的儲存體帳戶的清單。

## 將現有的儲存體帳戶加入清單中 ##

1. 如果您還沒有 Azure 儲存體帳戶，建立一個列出的步驟 **來建立新的儲存體帳戶區段** 上方。 (或者，您可以建立新的儲存體帳戶在 [Azure 管理入口網站][]。)
2. 內 **儲存體帳戶** ] 對話方塊中，按一下 [ **新增**。
3. 內 **加入儲存體帳戶** ] 對話方塊中，輸入值 **名稱** 和 **便捷鍵**。 這必須是現有 Azure 儲存體帳戶的帳戶名稱和存取金鑰。 使用 **儲存體** 區段 [Azure 管理入口網站][] 若要檢視您的儲存體帳戶名稱和金鑰。 您 **加入儲存體帳戶** 對話方塊將如下所示。

    ![][ic719497]

4. 按一下 [ **確定** 關閉 **加入儲存體帳戶** ] 對話方塊。

## 修改儲存體帳戶以使用新的存取金鑰 ##

1. 內 **儲存體帳戶** ] 對話方塊中，按一下的儲存體帳戶，您想来編輯 **編輯**。
2. 內 **編輯儲存體帳戶存取金鑰** ] 對話方塊中，修改 **便捷鍵** 值。
3. 按一下 [ **確定** 關閉 **編輯儲存體帳戶存取金鑰** ] 對話方塊。

## 從 Eclipse 所維護的清單中移除儲存體帳戶 ##

1. 內 **儲存體帳戶** ] 對話方塊中，按一下的儲存體帳戶，您想来編輯 **移除**。
2. 按一下 [ **確定** 當系統提示您移除儲存體帳戶。

>[AZURE.NOTE] 移除儲存體帳戶，透過 **儲存體帳戶** 對話方塊只會移除它從儲存體帳戶可在 Eclipse 內檢視的清單。 而不會從您的 Azure 訂用帳戶中移除此儲存體帳戶。 此外，當 Eclipse 重新載入您訂用帳戶的詳細資料之後，該儲存體帳戶可能會再次出現於清單中。

## 另請參閱 ##

[適用於 Eclipse 的 Azure 工具組][]

[安裝 Azure Toolkit for Eclipse][] 

[在 Eclipse 中建立適用於 Azure 的 Hello World 應用程式][]

如需有關如何使用 java 的 Azure 的詳細資訊，請參閱 [Azure Java 開發人員中心][]。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[What's New in the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699552

<!-- IMG List -->

[ic719496]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719496.png
[ic719497]: ./media/azure-toolkit-for-eclipse-azure-storage-account-list/ic719497.png


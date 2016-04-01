<properties
   pageTitle="設定具名的驗證認證 | Microsoft Azure"
   description="了解如何提供 Visual Studio 可用來向 Azure 驗證要求的認證，以便將應用程式從 Visual Studio 發佈至 Azure，或用來監視現有的雲端服務。 "
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/02/2015"
   ms.author="tarcher" />

# 設定具名的驗證認證

若要將應用程式從 Visual Studio 發佈至 Azure，或若要監視現有的雲端服務，您必須提供 Visual Studio 可用來向 Azure 驗證要求的認證。 您可以從 Visual Studio 中的幾個位置登入並提供這些認證。 例如，從 [伺服器總管] 中，您可以開啟捷徑功能表 **Azure** 節點，然後選擇 **連接到 Azure**。 登入時，您可以在 Visual Studio 中取得與 Azure 帳戶相關聯的訂用帳戶資訊，而且接下來您無需執行任何動作。

Azure Tools 也支援像以前一樣使用訂用帳戶檔案 (.publishsettings 檔案) 提供認證。 本主題將說明此方法，Azure SDK 2.2 中仍支援此方法。

向 Azure 驗證需要下列項目：

- 訂用帳戶識別碼

- 有效的 X.509 v3 憑證

>[AZURE.NOTE] X.509 v3 憑證的金鑰長度必須至少 2048 位元。 Azure 將會拒絕任何不符合此需求或無效的憑證。

Visual Studio 會使用您的訂用帳戶識別碼連同憑證資料做為認證。 包含憑證公開金鑰的訂用帳戶檔案 (.publishsettings 檔案) 會參考適當的認證。 訂用帳戶檔案可以包含多個訂用帳戶的認證。

您可以編輯的訂閱資訊 **新增/編輯訂閱** 對話方塊中，在本主題稍後所述。

如果您想要自行建立憑證，您可以參考中的指示 [建立並上傳 Azure 的管理憑證](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) ，然後手動將憑證上傳至管理入口網站。

>[AZURE.NOTE] Visual Studio 來管理您的雲端服務需要這些認證不是相同的認證來驗證對 Azure 儲存體服務提出之要求所需。

## 在 Visual Studio 中修改或匯出驗證認證

您也可以設定、 修改或匯出驗證認證 **新訂閱** 對話方塊中，會顯示您執行下列動作 ︰

- 在 **伺服器總管**, ，開啟捷徑功能表 **Azure** 節點，選擇 **管理訂閱**, ，選擇 **憑證** 索引標籤，然後選擇 **新增** 或 **編輯** ] 按鈕。

- 當您發行 Azure 雲端服務從 **發行 Azure 應用程式** 精靈] 中，選擇 [ **管理** 中 **選擇您的訂閱** 清單，然後選擇 [憑證] 索引標籤，然後選擇 **新增** 或 **編輯** ] 按鈕。

下列程序假設 **新訂閱** 對話方塊開啟時。

### 在 Visual Studio 中設定驗證認證

1. 在 **選取現有憑證** 驗證清單中，選擇憑證。

1. 選擇 **的完整路徑複製** ] 按鈕。憑證 （.cer 檔案） 的路徑會複製到剪貼簿。

    >[AZURE.IMPORTANT] 若要發行 Azure 應用程式從 Visual Studio，您必須將此憑證上傳至管理入口網站。

1. 將憑證上傳至 Azure 管理入口網站：

    1. 選擇 Azure 入口網站連結。

          [Azure 管理入口網站](http://go.microsoft.com/fwlink/?LinkID=213885) 隨即開啟。

    1. 使用您的 Microsoft 帳戶登入 Azure 管理入口網站，然後選擇 [ **雲端服務** ] 按鈕。

    1. 選擇您感興趣的雲端服務。

        該服務的頁面便會隨即開啟。

    1. 在 **憑證** 索引標籤上，選擇 [ **上載** ] 按鈕。

    1. 貼上您剛才建立的 .cer 檔案的完整路徑，然後輸入您指定的密碼。



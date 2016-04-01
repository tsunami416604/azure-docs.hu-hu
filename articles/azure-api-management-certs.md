<properties 
    pageTitle="將 Microsoft Azure 管理 API 憑證上傳至入口網站" 
    description="了解如何在 Microsoft Azure 中上傳管理 API 憑證" 
    services="cloud-services" 
    documentationCenter=".net" 
    authors="Thraka" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="na" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/01/2015"
    ms.author="adegeo"/>


# 上傳 Azure 管理 API 管理憑證

管理憑證可讓您使用 Azure 所提供的服務管理 API 進行驗證。 許多程式和工具 (例如 Visual Studio 或 Azure SDK) 會使用這些憑證，將各種 Azure 服務的設定與部署自動化。 這些並不是真的與雲端服務相關。 

>[AZURE.WARNING] 請務必小心 ！ 這些憑證類型允許使用它們進行驗證的任何人管理與他們相關聯的訂用帳戶。 

詳細資訊 （包括建立自我簽署的憑證） 的 Azure 憑證 [可用](cloud-services/cloud-services-certs-create.md#what-are-management-certificates) 您如果需要它。

您也可以使用 [Azure Active Directory](http://azure.microsoft.com/documentation/services/active-directory/) 來驗證用戶端程式碼用於自動化用途。

## 上傳管理憑證

一旦建立管理憑證 (僅包含公開金鑰的 .cer 檔案) 之後，您就可以將其上傳到入口網站。 入口網站提供憑證時，具有符合的憑證 (私密金鑰) 的任何人都可以透過管理 API 連線，並存取相關聯的訂用帳戶的資源。

1. 登入 [Azure 管理入口網站](http://manage.windowsazure.com)。
2. 按一下 [ **設定** （您可能需要向下捲動） 入口網站的左側。 
    
    ![設定](./media/azure-api-management-certs/settings.png)

3. 按一下 [ **管理憑證** ] 索引標籤。

    ![設定](./media/azure-api-management-certs/certificates-tab.png)
    
4. 按一下 [ **上載** ] 按鈕。

    ![設定](./media/azure-api-management-certs/upload.png)
    
5. 填寫對話方塊資訊，並按一下 [完成] **核取記號**。

    ![設定](./media/azure-api-management-certs/upload-dialog.png)

## 後續步驟

您現在已與訂閱相關聯的管理憑證，您可以 （您已安裝在本機上相符的憑證之後） 以程式設計方式連接到 [服務管理 REST API](https://msdn.microsoft.com/library/azure/mt420159.aspx) 和自動化也是與該訂用帳戶相關聯的各種 Azure 資源。 


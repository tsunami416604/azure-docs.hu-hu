<properties
    pageTitle="將 Dropbox API 新增至 PowerApps Enterprise| Microsoft Azure"
    description="在您組織的 App Service 環境中建立或設定新的 Dropbox API"
    services=""
    suite="powerapps"
    documentationCenter="" 
    authors="linhtranms"
    manager="dwerde"
    editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# 在您組織的 App Service 環境中建立新的 Dropbox API

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/), 、 使用工作帳戶登入。 例如，使用登入 */users/yourusername*@*YourCompany*。 com。 當您這樣做時，將會自動登入您的公司訂用帳戶。
 
2. 選取 **瀏覽** 在工作列上:  
![][12]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*:  
![][13]  

4. 在 **PowerApps**, ，請選取 **管理 Api**:  
![瀏覽至已註冊 api][] 4

5. 在 **管理 Api**, ，請選取 **新增** 新增新的 API:  
![加入 API][] 5

6. 輸入描述性 **名稱** 為您的 API。  
    
7. 在 **來源**, ，請選取 **可用 Api** 預先建置的 Api，請參閱 <，然後選取 **Dropbox**:  
![選取 dropbox api][] 6

8. 選取 **設定-設定必要的設定**:  
![設定 dropbox API 設定][] 7

9. 輸入 **應用程式金鑰** 和 **應用程式密鑰** Dropbox 應用程式的值。 如果您還沒有這些值，請參閱本主題中的＜註冊 Dropbox 應用程式以搭配 PowerApps 使用＞一節，建立您需要的金鑰與密碼值。  

    > [AZURE.IMPORTANT] 儲存 **重新導向 URL**。 您在本主題的後半部可能需要此值。

10. 選取 **確定** 完成的步驟。


完成時，新的 Dropbox API 會新增至您的 App Service 環境。


## 選擇性步驟：註冊 Dropbox 應用程式以搭配 PowerApps 使用

如果您現在沒有 Dropbox 應用程式及金鑰與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。 

1. 移至 [Dropbox] [1]，並使用您的帳戶登入。

2. 請移至 Dropbox 開發人員網站，並選取 **我的應用程式**:  
![Dropbox 開發人員網站][] 8

3. 選取 **建立應用程式**:  
![Dropbox 建立應用程式][] 9

4. 在 **Dropbox 平台上建立新的應用程式**:  

    ) 在 **選擇 API**, ，請選取 **Dropbox API**。  
    b) 在 **選擇的存取所需的類型**, ，請選取 **完整投寄箱**。  
    c) 輸入您的應用程式的名稱。  

    ![Dropbox 建立應用程式第 1 頁][] 10  

5. 在應用程式設定頁面中：  

    ) 在 **OAuth 2**, ，請將 **重新導向 URL** 您加入新的 Dropbox API 在 Azure 入口網站 (在本主題中) 時收到的重新導向 url。 選取 **新增**。  
    b) 選取 **顯示** 連結以顯示 **應用程式密鑰**:  

    ![Dropbox 建立應用程式第 2 頁][] 11

Dropbox 應用程式便建立好了。 您可以在 Azure 入口網站的 Dropbox API 組態中使用此應用程式。 


## 摘要和後續步驟
在本主題中，您已將 Dropbox API 新增至 PowersApps Enterprise。 接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式： 

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[4]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[5]: ./media/powerapps-create-api-dropbox/add-api.PNG
[6]: ./media/powerapps-create-api-dropbox/select-dropbox-api.PNG
[7]: ./media/powerapps-create-api-dropbox/configure-dropbox-api.PNG
[8]: ./media/powerapps-create-api-dropbox/dropbox-developer-site.PNG
[9]: ./media/powerapps-create-api-dropbox/dropbox-create-app.PNG
[10]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page1.PNG
[11]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page2.PNG


[12]: ./media/powerapps-create-api-dropbox/browseall.png
[13]: ./media/powerapps-create-api-dropbox/allresources.png

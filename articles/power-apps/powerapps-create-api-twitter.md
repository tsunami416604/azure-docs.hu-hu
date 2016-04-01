<properties
    pageTitle="將 Twitter API 新增至 PowerApps Enterprise| Microsoft Azure"
    description="在您組織的 App Service 環境中建立或設定新的 Twitter API"
    services=""
    suite="powerapps"
    documentationCenter="" 
    authors="rajeshramabathiran"
    manager="dwrede"
    editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# 在您組織的 App Service 環境中建立新的 Twitter API

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/), 、 使用工作帳戶登入。 例如，使用登入 */users/yourusername*@*YourCompany*。 com。 當您這樣做時，將會自動登入您的公司訂用帳戶。 

2. 選取 **瀏覽** 在工作列上 ︰  
![][14]  

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*:  
![][15]  

4. 在 **PowerApps**, ，請選取 **管理 Api**:  
![瀏覽至已註冊的 api][1]

5. 在 **管理 Api**, ，請選取 **新增** 新增新的 API:    
![新增 API][2]

6. 輸入描述性 **名稱** 為您的 API。  
    
7. 在 **來源**, ，請選取 **可用 Api** 選取預先建置的 Api，並選取 **Twitter**:  
![選取 Twitter api][3]

8. 選取 **設定-設定必要的設定**:    
![設定 Twitter API 設定][4]

9. 輸入 *消費者金鑰* 和 *消費者密鑰* Twitter 應用程式。 如果您還沒有這些值，請參閱本主題中的＜註冊 Twitter 應用程式以搭配 PowerApps 使用＞一節，建立您需要的金鑰與密碼值。  

    > [AZURE.IMPORTANT] 儲存 **重新導向 URL**。 您在本主題的後半部可能需要此值。

10. 選取 **確定** 完成的步驟。

完成時，新的 Twitter API 會新增至您的 App Service 環境。


## 選擇性步驟：註冊 Twitter 應用程式以搭配 PowerApps 使用

如果您現在沒有 Twitter 應用程式及金鑰與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。 

1. 移至 [https://apps.twitter.com/](https://apps.twitter.com) 並以您的 twitter 帳戶登入。

2. 選取 **建立新的應用程式**:    
![Twitter 應用程式] 頁面][6]

3. 在 **建立應用程式**:  
   
    a），請輸入 **名稱**。  
    b），請輸入 **描述**。  
    c） 輸入的值 **網站**。  
    d） 組 **回呼 url** 您加入新的 Twitter API 在 Azure 入口網站 （在本主題中） 時收到的重新導向 url。  
    e） 同意開發人員合約，然後選取 **建立 Twitter 應用程式**。  

    ![Twitter 應用程式建立][7]

4. 建立成功後，系統會將您重新導向至應用程式頁面。

Twitter 應用程式便建立好了。 您可以在 Azure 入口網站的 Twitter API 組態中使用此應用程式。 

## 摘要和後續步驟
在本主題中，您已將 Twitter API 新增至 PowersApps Enterprise。 接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式： 

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)


<!--References-->

[1]: ./media/powerapps-create-api-twitter/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-twitter/add-api.PNG
[3]: ./media/powerapps-create-api-twitter/select-twitter-api.PNG
[4]: ./media/powerapps-create-api-twitter/configure-twitter-api.PNG
[6]: ./media/powerapps-create-api-twitter/twitter-apps-page.PNG
[7]: ./media/powerapps-create-api-twitter/twitter-app-create.PNG
[14]: ./media/powerapps-create-api-sqlserver/browseall.png
[15]: ./media/powerapps-create-api-sqlserver/allresources.png


<properties
    pageTitle="將 Salesforce API 新增至 PowerApps Enterprise| Microsoft Azure"
    description="在您組織的 App Service 環境中建立或設定新的 Salesforce API"
    services=""
    suite="powerapps"
    documentationCenter="" 
    authors="rajeshramabathiran"
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


# 在您組織的 App Service 環境中建立新的 Salesforce API

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/), 、 使用工作帳戶登入。 例如，使用登入 */users/yourusername*@*YourCompany*。 com。 當您這樣做時，將會自動登入您的公司訂用帳戶。

2. 選取 **瀏覽** 在工作列上:  
![][14]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*:  
![][15]

4. 在 **PowerApps**, ，請選取 **管理 Api**:    
![瀏覽至已註冊的 API][1]

5. 在 **管理 Api**, ，請選取 **新增** 加入新的 API:  
![Add API][2]

6. 為您的 API 輸入描述性**名稱**。

7. 在 **來源**, ，請選取 **可用 Api** 選取預先建置的 Api，並選取 **Salesforce**:  
![選取 Salesforce api][3]

8. 選取 **設定-設定必要的設定**:  
![進行 dropbox API 設定][7]

9. 輸入您 Salesforce 應用程式的*應用程式金鑰*與*應用程式密碼*值。 如果您還沒有這些值，請參閱本主題中的＜註冊 Salesforce 應用程式以搭配 PowerApps 使用＞一節，建立您需要的金鑰與密碼值。
    > [AZURE.IMPORTANT] 儲存**重新導向 URL**。 您在本主題的後半部可能需要此值。

10. 選取 [**確定**] 以完成步驟。

完成時，新的 Salesforce API 會新增至您的 App Service 環境。


## 選擇性步驟：註冊 Salesforce 應用程式以搭配 PowerApps 使用

如果您現在沒有 Salesforce 應用程式及金鑰與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。

1. 開啟 [Salesforce 開發人員首頁 ][5], ，並以您的 Salesforce 帳戶登入。

2. 在首頁中，選取您的設定檔，然後選取 **安裝**:  
![Salesforce 首頁][6]

3. 選取 [**Create (建立)**]，然後選取 [**Apps (應用程式)**]。 在 **應用程式** 頁面上，選取 **新增** 下 **連接應用程式**:  
![Salesforce 建立應用程式][7]

4. 在 [**New Connected App (新增連接的應用程式)**] 中：

    a) 輸入的值 **連接應用程式名稱**。  
    b) 輸入的值 **API 名稱**。  
    c) 輸入的值 **連絡人電子郵件**。  
    d) 在 _api> (啟用 OAuth 設定) _ 選取 **啟用 OAuth 設定**, ，並設定 **回呼 URL** 您加入新的 Salesforce API 在 Azure 入口網站 (在本主題中) 時收到的重新導向 url。

5. 在 (_s) OAuth scopes_ 下, 面加入下列範圍來 **選取 OAuth 範圍**:

    - 存取與管理您的 Chatter 資料 (chatter_api)
    - 存取與管理您的資料 (api)
    - 允許存取您的唯一識別碼 (openid)
    - 隨時代表您執行要求 (refresh_token，offline_access)

6. **儲存** 您的變更:  
![Salesforce 新的應用程式][8]

Salesforce 應用程式便建立好了。 您可以在 Azure 入口網站的 Salesforce API 組態中使用此應用程式。

## 摘要和後續步驟

在本主題中，您已將 Salesforce API 新增至 PowersApps Enterprise。 接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式：

[加入連接，並讓使用者存取](powerapps-manage-api-connection-user-access.md)



[1]: ./media/powerapps-create-api-salesforce/browse-to-registered-apis.PNG 
[2]: ./media/powerapps-create-api-salesforce/add-api.PNG 
[3]: ./media/powerapps-create-api-salesforce/select-salesforce-api.PNG 
[4]: ./media/powerapps-create-api-salesforce/configure-salesforce-api.PNG 
[5]: https://developer.salesforce.com 
[6]: ./media/powerapps-create-api-salesforce/salesforce-developer-homepage.PNG 
[7]: ./media/powerapps-create-api-salesforce/salesforce-create-app.PNG 
[8]: ./media/powerapps-create-api-salesforce/salesforce-new-app.PNG 
[14]: ./media/powerapps-create-api-salesforce/browseall.png 
[15]: ./media/powerapps-create-api-salesforce/allresources.png 


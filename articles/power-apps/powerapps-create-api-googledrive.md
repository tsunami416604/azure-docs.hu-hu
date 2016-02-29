<properties
    pageTitle="將 Google Drive API 新增至 PowerApps Enterprise| Microsoft Azure"
    description="在您組織的 App Service 環境中建立或設定新的 Google Drive API"
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

# 在您組織的 App Service 環境中建立新的 Google Drive API

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/), 、 使用工作帳戶登入。 例如，使用登入 */users/yourusername*@*YourCompany*。 com。 當您這樣做時，將會自動登入您的公司訂用帳戶。
 
2. 選取 **瀏覽** 在工作列上:  
![][15]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*:  
![][16]  

4. 在 **PowerApps**, ，請選取 **管理 Api**:  
![瀏覽至已註冊 api][] 1

5. 在 **管理 Api**, ，請選取 **新增** 新增新的 API:  
![加入 API][] 2

6. 輸入描述性 **名稱** 為您的 API。  
    
7. 在 **來源**, ，請選取 **可用 Api** 選取預先建置的 Api，並選取 **Google 磁碟機**:  
![選取 [google api 的磁碟機][] 3

8. 選取 **設定-設定必要的設定**:  
![設定 google 機 API 設定][] 4

9. 輸入 *應用程式金鑰* 和 *應用程式密鑰* Google 磁碟機的應用程式。 如果您還沒有這些值，請參閱本主題中的＜註冊 Google 雲端硬碟應用程式以搭配 PowerApps 使用＞一節，建立您需要的金鑰與密碼值。  

    > [AZURE.IMPORTANT] 儲存 **重新導向 URL**。 您在本主題的後半部可能需要此值。

10. 選取 **確定** 完成的步驟。

完成時，新的 Google Drive API 會新增至您的 App Service 環境。


## 選擇性步驟：註冊 Google 雲端硬碟應用程式以搭配 PowerApps 使用

如果您現在沒有 Google 雲端硬碟應用程式及金鑰與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。 

1. 登入 [Google 開發人員主控台] [5]:  
![Google 開發人員主控台][] 6

2. 選取 **建立空白專案**。 

3. 輸入您的應用程式的名稱，即表示同意的條款與條件，然後選取 **建立**:  
![建立新的 google 磁碟機專案][] 7

4. 在成功建立新的專案中，選取 **使用 Google Api**:  
![使用 google api][] 8

5. 在 [概觀] 頁面中選取 **磁碟機 API**:  
![Google 磁碟機 API 概觀][] 9

6. 選取 **啟用 API**:  
![啟用 Google 磁碟機 API][] 10

7. 在 [啟用磁碟機 API，選取 [ **認證**, ，然後選取 **OAuth 2.0 用戶端識別碼**:  
![新增認證][] 12

8. 選取 **設定同意畫面**。

9. 在 **OAuth 同意畫面** 索引標籤上，輸入 **產品名稱**, ，然後選取 **儲存**:  
![設定] 同意畫面][] 13

10. 在 [建立用戶端 ID] 頁面中：  

    ) 在 **應用程式類型**, ，請選取 **Web 應用程式**。  
    b) 輸入用戶端的名稱。  
    c) 將您加入新的 Google 磁碟機 API 在 Azure 入口網站 (在本主題中) 時收到的重新導向 URL 重新導向 URL。  
    d) 選取 **建立**。  

    ![建立用戶端識別碼][] 14 

11. 隨即會顯示所註冊應用程式的用戶端 ID 與用戶端密碼。

Google 雲端硬碟應用程式便建立好了。 您可以在 Azure 入口網站的 Google Drive API 組態中使用此應用程式。 

## 摘要和後續步驟
在本主題中，您已將 Google Drive API 新增至 PowersApps Enterprise。 接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式： 

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-googledrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-googledrive/add-api.PNG
[3]: ./media/powerapps-create-api-googledrive/select-googledrive-api.PNG
[4]: ./media/powerapps-create-api-googledrive/configure-googledrive-api.PNG
[5]: https://console.developers.google.com/
[6]: ./media/powerapps-create-api-googledrive/google-developers-console.PNG
[7]: ./media/powerapps-create-api-googledrive/googledrive-create-project.PNG
[8]: ./media/powerapps-create-api-googledrive/use-google-apis.PNG
[9]: ./media/powerapps-create-api-googledrive/googledrive-api-overview.PNG
[10]: ./media/powerapps-create-api-googledrive/enable-googledrive-api.PNG
[11]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials.PNG
[12]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials-add.PNG
[13]: ./media/powerapps-create-api-googledrive/configure-consent-screen.PNG
[14]: ./media/powerapps-create-api-googledrive/create-client-id.PNG
[15]: ./media/powerapps-create-api-googledrive/browseall.png
[16]: ./media/powerapps-create-api-googledrive/allresources.png

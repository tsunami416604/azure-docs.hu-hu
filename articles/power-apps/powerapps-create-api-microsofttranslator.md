<properties
    pageTitle="新增 Microsoft Translator API 至 PowerApps Enterprise | Microsoft Azure"
    description="在您組織的應用程式服務環境中建立或設定新的 Microsoft Translator API"
    services=""
    suite="powerapps"
    documentationCenter="" 
    authors="linhtranms"
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


# 在您組織的應用程式服務環境中建立新的 Microsoft Translator API

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/), 、 使用工作帳戶登入。 例如，使用登入 */users/yourusername*@*YourCompany*。 com。 當您這樣做時，將會自動登入您的公司訂用帳戶。

2. 選取 **瀏覽** 在工作列上:  
![][7]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*:  
![][8]

4. 在 **PowerApps**, ，請選取 **管理 Api**:  
![瀏覽至已註冊的 API][1]

5. 在 **管理 Api**, ，請選取 **新增** 加入新的 API:  
![Add API][2]

6. 為您的 API 輸入描述性**名稱**。

7. 在 **來源**, ，請選取 **可用 Api** 選取預先建置的 Api，並選取 **Microsoft Translator**:  
![選取 Microsoft Translator API][3]

8. 選取 **設定-設定必要的設定**:  
![設定 Microsoft Translator API 設定][4]

9. 輸入 Microsoft Translator 應用程式的「用戶端識別碼」**和「用戶端密碼」**。 如果您還沒有這些值，請參閱本主題中的＜註冊 Microsoft Translator 應用程式搭配 PowerApps 使用＞一節來建立您需要的識別碼與密碼值。

9. 選取 [確定]**** 完成步驟。

完成時，您的應用程式服務環境中會新增 Microsoft Translator API。


## 選擇性：註冊 Microsoft Translator 應用程式以搭配 PowerApps 使用

如果您現在沒有 Microsoft Translator 應用程式及識別碼與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。


1. 移至 [Azure 資料市場開發人員的頁面 ][5] 並以您的 Microsoft 帳戶登入。

2. 選取 [註冊]****。

3. 在 [註冊您的應用程式]**** 中：

    a)，請輸入 **用戶端識別碼**。  
    b) 輸入 **名稱** 應用程式。  
    c) 輸入的空值 **重新導向 url**。 例如，輸入 *https://contosoredirecturl*。  
    d) 輸入 **描述**。  
    e) 選取 **建立**。

    ![註冊您的應用程式][6]

建立新的 Microsoft Translator 應用程式。 您可以在 Azure 入口網站的 Microsoft Translator API 組態中使用此應用程式。


## 摘要和後續步驟

在本主題中，您已將 Microsoft Translator API 新增至 PowersApps Enterprise。 接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式：

[加入連接，並讓使用者存取](powerapps-manage-api-connection-user-access.md)




[1]: ./media/powerapps-create-api-microsofttranslator/browse-to-registered-apis.PNG 
[2]: ./media/powerapps-create-api-microsofttranslator/add-api.PNG 
[3]: ./media/powerapps-create-api-microsofttranslator/select-microsofttranslator-api.PNG 
[4]: ./media/powerapps-create-api-microsofttranslator/configure-microsofttranslator-api.PNG 
[5]: https://datamarket.azure.com/developer/applications/ 
[6]: ./media/powerapps-create-api-microsofttranslator/register-your-application.PNG 
[7]: ./media/powerapps-create-api-microsofttranslator/browseall.png 
[8]: ./media/powerapps-create-api-microsofttranslator/allresources.png 


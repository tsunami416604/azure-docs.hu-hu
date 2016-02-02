<properties
    pageTitle="新增 Office 365 Outlook API 至 PowerApps Enterprise | Microsoft Azure"
    description="在您組織的應用程式服務環境中建立或設定新的 Office 365 Outlook API"
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


# 在您組織的應用程式服務環境中建立新的 Office 365 Outlook API

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/), 、 使用工作帳戶登入。 例如，使用登入 */users/yourusername*@*YourCompany*。 com。 當您這樣做時，將會自動登入您的公司訂用帳戶。

2. 選取 **瀏覽** 在工作列上:  
![][14]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*:  
![][15]

4. **PowerApps 服務**, ，請選取 **管理 Api**:    
![瀏覽至已註冊的 API][1]

5. 在 **管理 Api**, ，請選取 **新增** 加入新的 API:  
![Add API][2]

6. 為您的 API 輸入描述性**名稱**。

7. 在 **來源**, ，請選取 **可用 Api** 選取預先建置的 Api，並選取 **Office 365 的 Outlook**:  
![選取 Office 365 Outlook API][3]

8. 選取 **設定-設定必要的設定**:  
![進行 Office 365 Outlook API 設定][4]

9. 輸入 Office 365 Azure Active Directory (AAD) 應用程式的「應用程式金鑰」**與「應用程式密碼」**值。 如果您還沒有這些值，請參閱本主題中的＜註冊 AAD 應用程式搭配 PowerApps 使用＞一節來建立您需要的金鑰與密碼值。
    > [AZURE.IMPORTANT] 儲存**重新導向 URL**。 您在本主題的後半部可能需要此值。

10. 選取 [確定]**** 完成步驟。

完成時，新的 Office 365 Outlook API 會新增到您的應用程式服務環境。


## 選擇性：註冊 AAD 應用程式以搭配 PowerApps Office 365 API 使用

如果您現在沒有 AAD 應用程式及金鑰與密碼值，請使用下列步驟建立應用程式，然後取得您需要的值。

1. 開啟 [Azure 入口網站 ][5]。

2. 選取 [瀏覽]****，然後選取 [Active Directory]****。
    >[AZURE.NOTE] 這會在 Azure 傳統入口網站中開啟 Active Directory。  

3. 選取您的組織的租用戶名稱:  
![啟動 Azure Active Directory][6]

4. 選取 **應用程式** 索引標籤，然後選取 **新增**:  
![AAD 租用戶應用程式][7]

5. 在 [**新增應用程式**] 中：

    a) 輸入 **名稱** 應用程式。  
    b) 保留應用程式輸入做為 **Web**。  
    c) 選取 **下一步**。

    ![加入 AAD 應用程式 - 應用程式資訊][8]

6. 在 [應用程式屬性]**** 中：

    a) 輸入您應用程式的**登入 URL**。 因為您要為 PowerApps 與 AAD 進行驗證，將 [登入 url _https://login.windows.net_。  
    b) 輸入有效的 **應用程式識別碼 URI** 應用程式。  
    C) 選取 [**確定**]。

    ![加入 AAD 應用程式 - 應用程式屬性][9]

7. 完成後，系統會將您重新導向至新的 AAD 應用程式。 選取 **設定**:  
![Contoso AAD 應用程式][10]

8. 設定 **回覆 URL** _OAuth 2_ 區段，您可以加入新的 Office 365 Outlook API 在 Azure 入口網站 (在本主題中) 時收到的重新導向 url 下。 接著，請選取 **新增應用程式**:  
![設定 Contoso AAD 應用程式][11]

9. 在 **其他應用程式的權限** 視窗中，選取 **Office 365 Exchange Online**, ，然後選取 **確定**:  
![Contoso 應用程式委派][12]

10. 在 [設定] 頁面中，請注意該 _Office 365 的 Exchange Online_ 加入至其他 applications_ 清單 _Permission。

11. 選取 **委派的權限** _Office 365 的 Exchange Online_，然後選取下列權限:

    - 讀取和寫入使用者連絡人
    - 讀取使用者連絡人
    - 讀取和寫入使用者行事曆
    - 讀取使用者行事曆
    - 以使用者身分傳送郵件
    - 讀取和寫入使用者郵件
    - 讀取使用者郵件

    ![Contoso 應用程式委派權限][13]

Azure Active Directory 應用程式便建立好了。 您可以在 Azure 入口網站的 Office 365 Outlook API 組態中使用此應用程式。

## 摘要和後續步驟

在本主題中，您已將 Office 365 Outlook API 新增至 PowersApps Enterprise。 接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式：

[加入連接，並讓使用者存取](powerapps-manage-api-connection-user-access.md)



[1]: ./media/powerapps-create-api-office365-outlook/browse-to-registered-apis.PNG 
[2]: ./media/powerapps-create-api-office365-outlook/add-api.PNG 
[3]: ./media/powerapps-create-api-office365-outlook/select-office365-outlook-api.PNG 
[4]: ./media/powerapps-create-api-office365-outlook/configure-office365-outlook-api.PNG 
[5]: https://portal.azure.com 
[6]: ./media/powerapps-create-api-office365-outlook/launch-aad.PNG 
[7]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications.PNG 
[8]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-appinfo.PNG 
[9]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-app-properties.PNG 
[10]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app.PNG 
[11]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-configure.PNG 
[12]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.PNG 
[13]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.PNG 
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png 
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png 


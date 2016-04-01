<properties
    pageTitle="將 SharePoint Server API 新增至 PowerApps Enterprise| Microsoft Azure"
    description="在您組織的 App Service 環境中建立或設定新的 SharePoint Server API"
    services=""
    suite="powerapps"
    documentationCenter="" 
    authors="rajram"
    manager="dwrede"
    editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="litran"/>

# 在您組織的 App Service 環境中建立新的 SharePoint Server API

## 在 Azure 入口網站中建立 API

1. 在 [Azure 入口網站](https://portal.azure.com/), 、 使用工作帳戶登入。 例如，使用登入 */users/yourusername*@*YourCompany*。 com。 當您這樣做時，將會自動登入您的公司訂用帳戶。
 
2. 選取 **瀏覽** 在工作列上 ︰  
![][14]

3. 在清單中，您可以捲動以尋找 PowerApps 或輸入 *powerapps*:  
![][15]  

4. 在 **PowerApps**, ，請選取 **管理 Api**:  
![瀏覽至已註冊的 api][5]

5. 在 **管理 Api**, ，請選取 **新增** 新增新的 API:  
![新增 API][6]

6. 輸入描述性 **名稱** 為您的 API。   
7. 在 **來源**, ，請選取 **可用 Api** 選取預先建置的 Api，並選取 **SharePoint Server**。 
8. 選取 **設定-設定必要的設定**。
9. 輸入 *用戶端識別碼* 和 *應用程式金鑰* SharePoint Server 的 Azure Active Directory （AAD 應用程式） 和 *SharePoint URL* 和 *資源識別碼* AAD 的 Proxy 應用程式。 依照下節所列步驟設定您內部部署 SharePoint Server 的連線。  

    > [AZURE.IMPORTANT] 儲存 **重新導向 URL**。 您在本主題的後半部可能需要此值。  
    
10. 選取 **確定** 完成的步驟。

完成時，新的 SharePoint Server API 會新增至您的 App Service 環境。


## 設定內部部署 SharePoint Server 的連線

SharePoint Server 使用 Active Directory 進行使用者驗證。 App Service 環境中的 API 透過 Azure Active Directory (AAD) 進行驗證。 您不必交換使用者的 AAD 權杖及將它轉換成 AD 權杖。 此 AD 權杖之後可用於連接至內部部署服務。

[Azure 應用程式 Proxy (AAD Proxy)](../active-directory-application-proxy-publish.md) 用這項需求。 它是 GA 中的 Azure 服務，可保護對內部部署 Web 應用程式的遠端存取與 SSO。 MSDN 中詳細記錄了啟用 AAD Proxy 的步驟。 概括而言，步驟包括：  

1. [啟用應用程式 Proxy 服務](../active-directory-application-proxy-enable.md) – 這包括 ︰  

    - 在 Azure AD 中啟用應用程式 Proxy
    - 安裝與註冊 Azure 應用程式 Proxy 連接器

2. [使用應用程式 Proxy 發行應用程式](../active-directory-application-proxy-publish.md) – 這包括 ︰  

    - 使用精靈發佈應用程式 Proxy 應用程式。 Proxy 應用程式建立後，請隨即記下內部網路 Sharepoint 網站的外部 URL。
    - 將使用者與群組指派至應用程式。
    - 輸入應用程式 Proxy 連接器使用的進階組態，如 SPN (服務主體名稱)，擷取內部部署的 Kerberos 權杖。

Proxy 應用程式建立後，您必須建立另一個委派給 Proxy 應用程式的 AAD 應用程式。 若要取得同意流程所需的存取權杖與重新整理權杖，這是必要動作。 您可以建立新的 AAD 應用程式依照 [這些指示](../active-directory-integrating-applications.md)。

## 摘要和後續步驟
在本主題中，您已將 Office 365 Outlook API 新增至 PowersApps Enterprise。 接下來，請授與使用者此 API 的存取權，讓使用者能夠將此 API 新增至其應用程式： 

[新增連接並授與使用者存取權](powerapps-manage-api-connection-user-access.md)


<!--References-->
[2]: https://msdn.microsoft.com/library/azure/dn768219.aspx
[3]: https://msdn.microsoft.com/library/azure/dn768214.aspx
[4]: https://msdn.microsoft.com/library/azure/dn768220.aspx
[5]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[6]: ./media/powerapps-create-api-dropbox/add-api.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png



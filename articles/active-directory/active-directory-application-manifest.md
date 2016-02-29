<properties
   pageTitle="了解 Azure Active Directory 應用程式資訊清單 | Microsoft Azure"
   description="提供 Azure AD 應用程式資訊清單上的其他詳細資料，隨附於 Azure AD 租用戶中的每個應用程式組態。"
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/01/2015"
   ms.author="dkershaw;bryanla"/>

# 了解 Azure Active Directory 應用程式資訊清單

與 Azure Active Directory (AD) 整合的應用程式必須向 Azure AD 租用戶註冊，提供應用程式的持續性身分識別組態。 在執行階段參考此組態，啟用允許應用程式透過 Azure AD 外部和代理驗證/授權的案例。 如需 Azure AD 應用程式模型的詳細資訊，請參閱 [新增、 更新及移除應用程式] [新增-UPD-RMV-應用程式] 文件。

## 更新應用程式的身分識別組態

實際上有多個可用的選項可更新應用程式的身分識別組態屬性，隨著功能與困難度而有所不同，包括下列選項：

-  **[Azure 傳統入口網站] [AZURE 傳統的入口網站] 網頁使用者介面** 可讓您更新應用程式的最常見的屬性。 這是更新應用程式屬性最快速且最不容易有錯誤的方法，但無法提供您所有屬性的完整存取權，如同下面兩個方法。
- 您要更新不會顯示在 Azure 傳統入口網站的內容更進階的情況下，您可以修改 **應用程式資訊清單**。 這是本文的重點，並且會在下一節中開始詳細討論。
- 它也可讓 **撰寫的應用程式會使用 [圖形 API] [GRAPH API]** 來更新您的應用程式，而這需要最多精力。 如果您要撰寫管理軟體或需要自動定期更新應用程式屬性，這可能是個不錯的選擇。

## 使用應用程式資訊清單來更新應用程式的身分識別組態
透過 [Azure 傳統入口網站] [AZURE 傳統的入口網站]，您可以管理您的應用程式識別設定，下載並上傳的 JSON 檔案表示法，也就所謂的應用程式資訊清單。 沒有實際的檔案儲存在目錄中 - 應用程式資訊清單只是 Azure AD 圖形 API 應用程式實體上的 HTTP GET 作業，且上傳是應用程式實體上的 HTTP PATCH 作業。

如此一來，若要了解的格式和內容應用程式資訊清單，您必須參考 Graph API [應用程式的實體] [應用程式實體] 文件。 可更新的範例不過執行應用程式上傳資訊清單
包括：

- 宣告您的 web API 所公開的權限範圍 (oauth2Permissions)。 請參閱 「 公開 Web Api 至其他應用程式] 中的主題 [整合的應用程式與 Azure Active Directory] [整合的應用程式-AAD] 針對實作使用者模擬使用 oauth2Permissions 資訊委派權限範圍。 如先前所述，所有的應用程式的實體屬性都記錄在 Graph API [實體和複雜型別參考] [應用程式實體] 參考文件，包括 oauth2Permissions 成員是集合型別 [OAuth2Permission] 的 [應用程式的實體-OAUTH2-權限]。
- 宣告您的應用程式所公開的應用程式角色 (appRoles)。 應用程式的實體 appRole 成員是類型 [AppRole] [應用程式的實體-應用程式角色] 的集合。 請參閱 [角色型存取控制中使用 Azure AD 雲端應用程式] [RBAC-雲端-應用程式-AZUREAD] 一文，以實作範例。
- 宣告已知的用戶端應用程式。
- 要求 Azure AD 對登入使用者發出群組成員資格宣告。  注意：可設定為額外發出有關使用者目錄角色成員資格的宣告。 請參閱 [使用 AD 群組的雲端應用程式中的授權] [AAD-群組-FOR-授權] 一文，以實作範例。
- 可讓您的應用程式支援 OAuth 2.0 隱含授與流程 (適用於內嵌的 JavaScript 網頁或單一頁面應用程式 (SPA))
- 啟用 X509 憑證做為密碼金鑰。 請參閱 [組建服務與背景程式中的應用程式 Office 365] [O365-服務-服務精靈-應用程式] 和 [驗證與 Azure 資源管理員 API 開發人員指南] [DEV-GUIDE-TO-AUTH-WITH-ARM] 文件的實作範例。

應用程式資訊清單也會提供追蹤應用程式註冊狀態的好方法。 因為它可供 JSON 格式使用，所以檔案表示法可以簽入您的原始檔控制，以及應用程式的原始程式碼。

### 逐步執行範例
現在可逐步了解透過應用程式資訊清單更新您的應用程式身分識別組態所需的步驟：

1. 瀏覽至 [Azure 傳統入口網站] [AZURE 傳統的入口網站] 並使用具有服務管理員或共同管理員權限的帳戶登入。


2. 在您驗證之後，向下捲動並選取左瀏覽面板中的 Azure "Active Directory" 延伸模組 (1)，然後按一下您的應用程式註冊所在的 Azure AD 租用戶 (2)。

    ![選取 Azure AD 租用戶][選取 AZURE-AD 的租用戶]


3. 出現 [目錄] 頁面時，按一下頁面頂端的 [應用程式] (1) 以查看在租用戶中註冊的應用程式清單。 然後找到您要在清單中更新的應用程式並按一下它 (2)。

    ![選取 Azure AD 租用戶][選取 AZURE-AD-應用程式]


4. 既然您已選取應用程式的主頁面，請注意頁面底部的「管理資訊清單」功能 (1)。 如果您按一下此連結，系統將提示您下載或上傳 JSON 資訊清單檔案。 按一下 [下載資訊清單] (2)，其後會立即出現下載確認對話方塊，提示您按一下 [下載資訊清單] (3) 加以確認，然後在本機開啟或儲存檔案 (4)。

    ![管理資訊清單，請下載選項][管理資訊清單-下載]

    ![下載資訊清單][下載資訊清單]


5. 在此範例中，我們在本機儲存檔案，讓我們可以在編輯器中開啟、對 JSON 進行變更然後再次儲存。 以下是 JSON 結構在 Visual Studio JSON 編輯器內的外觀。 請注意它所遵循的結構描述 [應用程式的實體] [應用程式的實體] 如我們先前所述:

    ![更新資訊清單 JSON][更新資訊清單]


6. 當您完成更新資訊清單，請返回 Azure 傳統入口網站中的 Azure AD 應用程式頁面，再次按一下 [管理資訊清單] 功能 (1)，但是這次請選取 [上傳資訊清單] 選項 (2)。 類似於下載，您將會看見第二個對話方塊，提示您 JSON 檔案的位置。 按一下 [瀏覽檔案...」 (3)，然後使用 [選擇檔案來上傳] 對話方塊來選取 JSON 檔案 (4)，然後按 [開啟]。 一旦對話方塊消失，請選取 [確定] 核取記號 (5)，隨即會上傳您的資訊清單。  

    ![管理資訊清單上, 傳選項][管理資訊清單-上傳]

    ![上傳資訊清單 JSON][上傳資訊清單]

    ![上傳資訊清單 JSON-確認][上傳資訊清單-確認]

就這麼簡單。 現在您的應用程式可以根據您對資訊清單的變更，使用新的應用程式組態來執行。

## 後續步驟
請使用以下的 DISQUS 註解區段提供意見反應，並協助我們改善與形塑我們的內容。

<!--Image references-->
[MANAGE-MANIFEST-DOWNLOAD]: ./media/active-directory-application-manifest/manage-manifest-download.png
[MANAGE-MANIFEST-UPLOAD]: ./media/active-directory-application-manifest/manage-manifest-upload.png
[DOWNLOAD-MANIFEST]: ./media/active-directory-application-manifest/download-manifest.png
[SELECT-AZURE-AD-APP]: ./media/active-directory-application-manifest/select-azure-ad-application.png
[SELECT-AZURE-AD-TENANT]: ./media/active-directory-application-manifest/select-azure-ad-tenant.png
[UPDATE-MANIFEST]: ./media/active-directory-application-manifest/update-manifest.png
[UPLOAD-MANIFEST]: ./media/active-directory-application-manifest/upload-manifest.png
[UPLOAD-MANIFEST-CONFIRM]: ./media/active-directory-application-manifest/upload-manifest-confirm.png

<!--article references -->
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]: active-directory-integrating-applications.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#ApplicationEntity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType
[AZURE-CLASSIC-PORTAL]: https://manage.windowsazure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/


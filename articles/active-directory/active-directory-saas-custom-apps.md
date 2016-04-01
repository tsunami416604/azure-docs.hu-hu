<properties 
    pageTitle="設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入 | Microsoft Azure" 
    description="了解如何使用 SAML 和密碼 SSO 以自助方式將應用程式連接到 Azure Active Directory" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="11/18/2015" 
    ms.author="asmalser" />

#設定對不在 Azure Active Directory 應用程式庫中的應用程式的單一登入

Azure Active Directory 應用程式庫提供已知能支援的單一登入與 Azure Active Directory，表單中所述的應用程式的清單， [這篇文章](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)。 一旦您 (假設您是 IT 專業人員或組織中的系統整合者) 找到所要連接的應用程式，您就可以遵循應 Azure 管理入口網站中顯示的逐步指示，啟用單一登入。

客戶 [Azure Active Directory Premium](https://msdn.microsoft.com/library/azure/dn532272.aspx) 授權取得這些額外的功能，可以從叫用 **自訂** Azure AD 應用程式庫的類別 ︰

* 任何支援 SAML 2.0 身分識別提供者的應用程式皆可進行自助式連線
* Web 應用程式可在使用密碼型 SSO 的 HTML 登入頁面上進行自助式連線
* 將連結加入至任何應用程式的能力 [Office 365 應用程式啟動程式](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 或 [Azure AD 存取面板](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/#deploying-azure-ad-integrated-applications-to-users)
* 自助使用者佈建使用 SCIM 通訊協定的應用程式的連接 ([此處所述](active-directory-scim-provisioning))

這不僅包括您所使用、但尚未在 Azure AD 應用程式庫中上線的 SaaS 應用程式，也包括您的組織已部署至您所控制的伺服器 (在雲端或內部部署中) 的第三方 Web 應用程式。

注意 ︰ 應用程式開發人員想要測試其應用程式與這項功能之間的相容性可以這樣使用 [免費試用版的 Azure Active Directory Premium](https://azure.microsoft.com/trial/get-started-active-directory/), ，但建議取得 [內部的使用權限授權](https://mspartner.microsoft.com/en/us/pages/membership/internal-use-software.aspx)。

##新增未列出或自訂的應用程式 

若要設定應用程式，登入 Azure 管理入口網站中使用 Azure Active Directory 系統管理員帳戶，並瀏覽至 **Active Directory > [目錄] > 應用程式** 區段中，選取 **新增**, ，然後 **從資源庫新增應用程式**。 

![][1]

在應用程式庫，您可以新增自訂應用程式使用 **自訂** 類別的左側，或選取 **新增未列出的應用程式** 如果找不到您所需的應用程式會顯示在搜尋結果中的連結。 輸入應用程式的名稱之後，您可以設定單一登入選項和行為。 

**快速提示**︰ 最佳做法，請使用搜尋功能來檢查應用程式庫中是否有應用程式已經存在。 如果找到應用程式，且其描述提及「單一登入」，則應用程式已支援同盟單一登入。 

![][2]

新增自訂應用程式所提供的體驗，非常類似於預先整合的應用程式所提供的體驗。 若要開始，請選取 **設定單一登入**。 下一個畫面會呈現下列三個單一登入設定選項，其說明將在後續幾節提供。

![][3]


##Azure AD 單一登入

選取此選項，可為應用程式設定 SAML 型驗證。 若使用此選項，應用程式必須支援 SAML 2.0，您應先收集有關於如何使用應用程式之 SAML 功能的資訊，再繼續作業。 選取之後 **下一步**, ，將提示您輸入三個不同的 Url 對應至應用程式的 SAML 端點。 

![][4]

對話方塊中的工具提示圖示會詳細說明每個 URL 是什麼及其使用方式。 這些輸入之後，請按一下 **下一步** 以前往下一個畫面。 此畫面會提供相關資訊來說明在應用程式端需要進行哪些設定，才能使應用程式接受來自於 Azure AD 的 SAML 權杖。 

![][5]

所需的值會隨應用程式而不同，請查看應用程式的 SAML 文件以取得詳細資訊。  **登入** 和 **登出** 服務都解析為相同的端點，也就是您的 Azure AD 的執行個體的 SAML 要求處理端點的 URL。 「簽發者 URL」是在發出給應用程式的 SAML 權杖內顯示為「簽發者」的值。 

設定您的應用程式之後，按一下 [ **下一步** ] 按鈕，然後 **完成** 以關閉對話方塊。 

##將使用者和群組指派給您的 SAML 應用程式 

您的應用程式在設定為使用 Azure AD 作為 SAML 型身分識別提供者之後，就幾乎可以進行測試了。 為了控制安全性，Azure AD 不會核發允許他們登入應用程式的權杖，除非他們已使用 Azure AD 獲得存取存取權。 使用者可以直接獲得存取權，或透過其所屬的群組取得。 

若要將使用者或群組指派給您的應用程式中，按一下 [ **指派使用者** ] 按鈕。 選取使用者或群組來指派，然後選取您想 **指派** ] 按鈕。 

![][6]

指派使用者可讓 Azure AD 為該使用者核發權杖，並使此應用程式的圖格出現在使用者的存取面板中。 如果使用者使用 Office 365，則也會有應用程式圖格出現在 Office 365 應用程式啟動器中。 

您可以上傳的應用程式使用磚標誌 **上傳標誌** 按鈕 **設定** 應用程式] 索引標籤。 

###自訂在 SAML 權杖中發出的宣告 

當使用者對應用程式進行驗證時，Azure AD 會將 SAML 權杖發出給應用程式，其中包含可唯一識別使用者的使用者相關資訊 (或宣告)。 根據預設，其中包含使用者的使用者名稱、電子郵件地址、名字和姓氏。 

您可以檢視或編輯在 SAML 權杖，以在應用程式中傳送的宣告 **屬性** ] 索引標籤。 

![][7]

有兩個可能的原因，您可能需要編輯 SAML 權杖中發出的宣告 ︰ 
已寫入 • 應用程式需要一組不同的宣告 Uri 或宣告的值 
已將 •Your 應用程式部署需要 NameIdentifier 宣告，是儲存在 Azure Active Directory 中的使用者名稱 （也稱為使用者主體名稱） 以外的方式。 

如需如何新增和編輯這些案例的宣告資訊，請參閱 [上宣告自訂文件](active-directory-saml-claims-customization.md)。 

###測試 SAML 應用程式 

在 Azure AD 中和應用程式中設定 SAML URL 和憑證、將使用者或群組指派給 Azure 中的應用程式，並且已視需要檢視和編輯宣告之後，使用者即可登入應用程式。 

若要測試，只要登入 Azure AD 存取面板] https://myapps.microsoft.com 使用使用者帳戶，您指派給應用程式，再開始進行單一登入程序的應用程式磚。 或者，您可以直接瀏覽至應用程式的 [登入 URL]，並從該處登入。 

進行偵錯秘訣，請參閱此 [如何偵錯 SAML 型單一登入應用程式的發行項](active-directory-saml-debugging.md) 

##密碼單一登入

選取此選項可設定 [密碼型單一登入](active-directory-appssoaccess-whatis.md) web 應用程式具有 HTML 登入頁面。 密碼 SSO 也稱為密碼儲存庫存，可讓您管理使用者對不支援身分識別同盟之 Web 應用程式的存取和密碼。 如果有數個使用者需要共用單一帳戶 (例如共用組織的社交媒體應用程式帳戶)，這也很有用處。 

選取之後 **下一步**, ，將提示您輸入的登入網頁應用程式的網頁 URL。 請注意，這必須是包含使用者名稱和密碼輸入欄位的頁面。 輸入之後，Azure AD 會啟動程序來剖析使用者名稱輸入和密碼輸入的登入頁面。 如果此程序不成功，系統會引導您執行安裝瀏覽器延伸模組 (需要 Internet Explorer、Chrome 或 Firefox) 的替代程序，以讓您手動擷取欄位。

一旦擷取登入頁面，可能會指派使用者和群組，而可以設定認證原則，就像一般 [密碼 SSO 應用程式](active-directory-appssoaccess-whatis.md)。

注意 ︰ 您可以上傳的應用程式使用磚標誌 **上傳標誌** 按鈕 **設定** 應用程式] 索引標籤。 

##現有單一登入

選取此選項，可將應用程式的連結新增至組織的 Azure AD 存取面板或 Office 365 入口網站。 使用此選項，可讓您新增目前使用 Azure Active Directory 同盟服務 (或其他同盟服務)、而不是使用 Azure AD 的自訂 Web 應用程式的連結，以進行驗證。 或者，您可以新增特定 SharePoint 網頁或其他只要出現在使用者存取面板上的網頁的深層連結。 

選取之後 **下一步**, ，將提示您輸入的 URL 連結至應用程式。 完成後，使用者和群組可能會指派給應用程式，讓應用程式會出現在 [Office 365 應用程式啟動程式](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 或 [Azure AD 存取面板](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/#deploying-azure-ad-integrated-applications-to-users) 這些使用者。

注意 ︰ 您可以上傳的應用程式使用磚標誌 **上傳標誌** 按鈕 **設定** 應用程式] 索引標籤。 

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png



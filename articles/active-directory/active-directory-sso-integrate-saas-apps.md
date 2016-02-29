<properties
    pageTitle="整合 Azure Active Directory 單一登入 SaaS 應用程式 | Microsoft Azure"
    description="在 Azure Active Directory 中啟用 SaaS 應用程式的單一登入驗證和使用者佈建集中式存取管理。 如何將 Azure Active Directory 與 SaaS 應用程式整合的概觀。"
    services="active-directory"
      keywords="integrate Azure AD with SaaS apps"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

   <tags
      ms.service="active-directory"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="identity"
      ms.date="12/01/2015"
      ms.author="curtand"/>

# 整合 Azure Active Directory 單一登入與 SaaS 應用程式  

[AZURE.INCLUDE [active-directory-sso-use-case-intro](../../includes/active-directory-sso-use-case-intro.md)]

若要開始為將於組織部署的應用程式設定單一登入，您將使用 Azure Active Directory (Azure AD) 中的現有目錄。 您可以使用從 Microsoft Azure、Office 365 或 Windows Intune 取得的 Azure AD 目錄。 如果您有兩個或多個這些，請參閱 [管理 Azure AD 目錄](active-directory-administer.md) 來判斷要使用哪一個。

## 驗證

對於應用程式支援 SAML 2.0、WS-同盟或 OpenID Connect 通訊協定的應用程式，Azure Active Directory 使用簽章憑證來建立信任關係。 如需詳細資訊，請參閱 [管理憑證的同盟單一登入](active-directory-sso-certs.md)。

對於僅支援 HTML 表單型登入的應用程式，Azure Active Directory 使用「密碼儲存庫存」來建立信任關係。 這可讓您組織中的使用者，使用 SaaS 應用程式的使用者帳戶資訊，由 Azure AD 自動登入 SaaS 應用程式。 Azure AD 會收集並安全地儲存使用者帳戶資訊和相關的密碼。 如需詳細資訊，請參閱 [密碼型單一登入](active-directory-appssoaccess-whatis.md\#password-based-single-sign-on)。

## Authorization

佈建的帳戶可授與通過單一登入驗證的使用者使用應用程式的權限。 使用者佈建可以手動方式完成，或是在某些情況下，您可以依據在 Azure Active Directory 中所做的變更來新增和移除 SaaS 應用程式中的使用者資訊。 如需有關如何使用現有的 Azure AD 連接器，以自動佈建的詳細資訊，請參閱  [自動化使用者佈建和解除佈建 SaaS 應用程式](active-directory-saas-app-provisioning.md)

否則，您可以手動將使用者資訊新增至應用程式，或使用市集中可用的其他佈建解決方案。

## Access

Azure AD 提供幾種可自訂的方式，來對您組織中的使用者部署應用程式。 您不會受限於任一特定的部署或存取解決方案。 您可以使用 [最適合您需求的解決方案](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)。

## 已使用中的應用程式的其他考量

為組織已使用的應用程式設定單一登入是與為新應用程式建立新帳戶不同的程序。 有幾個基本步驟包括：將應用程式中的使用者身分識別對應到 Azure AD 身分識別，以及了解整合之後使用者如何體驗登入應用程式。

> [AZURE.NOTE] 若要為現有的應用程式設定 SSO，您必須在這兩個 Azure AD 全域系統管理員權限和 SaaS 應用程式。

### 對應使用者帳戶

使用者的身分識別通常有唯一身分識別碼，可能是電子郵件地址或通用個人名稱 (UPN)。 您必須將每個使用者的應用程式身分識別連結 (對應) 至其各自的 Azure AD 身分識別。 根據您的應用程式驗證的需求，有好幾種方法可完成這項作業。

如需對應應用程式識別與 Azure AD 身分識別的詳細資訊，請參閱 [自訂 SAML 權杖中發出的宣告](http://social.technet.microsoft.com/wiki/contents/articles/31257.azure-active-directory-customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps.aspx) 和 [自訂佈建的屬性對應](active-directory-saas-customizing-attribute-mappings.md)。

### 了解使用者的登入體驗

當您為已在使用中的應用程式整合 SSO 時，請務必了解使用者經驗將會受到影響。 對於所有應用程式，使用者將會開始使用其 Azure AD 認證來登入。 他們也可能需要使用不同的入口網站來存取應用程式。

對於某些應用程式的 SSO 也可以透過在應用程式的登入介面，但其他應用程式，使用者必須通過中央入口網站，例如 ([我的應用程式](http://myapps.microsoft.com) 或 [Office365](http://portal.office.com/myapps)) 登入。 深入了解不同類型的 SSO 和使用者經驗 [什麼是應用程式存取和單一登入與 Azure Active Directory](active-directory-appssoaccess-whatis.md)。

另一個寶貴資源， *隱藏使用者同意* 中 [Guiding 開發人員](active-directory-applications-guiding-developers-for-lob-applications.md) 文件。

## 後續步驟


針對您在應用程式庫中找到的 SaaS 應用程式，Azure AD 提供許多 [如何整合 SaaS 應用程式的教學課程](active-directory-saas-tutorial-list.md)。

如果應用程式不在應用程式庫中，您可以 [加入做為自訂 Azure AD 應用程式庫
應用程式](http://blogs.technet.com/b/ad/archive/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-gt-now-in-preview.aspx)。

沒有更多有關 Azure.com 文件庫中的這些問題的詳細資料
開頭為 [什麼是應用程式存取和單一登入與 Azure Active Directory。](active-directory-appssoaccess-whatis.md)。


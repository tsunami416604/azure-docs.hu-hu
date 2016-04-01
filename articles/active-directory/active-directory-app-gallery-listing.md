<properties
   pageTitle="在 Azure Active Directory 應用程式庫中列出您的應用程式"
   description="如何列出 Azure Active Directory 組件庫中支援單一登入的應用程式 | Microsoft Azure"
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/29/2015"
   ms.author="mbaldwin"/>


# 在 Azure Active Directory 應用程式庫中列出您的應用程式

若要列出支援單一登入與 Azure Active Directory 中的應用程式 [Azure AD 庫](http://azure.microsoft.com/marketplace/active-directory/all/), ，首先必須實作下列整合模式的其中一個應用程式 ︰

* **OpenID Connect** -使用 OpenID Connect 的驗證和 Azure AD 同意組態 API 與 Azure AD 直接整合。 如果您是剛開始整合，而您的應用程式不支援 SAML，這是建議的模式。

* **SAML** – 應用程式已經有設定使用 SAML 通訊協定的協力廠商身分識別提供者的能力。

每個模式的列出需求如下。

##OpenID Connect 整合

若要整合應用程式與 Azure AD 中，下列 [開發人員指示](active-directory-authentication-scenarios.md)。 然後完成下面問題，並傳送到 waadpartners@microsoft.com。

* 提供可由 Azure AD 小組搭配您的應用程式來測試整合的測試租用戶或帳戶的認證。  

* 提供有關如何登入和 Azure AD 的執行個體連接到您的應用程式使用 Azure AD 小組可以指示 [Azure AD 同意架構](https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/#overview-of-the-consent-framework)。 

* 提供 Azure AD 小組搭配您的應用程式測試單一登入所需的任何進一步指示。 

* 提供下列資訊：

> 公司名稱：
> 
> 公司網站：
> 
> 應用程式名稱：
> 
> 應用程式描述 (256 個字元的限制)：
> 
> 應用程式網站 (資訊)：
> 
> 應用程式技術支援網站或連絡資訊：
> 
> 用戶端識別碼的應用程式，在 https://manage.windowsazure.com 應用程式詳細資料中所示 ︰
> 
> 客戶前往註冊和 (或) 購買應用程式的應用程式註冊 URL：
> 
> 選擇要為您的應用程式列出的最多三個類別 (如需可用的類別，請參閱 Azure Active Directory Marketplace)：
> 
> 附加應用程式小型圖示 (PNG 檔案、45px x 45px、背景純色)：
> 
> 附加應用程式大型圖示 (PNG 檔案、215px x 215px、背景純色)：
> 
> 附加應用程式標誌 (PNG 檔案、150px x 122px、透明背景色彩)：

##SAML 整合

支援 SAML 2.0 的任何應用程式可以直接與使用 Azure AD 租用戶整合 [這些指示來新增自訂應用程式](active-directory-saas-custom-apps.md)。 一旦您已經測試您的應用程式整合，適用於 Azure AD 的、 傳送下列資訊以 <waadpartners@microsoft.com>。

* 提供可由 Azure AD 小組搭配您的應用程式來測試整合的測試租用戶或帳戶的認證。  

* SAML 登入 URL、 簽發者 URL (實體 ID) 和回覆 URL （判斷提示取用者服務） 的值提供應用程式，所述 [這裡](active-directory-saas-custom-apps.md)。 如果您通常會提供這些值做為一個 SAML 中繼資料檔的一部分，也請一併傳送該檔案。

* 提供如何在使用 SAML 2.0 的應用程式中設定 Azure AD 做為身分識別提供者的簡短描述。 如果您的應用程式支援透過自助系統管理入口網站來設定 Azure AD 做為身分識別提供者，請確認以上提供的認證包含執行這項設定所需的能力。

* 提供下列資訊：

> 公司名稱：
> 
> 公司網站：
> 
> 應用程式名稱：
> 
> 應用程式描述 (256 個字元的限制)：
> 
> 應用程式網站 (資訊)：
> 
> 應用程式技術支援網站或連絡資訊：
> 
> 客戶前往註冊和 (或) 購買應用程式的應用程式註冊 URL：
> 
> 選擇要在列出的應用程式最多三個類別 (如可用的類別，請參閱 [Azure Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/))):
> 
> 附加應用程式小型圖示 (PNG 檔案、45px x 45px、背景純色)：
> 
> 附加應用程式大型圖示 (PNG 檔案、215px x 215px、背景純色)：
> 
> 附加應用程式標誌 (PNG 檔案、150px x 122px、透明背景色彩)：



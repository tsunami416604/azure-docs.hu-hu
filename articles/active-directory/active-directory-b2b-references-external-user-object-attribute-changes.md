<properties
   pageTitle="外部使用者物件屬性變更為 Azure Active Directory B2B 共同作業預覽 |Microsoft Azure"
   description="Azure Active Directory B2B 藉由讓商務夥伴，選擇性地存取公司的應用程式支援跨公司的關聯性"
   services="active-directory"
   authors="viv-liu"
   manager="cliffdi"
   editor=""
   tags=""/>

<tags
   ms.service="active-directory"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/27/2015"
   ms.author="viviali"/>


# Azure Active Directory (Azure AD) B2B 共同作業預覽版本的外部使用者物件屬性變更

Azure AD 目錄中的每個使用者都以使用者物件形式呈現。 Azure AD 中的使用者物件會在 B2B 共同作業邀請/兌換流程的各種階段經歷屬性變更。 使用者物件代表目錄中具有會在兌換時 (夥伴使用者按一下邀請電子郵件中的連結) 變更屬性的夥伴使用者。 具體而言：

- **SignInName** 和 **AltSecId** 屬性已填入
- **DisplayName** 屬性從使用者主要名稱 (user_fabrikam.com#EXT#@contoso.com) 變更為登入名稱 (user@fabrikam.com)

在 Azure AD 中追蹤這些屬性，有助您疑難排解夥伴使用者是否已兌換其 B2B 共同作業邀請。

## 相關文章

請瀏覽有關 Azure B2B 共同作業的其他文章：

- [什麼是 Azure AD B2B 共同作業?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [它的運作方式](active-directory-b2b-how-it-works.md)
- [詳細的逐步解說](active-directory-b2b-detailed-walkthrough.md)
- [CSV 檔案格式的參考](active-directory-b2b-references-csv-file-format.md)
- [外部使用者的權杖格式](active-directory-b2b-references-external-user-token-format.md)
- [目前的預覽限制](active-directory-b2b-current-preview-limitations.md)






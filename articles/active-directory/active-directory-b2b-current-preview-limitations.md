<properties
   pageTitle="Azure Active Directory B2B 共同作業的目前預覽限制 |Microsoft Azure"
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
   ms.workload="identity"
   ms.date="10/27/2015"
   ms.author="viviali"/>


# Azure Active Directory (Azure AD) B2B 共同作業的目前預覽限制

- 外部使用者無法使用 Multi-Factor Authentication (MFA)。 例如，如果 Contoso 有 MFA，但合作夥伴組織沒有，則合作夥伴組織的使用者無法透過 B2B 共同作業獲得 MFA。
- 只能透過 CSV 邀請；不支援個別的邀請和 API 存取。
- 只有 Azure AD 全域系統管理員可以上傳 .csv 檔案。
- 邀請給取用者的電子郵件地址 (例如 hotmail.com、 Gmail.com 或 comcast.net) 目前不支援。
- 外部使用者存取內部部署應用程式尚未經過測試。
- 從實際使用者的目錄中刪除實際使用者時，並不會自動清除外部使用者。
- 不支援邀請給通訊群組清單。
- 透過 CSV 最多可以上傳 2,000 筆記錄。

## 相關文章

請瀏覽有關 Azure B2B 共同作業的其他文章：

- [什麼是 Azure AD B2B 共同作業?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [它的運作方式](active-directory-b2b-how-it-works.md)
- [詳細的逐步解說](active-directory-b2b-detailed-walkthrough.md)
- [CSV 檔案格式的參考](active-directory-b2b-references-csv-file-format.md)
- [外部使用者的權杖格式](active-directory-b2b-references-external-user-token-format.md)
- [外部使用者物件屬性變更](active-directory-b2b-references-external-user-object-attribute-changes.md)






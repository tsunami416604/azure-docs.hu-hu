<properties
   pageTitle="Azure Active Directory 驗證通訊協定 | Microsoft Azure"
   description="Azure Active Directory (AD) 所支援驗證通訊協定的概觀"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   services="active-directory"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/17/2015"
   ms.author="mbaldwin"/>


# Azure Active Directory 驗證通訊協定

Azure Active Directory (Azure AD) 支援數個最常用的驗證和授權通訊協定。 本節中的主題描述 Azure AD 中所支援的通訊協定和其實作。 主題包括檢閱支援的宣告類型，使用同盟中繼資料的簡介、 詳細的 OAuth 2.0。 和 SAML 2.0 通訊協定參考文件，以及疑難排解小節。

## 驗證通訊協定的文件和參考資料

- [重要簽署金鑰變換的相關資訊在 Azure AD 中](https://msdn.microsoft.com/library/azure/dn641920.aspx) – 了解 Azure AD 的簽署金鑰變換模式，您可以的變更為索引鍵自動更新，以及如何更新最常見的應用程式案例的討論。

- [支援的權杖和宣告類型](active-directory-token-and-claims.md) -了解宣告的權杖中 Azure AD 所發出。

- [同盟中繼資料](https://msdn.microsoft.com/library/azure/dn195592.aspx) -了解如何尋找並解譯 Azure AD 產生的中繼資料文件。

- [Azure AD 中的 OAuth 2.0](https://msdn.microsoft.com/library/azure/dn645545.aspx) -了解 Azure AD 中的 OAuth 2.0 實作。

- [OpenID Connect 1.0](https://msdn.microsoft.com/library/azure/dn645541.aspx) -了解如何使用 OAuth 2.0 授權通訊協定進行驗證。

- [SAML 通訊協定參照](https://msdn.microsoft.com/library/azure/dn195591.aspx) -了解 Azure AD 單一登入和單一登出 SAML 設定檔。

- [WS-同盟 1.2](https://msdn.microsoft.com/library/azure/dn903702.aspx) -Azure AD 中深入了解 WS-同盟 1.2。

- [疑難排解驗證通訊協定](https://msdn.microsoft.com/library/azure/dn195584.aspx) -了解如何避免發生問題及解譯和解決錯誤時使用 Azure AD。



## 另請參閱

[Azure Active Directory 開發人員指南](active-directory-developers-guide.md)

[使用 Azure AD 進行驗證](../app-service-web/web-sites-authentication-authorization.md)

[Active Directory 程式碼範例](active-directory-code-samples.md)






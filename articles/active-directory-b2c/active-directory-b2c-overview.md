<properties
    pageTitle="Azure Active Directory B2C 預覽：概觀 | Microsoft Azure"
    description="使用 Azure Active Directory B2C 開發取用者導向應用程式"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor=""/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/28/2015"
    ms.author="swkrish"/>


# Azure Active Directory B2C 預覽：在您的應用程式中註冊與登入取用者

**Azure Active Directory B2C** 是適用於取用者導向 Web 與行動應用程式的全方位雲端身分識別管理解決方案。 其為高可用性的全域服務，可針對數億萬的取用者身分識別進行級別調整。 Azure Active Directory B2C 建置於企業級安全平台，確保讓您的應用程式、商務和取用者受到安全防護。

在過去，應用程式開發人員若想要註冊並讓取用者登入其應用程式，他們會編寫自己的程式碼。而且他們會使用內部部署資料庫或系統來儲存使用者名稱和密碼。Azure Active Directory B2C 為開發人員提供更理想的做法，透過安全且以標準為基礎的平台以及豐富的可延伸原則組合，協助將取用者身分識別管理整合至應用程式。使用 Azure Active Directory B2C 可讓取用者使用其現有的社交帳戶 (Facebook、Google、Amazon、LinkedIn) 註冊應用程式，或是建立新的認證 (電子郵件地址與密碼，或使用者名稱與密碼)；後者稱為「本機帳戶」。

Azure Active Directory B2C 處於預覽版狀態。 我們冀望在此期間能聆聽您的寶貴意見反應與試用體驗。 我們可能會根據這些意見反應進行重大變更，以改善服務。 在這段期間，請勿使用預覽版發行生產應用程式。 讓我們使用您的想法 [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory)。

## 開始使用

若要建置可接受取用者註冊與登入的應用程式，您必須先使用 Azure Active Directory B2C 租用戶註冊該應用程式。取得使用中所述的步驟您自己租用戶 [文章](active-directory-b2c-get-started.md)。

您可以撰寫您的應用程式對 Azure Active Directory B2C 服務直接傳送通訊協定訊息是選擇使用 [OAuth 2.0](active-directory-b2c-protocols.md#oauth2-authorization-code-flow) 或 [Openid Connect](active-directory-b2c-protocols.md#openid-connect-sign-in-flow) 或使用我們的程式庫為您執行工作 (選擇您最愛的平台下方並開始使用)。

[AZURE.INCLUDE [active-directory-b2c-quickstart-table](../../includes/active-directory-b2c-quickstart-table.md)]

## 新功能

請不時返回此處查看，瞭解關於 Azure Active Directory B2C 預覽未來變更的相關資訊。 我們也會使用 @AzureAD 發佈任何更新的相關推文。

- 深入了解我們 [可延伸的原則架構](active-directory-b2c-reference-policies.md) 及原則，您可以建立和使用您的應用程式中的類型。
- 目前 [預覽限制、 限制和條件約束](active-directory-b2c-limitations.md)。

## 做法

瞭解如何使用特定 Azure Active Directory B2C 預覽功能：

- 設定 ([Facebook](active-directory-b2c-setup-fb-app.md), ，[Google +](active-directory-b2c-setup-goog-app.md), ，[Amazon](active-directory-b2c-setup-amzn-app.md) 和 [LinkedIn](active-directory-b2c-setup-li-app.md)) 消費者應用程式中使用的帳戶。
- [使用自訂屬性來收集使用者資訊](active-directory-b2c-reference-custom-attr.md)。
- [消費者應用程式中啟用多因素驗證](active-directory-b2c-reference-mfa.md)。
- [設定自助式密碼重設您的取用者](active-directory-b2c-reference-sspr.md)。
- [自訂外觀和操作的註冊、 登入和其他消費者頁面](active-directory-b2c-reference-ui-customization.md) 由 Azure Active Directory B2C。
- [使用 Azure Active Directory Graph API 來以程式設計方式建立、 讀取、 更新和刪除消費者](active-directory-b2c-devquickstarts-graph-dotnet.md) Azure Active Directory B2C 租用戶中。

## 參考

以下連結有助於深入探索服務：

- 請參閱 [Active Directory B2C 定價資訊](https://azure.microsoft.com/pricing/details/active-directory-b2c)
- 取得有關堆疊溢位使用 [azure active 目錄](http://stackoverflow.com/questions/tagged/azure-active-directory) 或 [adal](http://stackoverflow.com/questions/tagged/adal) 標記。
- 請不吝提供您的想法預覽使用 [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) -我們想聽聽他們! 請在您的文章標題中使用 "AzureADB2C:" 字詞，以方便我們尋找。
- Azure Active Directory B2C 使用稱為「應用程式模型 v2.0」的應用程式註冊模型，可支援業界標準通訊協定、OpenID Connect 和 OAuth 2.0。
  - [應用程式模型 v2.0 通訊協定參照](active-directory-b2c-reference-protocols.md)
  - [應用程式模型 v2.0 語彙基元參考](active-directory-b2c-reference-tokens.md)
- [Azure Active Directory B2C 常見問題集](active-directory-b2c-faqs.md)
- [針對 Azure Active Directory B2C 檔案支援要求](active-directory-b2c-support.md)






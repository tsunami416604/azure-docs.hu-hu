<properties
    pageTitle="應用程式模型 v2.0 概觀 | Microsoft Azure"
    description="建置具備 Microsoft 帳戶和 Azure Active Directory 登入之應用程式的簡介。"
    services="active-directory"
    documentationCenter=""
    authors="dstrockis"
    manager="mbaldwin"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/06/2015"
    ms.author="dastrock"/>

# 應用程式模型 v2.0 預覽版：以單一應用程式登入 Microsoft 帳戶和 Azure AD 使用者

> [AZURE.NOTE]
    此資訊適用於 v2.0 應用程式模型公開預覽版本。  如需如何公開上市的 Azure AD 整合服務，請參閱 [Azure Active Directory 開發人員指南](active-directory-developers-guide.md)。

在過去，想要同時支援 Microsoft 帳戶和 Azure Active Directory 的應用程式開發人員必須整合這兩個不同的系統。 透過 v2.0 應用程式模型，您現在可以使用這兩種帳戶類型登入使用者。 一個簡單的整合便可讓您觸達橫跨個人和工作/學校帳戶的數百萬名使用者對象。

您的應用程式也可以使用 [組 Office 365 REST Api](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2) 使用任一種帳戶。  這些 API 目前包含 Outlook 的郵件、連絡人和行事曆 API。  在不久的將來會新增其他服務。
<!-- TODO: customer reference article -->
<!-- Several apps have already begun to bridge the gap between consumer and enterprise accounts, including: [Boomerang](), [TripIt](), & [Uber](). -->

V2.0 應用程式模型為預覽狀態。  在預覽期間，歡迎您提供使用新應用程式模型進行試驗的意見反應和經驗。  我們可能會根據這些意見反應進行重大變更，以改善服務。  您就不會釋放生產應用程式在這段期間使用 2.0 版應用程式模型。
<!-- TODO: Get approval on how it looks  -->

## 開始使用
有兩種方式可以啟動應用程式並搭配 v2.0 應用程式模型執行。  您可以選擇要傳送直接訊息通訊協定使用 [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) 或 [Openid Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow)。  或者，您可以為您執行的工作-選擇您最愛的平台下方，並開始使用我們的程式庫。
<!-- TODO: Finalize this table  -->

[AZURE.INCLUDE [active-directory-v2-quickstart-table](../../includes/active-directory-v2-quickstart-table.md)]

## 新功能
經常查看這裡，以了解 v2.0 應用程式模型公開預覽版的未來變更。  我們也會使用 @AzureAD 發佈任何更新的相關推文。

- 深入了解 [類型的應用程式，您可以建置應用程式模型 v2.0](active-directory-v2-flows.md)。
- 對於開發人員熟悉 Azure Active Directory，您應該查看 [更新我們的通訊協定與 2.0 版應用程式模型中的差異](active-directory-v2-compare.md)。
- 目前 [預覽限制、 限制和條件約束](active-directory-v2-limitations.md)。

## 參考
下列連結有助於深入探索平台：

- 取得有關堆疊溢位使用 [azure active 目錄](http://stackoverflow.com/questions/tagged/azure-active-directory) 或 [adal](http://stackoverflow.com/questions/tagged/adal) 標記。
- 請不吝提供您的想法預覽使用 [User Voice](http://feedback.azure.com/forums/169401-azure-active-directory) -我們想聽聽他們!  請在您的貼文標題中使用 "AppModelv2:" 字詞，以方便我們尋找。
- [應用程式模型 v2.0 通訊協定參考](active-directory-v2-protocols.md)
- [應用程式模型 v2.0 權杖參考](active-directory-v2-tokens.md)
- [Office 365 REST API 參考](https://msdn.microsoft.com/office/office365/howto/authenticate-Office-365-APIs-using-v2)
- [v2 端點的範圍和同意](active-directory-v2-scopes.md)

<!-- TODO: These articles
- [ADAL Library Reference]()
- [v2 Endpoint FAQs](active-directory-v2-faq.md)
-->


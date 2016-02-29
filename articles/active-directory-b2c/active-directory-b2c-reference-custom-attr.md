<properties
    pageTitle="Azure Active Directory B2C 預覽：自訂屬性 | Microsoft Azure"
    description="如何在 Azure Active Directory B2C 中使用自訂屬性收集取用者相關資訊"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="curtand"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/22/2015"
    ms.author="swkrish"/>

#  Azure Active Directory B2C 預覽：使用「自訂屬性」來收集取用者相關資訊

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

您的 Azure Active Directory (AD) B2C 目錄隨附一組內建屬性；例如名字、姓氏、城市、郵遞區號等等。不過，每個取用者導向應用程式針對想從取用者收集的資訊 (屬性)，皆具有獨特需求。 Azure AD B2C 可讓您擴充目錄 (具體而言，您可擴充儲存在每個取用者帳戶上的屬性集合)。 您可以建立自訂的屬性上 [Azure 預覽入口網站](https://portal.azure.com/) 並將它用於您的註冊原則，如下所示。 您也可以讀取和寫入這些屬性所示，使用 Azure AD Graph API [這裡](active-directory-b2c-devquickstarts-graph-dotnet.md)。

> [AZURE.NOTE]
自訂屬性使用 [Azure AD Graph API 目錄結構描述延伸模組](https://msdn.microsoft.com/library/azure/dn720459.aspx) 底下。

## 如何建立自訂屬性

1. [瀏覽至 Azure 預覽入口網站的分頁 B2C 功能](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)...
2. 按一下 [ **使用者屬性**。
3. 按一下 [ **+ 新增** 刀鋒視窗的頂端。
4. 提供 **名稱** 自訂屬性 (例如，「 ShoeSize 」)，並選擇性地 **描述**。 按一下 [ **建立**。 大功告成！

    > [AZURE.NOTE]
    只有 「 字串 」 **資料型別** 正在使用。 我們日後將會新增更多資料類型 (例如 DateTime、Integer 等等)。

自訂屬性現已提供的清單 **使用者屬性** 和您註冊的原則中使用。

## 如何在您的註冊原則中使用自訂屬性

1. 瀏覽至 [B2C 功能] 分頁上 [Azure 預覽入口網站](htts://portal.azure.com/)。 讀取 [這裡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) 如何執行這項操作。
2. 按一下 [ **註冊原則**。
3. 按一下以開啟註冊原則 (例如「B2C_1_SiUp」)。 按一下 [ **編輯** 刀鋒視窗的頂端。
4. 按一下 [ **註冊屬性** ，然後選取 [自訂屬性 (例如，「 ShoeSize 」)。 按一下 [ **確定**。
5. 按一下 [ **應用程式宣告** ，然後選取 [自訂屬性。 按一下 [ **確定**。 
6. 按一下 [ **儲存** 刀鋒視窗的頂端。 大功告成！

您可以使用原則上的「立即執行」功能來驗證取用者體驗。 現在您應會在取用者註冊期間收集之屬性的清單中看見「ShoeSize」，其亦會在傳回至您應用程式的權杖中出現。


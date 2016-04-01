<properties
    pageTitle="Azure Active Directory B2C 預覽：Multi-Factor Authentication | Microsoft Azure"
    description="如何在受 Azure Active Directory B2C 保護的取用者導向應用程式中啟用 Multi-Factor Authentication"
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

# Azure Active Directory B2C 預覽：在取用者導向應用程式中啟用 Multi-Factor Authentication

直接與整合 azure Active Directory (AD) B2C [Azure 多重要素驗證](../multi-factor-authentication/multi-factor-authentication.md) 以方便您將第二個安全性層級加入至消費者應用程式中註冊和登入體驗。 您連一行程式碼都不用寫，即可達成此目標。 目前我們支援使用撥打電話與簡訊做為驗證選項。 如果您已建立註冊和登入的原則 (中所述 [這篇文章]())，您可以開啟多因素驗證，在後續章節中所示。

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
Multi-Factor Authentication 亦可在建立註冊和登入原則時開啟，而非單靠編輯現有原則將其開啟。

應用程式可運用此功能處理諸如下列的各種案例，其中您：

- 存取某一應用程式時需要 Multi-Factor Authentication，但在存取另一應用程式時需要它。 例如，取用者可使用社交或本機帳戶登入汽車保險應用程式，但必須先完成電話號碼驗證，才能存取同個目錄中註冊的家庭保險應用程式。
- 一般而言，存取應用程式時無需 Multi-Factor Authentication，但在存取其中的敏感部分資訊時則需要它。 例如，取用者可使用社交或本機帳戶登入銀行應用程式檢查帳戶餘額，但必須先完成電話號碼驗證，才能嘗試進行轉帳匯款作業。

## 修改註冊原則以啟用 Multi-Factor Authentication

1. [瀏覽至 Azure 預覽入口網站的分頁 B2C 功能](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)...
2. 按一下 [ **註冊原則**。
3. 按一下以開啟註冊原則 (例如「B2C_1_SiUp」)。
4. 按一下 [ **多因素驗證** 開啟 **狀態** 至 **ON**。 按一下 [ **確定**。
5. 按一下 [ **儲存** 刀鋒視窗的頂端。 大功告成！

您可以使用原則上的「立即執行」功能來驗證取用者體驗。 您應會看到以下的正常運作流程：

出現 Multi-Factor Authentication 步驟前，即會在目錄中建立取用者帳戶。 在步驟執行過程中，系統會要求取用者提供電話號碼進行驗證。 若驗證成功，會將電話號碼附加至取用者帳戶以供之後使用。 即使取用者取消或卸除，下次登入時系統仍會要求其再度驗證電話號碼 (已啟用 Multi-Factor Authentication；請參閱下一節)。

## 修改登入原則以啟用 Multi-Factor Authentication

1. 瀏覽至 [B2C 功能] 分頁上 [Azure 預覽入口網站](htts://portal.azure.com/)。 讀取 [這裡](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) 如何執行這項操作。
2. 按一下 [ **登入原則**。
3. 按一下以開啟登入原則 (例如「B2C_1_SiIn」)。 按一下 [ **編輯** 刀鋒視窗的頂端。
4. 按一下 [ **多因素驗證** 開啟 **狀態** 至 **ON**。 按一下 [ **確定**。
5. 按一下 [ **儲存** 刀鋒視窗的頂端。 大功告成！

您可以使用原則上的「立即執行」功能來驗證取用者體驗。 您應會看到以下的正常運作流程：

取用者登入 (使用社交或本機帳戶) 時，若已將通過驗證的電話號碼附加至取用者帳戶，系統會要求其進行驗證。 若未附加電話號碼，系統會要求取用者提供一個電話號碼並加以驗證；驗證成功後，即會將此電話號碼附加至取用者帳戶以供之後使用。



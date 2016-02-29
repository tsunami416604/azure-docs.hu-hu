<properties
    pageTitle="Azure Active Directory B2C 預覽：LinkedIn 組態 | Microsoft Azure"
    description="在受 Azure Active Directory B2C 保護的應用程式中，針對具有 LinkedIn 帳戶的取用者提供註冊和登入"
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
    ms.date="10/08/2015"
    ms.author="swkrish"/>

# Azure Active Directory (AD) B2C 預覽：針對具有 LinkedIn 帳戶的取用者提供註冊和登入

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 LinkedIn 應用程式

若要在 Azure Active Directory (AD) B2C 中使用 LinkedIn 做為身分識別提供者，您必須先建立 LinkedIn 應用程式，然後再對其提供正確參數。 您將需要 LinkedIn 帳戶，才能執行此動作;如果您沒有帳戶，您可以取得在 [https://www.linkedin.com/](https://www.linkedin.com/)。

1. 移至 [LinkedIn 開發人員網站](https://www.developer.linkedin.com/) 並以您的 LinkedIn 帳戶認證登入。
2. 按一下 [ **我的應用程式** 在頂端功能表列中，然後 **建立應用程式**。

    ![LinkedIn - 新建應用程式](./media/active-directory-b2c-setup-li-app/linkedin-new-app.png)

3. 在 **建立新的應用程式** 表單中，填入的相關資訊 (**公司名稱**, ，**名稱**, ，**描述**, ，**應用程式標誌 URL**, ，**應用程式使用**, ，**網站 URL**, ，**公司電子郵件** 和 **公司電話**)。
4. 接受 LinkedIn API 的使用規定，按 [ **提交**。

    ![LinkedIn - 註冊應用程式](./media/active-directory-b2c-setup-li-app/linkedin-register-app.png)

5. 值複製 **用戶端識別碼** 和 **用戶端密碼** (您可以找到這些下 **驗證金鑰** 一節)。 您必須使用這兩個值，將 LinkedIn 設為目錄中的身分識別提供者。

    > [AZURE.NOTE]
    **用戶端密碼** 是重要的安全性認證。

6. 輸入 [https://login.microsoftonline.com/te/ {目錄} / oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) 中 **授權重新導向 Url** 欄位 (在 **OAuth 2.0** 一節)，其中 **{目錄}** 是取代為您的目錄名稱 (例如 contoso.onmicrosoft.com)，並按一下 [ **新增**。 然後按一下 [ **更新**。

    ![LinkedIn - 設定應用程式](./media/active-directory-b2c-setup-li-app/linkedin-setup.png)

## 將 LinkedIn 設為您目錄中的身分識別提供者

1. [瀏覽至 Azure 預覽入口網站的分頁 B2C 功能](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. B2C 功能在分頁上，按一下 [ **身分識別提供者**。
3. 按一下 [ **+ 新增** 刀鋒視窗的頂端。
4. 提供易記 **名稱** 身分識別提供者組態。 例如，輸入「LI」。
5. 按一下 [ **身分識別提供者類型**, ，請選取 **LinkedIn** 按一下 **確定**。
6. 按一下 [ **設定此身分識別提供者** 輸入 **用戶端識別碼** 和 **用戶端密碼** 您稍早建立的 LinkedIn 應用程式。
7. 按一下 [ **確定** 然後 **建立** 儲存 LinkedIn 組態。


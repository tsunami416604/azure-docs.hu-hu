<properties
    pageTitle="Azure Active Directory B2C 預覽：Google+ 組態 | Microsoft Azure"
    description="在受 Azure Active Directory B2C 保護的應用程式中，針對具有 Google+ 帳戶的取用者提供註冊和登入"
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

# Azure Active Directory B2C 預覽：針對具有 Google+ 帳戶的取用者提供註冊和登入

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 Google+ 應用程式

若要在 Azure Active Directory (AD) B2C 中使用 Google+ 做為身分識別提供者，您必須先建立 Google+ 應用程式，然後再對其提供正確參數。 您將需要 Google + 帳戶才能執行此動作;如果您沒有帳戶，您可以取得在 [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)。

1. 移至 [Google 開發人員主控台](https://console.developers.google.com/) 並以您的 Google + 帳戶認證登入。
2. 按一下 [ **建立專案**, ，輸入 **專案名稱**, 、 接受服務條款，然後按一下 [ **建立**。

    ![G+ - 開始使用](./media/active-directory-b2c-setup-goog-app/google-get-started.png)

    ![G+ - 新增專案](./media/active-directory-b2c-setup-goog-app/google-new-project.png)

3. 按一下 [ **APIs & Auth** 然後 **認證** 左側導覽列中。
4. 按一下 [ **OAuth 同意畫面** 頂端的索引標籤。

    ![G+ - 認證](./media/active-directory-b2c-setup-goog-app/google-add-cred.png)

5. 選取或指定的有效 **電子郵件地址**, ，提供 **產品名稱** 按一下 **儲存**。

    ![G+ - OAuth 同意畫面](./media/active-directory-b2c-setup-goog-app/google-consent-screen.png)

6. 按一下 [ **將認證新增** ，然後選擇 [ **OAuth 2.0 用戶端識別碼**。

    ![G+ - OAuth 同意畫面](./media/active-directory-b2c-setup-goog-app/google-add-oauth2-client-id.png)

7. 在 **應用程式類型**, ，請選取 **Web 應用程式**。

    ![G+ - OAuth 同意畫面](./media/active-directory-b2c-setup-goog-app/google-web-app.png)

8. 提供 **名稱** 您的應用程式中，輸入 [https://login.microsoftonline.com](https://login.microsoftonline.com) 中 **授權重新導向 Uri** 欄位和 [https://login.microsoftonline.com/te/ {tenant} / oauth2/authresp](https://login.microsoftonline.com/te/{tenant}/oauth2/authresp) 中 **授權重新導向 Uri** ] 欄位中，其中 **{tenant}** 是要取代為您的租用戶名稱 (例如，contosob2c.onmicrosoft.com)。 按一下 [ **建立**。

    > [AZURE.NOTE]
     **{Tenant}** 值會區分大小寫。

    ![G+ - 建立用戶端識別碼](./media/active-directory-b2c-setup-goog-app/google-create-client-id.png)

9. 值複製 **用戶端識別碼** 和 **用戶端密碼**。 您必須使用這兩個值，將 Google+ 設為租用戶中的身分識別提供者。

    > [AZURE.NOTE]
    **用戶端密碼** 是重要的安全性認證。

    ![G+ - 用戶端密碼](./media/active-directory-b2c-setup-goog-app/google-client-secret.png)

## 將 Google+ 設為您租用戶中的身分識別提供者

1. [瀏覽至 Azure 預覽入口網站的分頁 B2C 功能](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. B2C 功能在分頁上，按一下 [ **身分識別提供者**。
3. 按一下 [ **+ 新增** 刀鋒視窗的頂端。
4. 提供易記 **名稱** 身分識別提供者組態。 例如，輸入 "G+"。
5. 按一下 [ **身分識別提供者類型**, ，請選取 **Google** 按一下 **確定**。
6. 按一下 [ **設定此身分識別提供者** 輸入 **用戶端識別碼** 和 **用戶端密碼** 您稍早建立的 Google + 應用程式。
7. 按一下 [ **確定** 然後 **建立** 來儲存 Google + 組態。



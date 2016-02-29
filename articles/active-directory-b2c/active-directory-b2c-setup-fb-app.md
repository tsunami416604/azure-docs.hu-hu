<properties
    pageTitle="Azure Active Directory B2C 預覽：Facebook 組態 | Microsoft Azure"
    description="在受 Azure Active Directory B2C 保護的應用程式中，針對具有 Facebook 帳戶的取用者提供註冊和登入"
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

# Azure Active Directory B2C 預覽：針對具有 Facebook 帳戶的取用者提供註冊和登入

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 Facebook 應用程式

若要在 Azure Active Directory (AD) B2C 中使用 Facebok 做為身分識別提供者，您必須先建立 Facebook 應用程式，然後再對其提供正確參數。 您將需要的 Facebook 帳戶，若要這樣做。如果您沒有帳戶，您可以取得在 [https://www.facebook.com/](https://www.facebook.com/)。

1. 移至 [Facebook 開發人員網站](https://developers.facebook.com/) 並以您的 Facebook 帳戶認證登入。
2. 如果您尚未這麼做，按一下 [ **應用程式** 然後按一下 [ **身為開發人員註冊**, ，接受政策並遵循註冊步驟。
3. 按一下 [ **應用程式** 然後 **加入新的應用程式**。 然後選擇 [ **網站** 做為平台，然後按一下 **Skip 和建立應用程式識別碼**。

    ![FB - 新建應用程式](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)

    ![FB - 新建應用程式 - 網站](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)

    ![FB - 建立應用程式識別碼](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)

4. 在表單中，提供 **顯示名稱**, ，選擇適當 **類別** 按一下 **建立應用程式識別碼**。 注意：這會要求您接受「Facebook 平台原則」並完成線上「安全性檢查」。

    ![FB - 建立應用程式識別碼](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)

5. 按一下 [ **設定** 在左導覽。 輸入有效的 **連絡人電子郵件**。
6. 按一下 [ **+ 加入平台** ，然後選取 **網站**。

    ![FB - 設定](./media/active-directory-b2c-setup-fb-app/fb-settings.png)

    ![FB - 設定](./media/active-directory-b2c-setup-fb-app/fb-website.png)

7. 輸入 [https://login.microsoftonline.com/](https://login.microsoftonline.com/) 中 **網站 URL** 欄位，然後按一下 **儲存變更**。
8. 複製的值 **應用程式識別碼**。 按一下 [ **顯示** ，並將複製的值 **應用程式密鑰**。 您必須同時使用這兩個值，將 Facebook 設定為您目錄中的身分識別提供者。

    > [AZURE.NOTE]
    **應用程式密鑰** 是重要的安全性認證。

    ![FB - 網站 URL](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)

9. 按一下 [ **進階** 的頂端，索引標籤，然後輸入 [https://login.microsoftonline.com/te/ {目錄} / oauth2/authresp](https://login.microsoftonline.com/te/{directory}/oauth2/authresp) 中 **有效的 OAuth 重新導向 Uri** 欄位 (在 **安全性** 區段)，其中 **{目錄}** 是要取代為您的目錄名稱 (例如，contosob2c.onmicrosoft.com)。 按一下 [ **儲存變更** 頁面的底部。

    ![FB - OAuth 重新導向 URI](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)

10. 若要讓您的 Facebook 應用程式可供 Azure AD B2C 使用，您必須將其設定為對外公開。 您可以按一下 **狀態和檢閱** 在左方瀏覽並開啟在頁面頂端的交換器 **是**。 按一下 [ **確認**。

    ![FB - OAuth 重新導向 URI](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## 將 Facebook 設定為您目錄中的身分識別提供者

1. [瀏覽至 Azure 預覽入口網站的分頁 B2C 功能](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade)。
2. B2C 功能在分頁上，按一下 [ **身分識別提供者**。
3. 按一下 [ **+ 新增** 刀鋒視窗的頂端。
4. 提供易記 **名稱** 身分識別提供者組態。 例如，輸入「FB」。
5. 按一下 [ **身分識別提供者類型**, ，請選取 **Facebook** 按一下 **確定**。
6. 按一下 [ **設定此身分識別提供者** 輸入 **應用程式識別碼** 和 **應用程式密鑰** 您稍早建立的 Facebook 應用程式的 **用戶端識別碼** 和 **用戶端密碼** 欄位分別。
7. 按一下 [ **確定** 然後 **建立** 來儲存 Facebook 組態。


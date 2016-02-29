<properties
    pageTitle="Azure Active Directory B2C 預覽：自助式密碼重設 | Microsoft Azure"
    description="此主題說明如何在 Azure Active Directory B2C 中為取用者設定自助式密碼重設"
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

# Azure Active Directory B2C 預覽：針對取用者設定自助式密碼重設

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

此功能可讓取用者 (已註冊本機帳戶) 重設自己的密碼。 這可大幅減輕支援人員的負擔，特別是在您的應用程式具有數百萬名定期使用的取用者時更是如此。 目前僅支援使用已驗證的電子郵件地址做為復原方法。 我們將在未來新增其他復原方法 (例如已驗證的電話號碼、安全性問題等)。 依預設，您的目錄並不會開啟自助式密碼重設。 使用下列步驟將其開啟：

1. 登入 [Azure 入口網站](https://manage.windowsazure.com/) 訂用帳戶系統管理員身分。 此為與您建立目錄時所用相同的工作或學校帳戶，或是相同的 Microsoft 帳戶。
2. 瀏覽至位於左側導覽列的 Active Directory 延伸模組。
3. 尋找您的目錄下 **目錄** ] 索引標籤上，並加以點選。
4. 按一下 [ **設定** ] 索引標籤。
5. 向下捲動至 **使用者密碼重設原則** 區段及切換 **使用者啟用密碼重設** 選項 **是**。 請注意， **替代電子郵件地址** 核取選項，將它保持為是。

    ![自助式密碼重設](./media/active-directory-b2c-reference-sspr/sspr.png)
 
6. 按一下 [ **儲存** 頁面的底部。 大功告成！

若要進行測試，請針對任何登入原則 (已使用本機帳戶做為身分識別提供者) 使用「立即執行」功能。 在本機帳戶登入頁面上 (您在其中輸入電子郵件地址和密碼或使用者名稱與密碼)，按一下 [ **無法存取您的帳戶?** 驗證客戶體驗。

> [AZURE.NOTE]
自助式密碼重設頁面所使用的可自訂 [公司品牌功能](active-directory-add-company-branding.md)。


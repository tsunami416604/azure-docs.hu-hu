<properties
    pageTitle="Azure Active Directory：建立租用戶支援主題 | Microsoft Azure"
    description="建立 Azure Active Directory 租用戶或 Azure Active Directory B2C 租用戶 - 問題與解決方法"
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
    ms.date="09/24/2015"
    ms.author="swkrish"/>

# 建立 Azure AD 租用戶或 Azure AD B2C 租用戶 - 問題與解決方法

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 建立 Azure AD 租用戶

若您第一次無法建立 Azure AD 租用戶，請再試一次。 若問題持續發生，請連絡「支援服務」。

## 建立 Azure AD B2C 租用戶 (預覽)

已知問題時可能會遇到 [的 Azure AD B2C 租用戶建立](active-directory-b2c-get-started.md)。

- 若您的租用戶清單未顯示 Azure AD B2C 租用戶，請再試一次。
- 若您的租用戶清單未顯示 Azure AD B2C 租用戶，但出現錯誤訊息指出「無法完成 B2C 租用戶 'contosob2c' 的建立。 請瀏覽此 [連結](http://go.microsoft.com/fwlink/?LinkID=624192&clcid=0x409) 詳細指引。 」，然後執行下列其中一項 ︰
    - 瀏覽至 Azure 預覽入口網站使用此連結 B2C 功能分頁 [https://portal.azure.com/ {tenant}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com](https://portal.azure.com/{directory}.onmicrosoft.com/?Microsoft_AAD_B2CAdmin=true#blade/Microsoft_AAD_B2CAdmin/TenantManagementBlade/id/{tenant}.onmicrosoft.com), ，其中 **{tenant}** 是要取代的全域系統管理員身分登入租用戶建立時 (例如，contosob2c)，所使用的名稱。 按一下 [ **啟用 B2C 功能** 分頁，然後按一下頂端 **確定**。 您的 Azure AD B2C 租用戶應已隨時可用！
    - 刪除剛建立的租用戶並再試一次。
- 若上述解決方法無效，請連絡「支援服務」。 讀取 [這篇文章](active-directory-b2c-support.md) 如何針對 Azure AD B2C 檔案支援要求。



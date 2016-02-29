<properties
   pageTitle="疑難排解：'Active Directory' 項目遺失或無法使用 | Microsoft Azure "
   description="若 Active Directory 功能表項目未出現在 Azure 管理入口網站中，該怎麼做。"
   services="active-directory"
   documentationCenter="na"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/04/2015"
   ms.author="mbaldwin"/>

# 疑難排解：'Active Directory' 項目遺失或無法使用

許多使用 Azure Active Directory 功能和服務的指示以開始 「 移至 Azure 管理入口網站，按一下 [ **Active Directory**。 」 但該怎麼辦如果 Active Directory 延伸模組或功能表項目不會出現，或標示 **無法使用**嗎? 本主題旨在提供協助。 它會描述的條件 **Active Directory** 不會出現或無法使用，並說明要如何繼續進行。

## Active Directory 遺失

一般而言， **Active Directory** 項目出現在左的導覽功能表。 Azure Active Directory 程序中的指示假設此項目是在您的檢視中。

![螢幕擷取畫面：Azure 中的 Active Directory](./media/active-directory-troubleshooting/typical-view.png)

當下列任一條件成立時，Active Directory 項目就會出現在左側導覽功能表中。 否則，該項目便不會出現。

* 目前的使用者是使用 Microsoft 帳戶 (之前稱為 Windows Live ID) 登入。

    或

* Azure 租用戶都擁有一個目錄，且目前的帳戶為目錄系統管理員。

    或

* Azure 租用戶至少具有一個 Azure AD 存取控制 (ACS) 命名空間。 如需詳細資訊，請參閱 [存取控制命名空間](https://msdn.microsoft.com/library/azure/gg185908.aspx)。

    或

* Azure 租用戶至少具有一個 Azure Multi-Factor Authentication 提供者。 如需詳細資訊，請參閱 [管理的 Azure 多因素驗證提供者](multi-factor-authentication-get-started-cloud.md/creating-an-azure-multi-factor-auth-provider)。

若要建立存取控制命名空間或多重要素驗證提供者，請按一下 [ **+ 新增** > **應用程式服務** > **Active Directory**。

若要取得目錄的系統管理權限，必須要求系統管理員將系統管理員角色指派給您的帳戶。 如需詳細資訊，請參閱 [指派系統管理員角色](active-directory-assign-admin-roles.md)。

## Active Directory 無法使用

當您按一下 **+ 新增** > **應用程式服務**, 、 **Active Directory** 項目隨即出現。 具體而言，當任何 Active Directory 功能 (例如，目錄、存取控制或 Multi-Factor Auth 提供者) 可供目前使用者使用時，Active Directory 項目即會出現。

不過，在網頁載入時，項目會呈現灰色而標示為 **無法使用**。 這是暫時性狀態。 如果您稍待片刻，該項目將變成可用狀態。 如果延遲太久，重新整理網頁通常就能解決問題。

![螢幕擷取畫面：Active Directory 無法使用](./media/active-directory-troubleshooting/not-available.png)


<properties
    pageTitle="Azure Active Directory 中的專用群組 | Microsoft Azure"
    description="在 Azure Active Directory 中如何建立專用群組與其運作方式的概觀。"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""
    />

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/17/2015"
    ms.author="curtand"/>

# Azure Active Directory 中的專用群組

在 Azure Active Directory 中，會自動建立專用群組，也會自動建立專用群組的群組成員資格。 您無法透過 Azure 入口網站、Windows PowerShell Cmdlet，或以程式設計方式，在專用群組中新增或移除成員。 若要啟用專用的群組，在 Azure 入口網站中，在 [設定] 索引標籤，設定 **啟用專用群組切換為 [是]**。

一旦啟用專用群組] 參數設為 **是**, ，您可以進一步讓目錄設定會自動建立所有使用者專用的群組 **啟用 「 所有使用者 」 群組** 切換至 **是**。 您再也可以編輯此專用群組的名稱輸入 **顯示名稱 「 所有使用者 」 群組** 欄位。

如果您想要將相同的權限指派給您目錄中的所有使用者，[All Users] 專用群組可能會很實用。 例如，您可以將 [All Users] 專用群組的存取權指派給此應用程式，以便為您目錄中的所有使用者授與 SaaS 應用程式的存取權。

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組來管理資源的存取權](active-directory-manage-groups.md)
* [什麼是 Azure Active Directory？](active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)


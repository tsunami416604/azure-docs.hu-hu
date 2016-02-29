<properties
   pageTitle="新增和管理多個 Azure Active Directory 目錄 | Microsoft Azure"
   description="新增和管理 Azure Active Directory 目錄的指示和最佳作法，說明目錄為完全獨立的資源"
   services="active-directory"
   documentationCenter=""
   authors="curtand"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/01/2015"
   ms.author="curtand"/>

# 新增和管理多個 Azure Active Directory 目錄

在 Azure Active Directory (Azure AD) 目錄中，每個目錄都是完全獨立的資源：對等、全功能，且在邏輯上獨立於您管理的其他目錄。 目錄之間沒有任何父子關聯性。 目錄之間的這項獨立性包括資源獨立性、系統管理獨立性和同步處理獨立性。

##資源獨立性

如果您在某個目錄中建立或刪除資源，則不會影響另一個目錄中的任何資源 (但外部使用者有部分例外狀況)，如下所述。 如果您搭配使用自訂網域 'contoso.com' 與某個目錄，則它不能與任何其他目錄搭配使用。

##系統管理獨立性

如果 'Contoso' 目錄的非系統管理使用者會再建立 'Test' 測試目錄:
- 根據預設，會建立一個目錄的使用者新增新目錄，外部使用者的身分，並指定該目錄中的全域管理員角色。
- 'Contoso' 目錄的系統管理員的 'Test' 目錄沒有直接管理權限，除非 'Test' 的系統管理員特別授與這些權限。 'Contoso' 的系統管理員可以控制 'Test' 目錄的存取控制的使用者帳戶建立 'Test'。
- 如果您變更 (新增或移除) 一個目錄中使用者的系統管理員角色，變更不會影響任何使用者可能有另一個目錄中的系統管理員角色。

##同步處理獨立性

您可以設定每個 Azure AD 目錄分開讓資料從單一執行個體中的其中一個同步處理:
  - 目錄同步作業 (DirSync) 工具，與單一 AD 樹系同步處理資料。
  - Azure Active Directory 連接器 Forefront Identity Manager，與一或多個內部部署樹系和/或非 Azure AD 資料來源同步處理資料。

##新增 Azure AD 目錄

若要在 Azure 傳統入口網站中加入 Azure AD 目錄，選取左側的 Azure Active Directory 延伸模組，然後點選 **新增**。

> [AZURE.NOTE]   不像其他 Azure 資源，您的目錄不 Azure 訂用帳戶的子資源。 如果您取消或允許 Azure 訂用帳戶到期，則還是可以使用 Azure PowerShell、Azure Graph API 或其他介面 (例如 Office 365 系統管理中心) 存取目錄資料。 您也可以關聯另一個訂用帳戶與目錄。

如需 Azure AD 授權問題和最佳作法的一般概觀，請參閱 [什麼 Azure Active Directory 授權?](active-directory-licensing-what-is.md)。


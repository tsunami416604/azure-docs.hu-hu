<properties
    pageTitle="建立簡單的規則，設定群組的動態成員資格 | Microsoft Azure"
    description="說明建立簡單的規則，以設定群組的動態成員資格。"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/17/2015"
    ms.author="curtand"/>


# 建立簡單的規則，設定群組的動態成員資格

若要啟用特定群組的動態成員資格，請執行下列步驟：

1. 在 Azure 網站中，在 **群組** 索引標籤上，選取您想要編輯的群組，然後在此群組 **設定** 索引標籤上，設定 **啟用動態成員資格** 切換至 **是**。

2. 您現在可以為將控制此群組的動態成員資格如何運作的群組，設定一個簡單的單一規則。 請確定 **將使用者加入其中** 選項已選取，然後再從清單中 （例如，department、 jobTitle 等），選取一個使用者屬性

3. 接著，選取一個條件 (不等於、等於、開頭不是、開頭為、不包含、包含、不符合、符合)，最後指定所選使用者屬性的值。 例如，如果群組已指派給 SaaS 應用程式，並設定一個規則，藉此啟用這個群組的動態成員資格 **將使用者加入其中** 設為 jobTitle，Equals(-eq) 銷售代表，其職稱設定為 Sales Rep Azure AD 目錄中的所有使用者將都擁有存取此 SaaS 應用程式。

這些文章提供有關 Azure Active Directory 的其他資訊。

* [使用 Azure Active Directory 群組來管理資源的存取權](active-directory-manage-groups.md)
* [什麼是 Azure Active Directory？](active-directory-whatis.md)
* [整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)



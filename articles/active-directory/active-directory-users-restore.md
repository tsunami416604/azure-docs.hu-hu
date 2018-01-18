---
title: "Állítsa vissza vagy egy nemrég törölt felhasználó végleges eltávolításához az Azure Active Directoryban |} Microsoft Docs"
description: "Állítsa vissza a törölt felhasználói, visszaállítható felhasználók megtekintése, és véglegesen törli a felhasználó az Azure Active Directoryban"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 01/12/2018
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: it-pro
ms.openlocfilehash: d8a1850f8635097364268abdf77394ba592f761b
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="restore-a-deleted-user-in-azure-active-directory"></a>Az Azure Active Directory törölt felhasználó visszaállítása

Ez a cikk állítsa vissza vagy véglegesen törli a korábban törölt felhasználói utasításokat tartalmaz. Ha töröl egy felhasználót az Azure Active Directory (Azure AD), a törölt felhasználói megőrzi a törlés 30 napig. Ebben az időszakban a felhasználó és a tulajdonságok állíthatók vissza. 


## <a name="how-to-restore-a-recently-deleted-user"></a>Közelmúltban törölt felhasználó visszaállítása
Amikor a felhasználó nemrég törölték, az összes címtár-információ megőrződik. Ha a felhasználó helyreáll, ezt az információt, valamint helyreáll.

1. Az a [az Azure AD felügyeleti központban](https://aad.portal.azure.com), jelölje be **felhasználók és csoportok** &gt; **minden felhasználó**. 
2. A **megjelenítése**, a lap megjelenítése szűrése **nemrég törölt felhasználók**. 
3. Válasszon legalább egy nemrég törölt felhasználók.
4. Válassza ki **visszaállítási felhasználói**.

## <a name="how-to-permanently-delete-a-recently-deleted-user"></a>Végleg törölni kívánja a közelmúltban törölt felhasználói útmutató

1. Az a [az Azure AD felügyeleti központban](https://aad.portal.azure.com), jelölje be **felhasználók és csoportok** &gt; **minden felhasználó**. 
2. A **megjelenítése**, a lap megjelenítése szűrése **nemrég törölt felhasználók**. 
3. Válasszon legalább egy nemrég törölt felhasználók.
4. Válassza ki **véglegesen törli**.

## <a name="required-permissions"></a>Szükséges engedélyek
Az alábbi engedélyek megfelelőek a felhasználók visszaállítása.

Szerepkör  | Engedélyek 
--------- | ---------
Céges rendszergazda<p>1. szintű partnertámogatás<p>2. szintű partnertámogatás<p>Felhasználóifiók-adminisztrátor | Visszaállíthatja a törölt felhasználók 
Céges rendszergazda<p>1. szintű partnertámogatás<p>2. szintű partnertámogatás<p>Felhasználóifiók-adminisztrátor | Véglegesen törölheti a felhasználók

## <a name="next-steps"></a>További lépések
Ezek a cikkek további információkkal az Azure Active Directory felhasználó-kezelés.

* [Gyors üzembe helyezés: Hozzáadása, és az Azure Active Directory felhasználók törlése](add-users-azure-active-directory.md)
* [Felhasználói profilok kezelése](active-directory-users-profile-azure-portal.md)
* [Egy másik címtárból adja hozzá a vendégfelhasználók számára](active-directory-b2b-what-is-azure-ad-b2b.md) 
* [Felhasználó hozzárendelése egy szerepkörhöz az Azure AD-ben](active-directory-users-assign-role-azure-portal.md)

---
title: Csoporttulajdonságok kezelése az Azure AD-ben | Microsoft Docs
description: A cikkből megtudhatja, hogyan módosíthatja a csoportok tulajdonságait és egyéb konfigurációs beállításait az Azure Active Directoryban.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 08/01/2017
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 50d9443197df35ac032c87317966551da1612c41
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860364"
---
# <a name="manage-the-settings-for-a-group-in-azure-active-directory"></a>Csoportok beállításainak kezelése az Azure Active Directoryban
Ez a cikk azt ismerteti, hogyan módosíthatja a csoportok beállításait az Azure Active Directoryban (Azure AD).

## <a name="how-do-i-find-and-change-the-settings"></a>Hogyan kereshetem meg és módosíthatom a beállításokat?
1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza a **Minden szolgáltatás** elemet, írja be a **Felhasználók és csoportok** kifejezést a szövegmezőbe, majd nyomja le az **Enter** billentyűt.

   ![A Felhasználók és csoportok panel megnyitása](./media/active-directory-groups-settings-azure-portal/search-user-management.png)
3. A **Felhasználók és csoportok** panelen válassza a **Minden csoport** elemet.

   ![A Minden csoport panel megnyitása](./media/active-directory-groups-settings-azure-portal/view-groups-blade.png)
4. A **Felhasználók és csoportok – Minden csoport** panelen válasszon ki egy csoportot.
5. A **Csoport – *csoportnév*** panelen válassza a **Tulajdonságok** elemet.

   ![A Tulajdonságok panel megnyitása](./media/active-directory-groups-settings-azure-portal/select-group-properties.png)
6. Ha befejezte a csoport tulajdonságainak módosítását, kattintson a **Mentés** parancsra.    

   ![Tulajdonságok módosításainak mentése](./media/active-directory-groups-settings-azure-portal/save-group-properties.png)

## <a name="next-steps"></a>További lépések
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Meglévő csoportok megtekintése](active-directory-groups-view-azure-portal.md)
* [Új csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)
* [Csoporttagok kezelése](active-directory-groups-members-azure-portal.md)
* [Csoporttagságok kezelése](active-directory-groups-membership-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../users-groups-roles/groups-dynamic-membership.md)

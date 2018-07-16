---
title: A saját csoportot tartalmazó csoportok kezelése az Azure AD-ben | Microsoft Docs
description: A csoportok tartalmazhatnak más csoportokat az Azure Active Directoryban. Az alábbiakat követve kezelheti ezen csoportok tagságát.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 8a71677ae3ceb5617f0a817a8eff438d5e3f2774
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860367"
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Annak kezelése, hogy egy csoport mely csoportokhoz tartozik az Azure Active Directory-bérlőben
A csoportok tartalmazhatnak más csoportokat az Azure Active Directoryban. Az alábbiakat követve kezelheti ezen csoportok tagságát.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>Hogyan találhatom meg azokat a csoportokat, amelyeknek a saját csoportom a tagja?
1. Jelentkezzen be az [Azure AD felügyeleti központba](https://aad.portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza a **Felhasználók és csoportok** elemet.

   ![A Felhasználók és csoportok megnyitásának képe](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Válassza a **Minden csoport** lehetőséget.

   ![Kép: Csoportok kiválasztása](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Válasszon ki egy csoportot.
2. Válassza a **Csoporttagságok** elemet.

   ![Kép: A csoporttagságok megnyitása](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. A saját csoport egy másik csoport tagjaként való hozzáadásához válassza a **Csoport – Csoporttagságok** panel **Hozzáadás** parancsát.
2. Válasszon ki egy csoportot a **Csoport kiválasztása** panelen, majd kattintson a panel alján található **Kiválasztás** gombra. A kiválasztott csoport egyszerre csak egy csoporthoz adható hozzá. A **Felhasználó** mezővel aszerint szűrheti a megjelenített elemeket, hogy a megadott szöveg megtalálható-e egy felhasználó- vagy eszköznévben. Helyettesítő karakterek nem használhatók a mezőben.

   ![Csoporttagság hozzáadása](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Ha el szeretné távolítani a saját csoportot egy másik csoport tagjai közül, válasszon ki egy csoportot a **Csoport – Csoporttagságok** panelen.
9. Kattintson az **Eltávolítás** parancsra, majd erősítse meg a műveletet, amikor a rendszer megkéri rá.

   ![tagság eltávolítása parancs](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Ha befejezte a csoport csoporttagságának módosítását, kattintson a **Mentés** parancsra.

## <a name="additional-information"></a>További információ
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

* [Meglévő csoportok megtekintése](active-directory-groups-view-azure-portal.md)
* [Új csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)
* [Csoportbeállítások kezelése](active-directory-groups-settings-azure-portal.md)
* [Csoporttagok kezelése](active-directory-groups-members-azure-portal.md)
* [A csoportban lévő felhasználók dinamikus szabályainak kezelése](../users-groups-roles/groups-dynamic-membership.md)

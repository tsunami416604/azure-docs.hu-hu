---
title: Felhasználók és csoportok licenccsomagjainak módosítása - Azure AD | Microsoft dokumentumok
description: A csoporton belüli felhasználók áttelepítése különböző szolgáltatási csomagokba az Azure Active Directory ban lévő csoportlicencelés használatával
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2f04e1728f94c89bddcc31c287cc017a79020f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025909"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Felhasználó vagy csoport licenc-hozzárendelésének módosítása az Azure Active Directoryban

Ez a cikk ismerteti, hogyan helyezheti át a felhasználók és csoportok között szolgáltatási licenccsomagok az Azure Active Directoryban (Azure AD). A cél az Azure AD megközelítése annak biztosítása, hogy a licenc módosítása során ne vessüljen el a szolgáltatás vagy az adatok. A felhasználóknak zökkenőmentesen kell váltaniuk a szolgáltatások között. Az ebben a cikkben ismertetett licenccsomag-hozzárendelési lépések az Office 365 E1 egyik felhasználójának vagy csoportjának office 365 E3-ra történő módosítását ismertetik, de a lépések az összes licenccsomagra vonatkoznak. Amikor frissíti egy felhasználó vagy csoport licenc-hozzárendeléseit, a licenc-hozzárendelések és az új hozzárendelések egyidejűleg történnek, így a felhasználók nem veszítik el a hozzáférést a szolgáltatásaikhoz a licencmódosítások során, vagy nem látják a csomagok közötti licencütközéseket.

## <a name="before-you-begin"></a>Előkészületek

A licenc-hozzárendelések frissítése előtt fontos ellenőrizni, hogy bizonyos feltételezések minden frissítésre vonatkozóan igazak-e. Ha a feltételezések nem igazak a csoport összes felhasználójára, az áttelepítés néhány esetben sikertelen lehet. Ennek eredményeképpen előfordulhat, hogy egyes felhasználók elveszítik a szolgáltatásokhoz vagy adatokhoz való hozzáférést. Győződjön meg arról, hogy:

- A felhasználók rendelkeznek a jelenlegi licenccsomaggal (ebben az esetben az Office 365 E1-el), amely egy csoporthoz van rendelve, és amelyet a felhasználó örököl, és nem közvetlenül.

- Elegendő licence van a hozzárendelt licenccsomaghoz. Ha nem rendelkezik elegendő licenccel, előfordulhat, hogy egyes felhasználók nem kapják meg az új licenccsomagot. Ellenőrizheti az elérhető licencek számát.

- A felhasználók nem rendelkeznek más hozzárendelt szolgáltatási licencekkel, amelyek ütközhetnek a kívánt licenccel, vagy megakadályozhatják az aktuális licenc eltávolítását. Például egy olyan szolgáltatás, például a Workplace Analytics vagy a Project Online licence, amely más szolgáltatásoktól függ.

- Ha a helyszíni csoportokat kezeli, és szinkronizálja őket az Azure AD Connecten keresztül, majd a helyszíni rendszer használatával felhasználókat vesz fel vagy távolít el. Eltarthat egy ideig, amíg a módosítások szinkronizálása az Azure AD-vel a csoport licencelése által felvette.

- Ha az Azure AD dinamikus csoporttagságait használja, az attribútumok módosításával felhasználókat vesz fel vagy távolít el, de a licenc-hozzárendelések frissítési folyamata ugyanaz marad.

## <a name="change-user-license-assignments"></a>Felhasználói licenc-hozzárendelések módosítása

A **Licenc-hozzárendelések frissítése** lapon, ha azt látja, hogy egyes jelölőnégyzetek nem érhetők el, az olyan szolgáltatásokat jelez, amelyek nem módosíthatók, mert csoportlicencből származnak.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) egy licencrendszergazdai fiók használatával az Azure AD-szervezetben.
1. Válassza az Azure Active > **Directory-felhasználók**lehetőséget, majd nyissa meg a **profillapot** egy felhasználó számára. **Azure Active Directory**
1. Válassza **a Licencek**lehetőséget.
1. Válassza **a Hozzárendelések** lehetőséget a felhasználó vagy csoport licenchozzárendelésének szerkesztéséhez. A **Hozzárendelések** lapon feloldhatja a licenc-hozzárendelési ütközéseket.
1. Jelölje be az Office 366 E3 jelölőnégyzetét, és győződjön meg arról, hogy legalább a felhasználóhoz rendelt összes E1 szolgáltatás ki van jelölve.
1. Törölje a jelet az Office 365 E1 jelölőnégyzetéből.

    ![licenc-hozzárendelések lap egy olyan felhasználó számára, aki törli az Office 365 E1-et és az Office 365 E3-at kiválasztva](media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Kattintson a **Mentés** gombra.

Az Azure AD alkalmazza az új licenceket, és egyszerre eltávolítja a régi licenceket a szolgáltatás folytonosságának biztosítása érdekében.

## <a name="change-group-license-assignments"></a>Csoportlicenc-hozzárendelések módosítása

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) egy licencrendszergazdai fiók használatával az Azure AD-szervezetben.
1. Válassza az **Azure Active Directory-csoportok** > **Groups**lehetőséget, majd nyissa meg egy csoport **áttekintő** lapját.
1. Válassza **a Licencek**lehetőséget.
1. Válassza a **Hozzárendelések** parancsot a felhasználó vagy csoport licenc-hozzárendelésének szerkesztéséhez.
1. Jelölje be az Office 366 E3 jelölőnégyzetét. A szolgáltatás folytonosságának fenntartása érdekében győződjön meg arról, hogy a felhasználóhoz már hozzárendelt összes E1 szolgáltatást ki kell választania.
1. Törölje a jelet az Office 365 E1 jelölőnégyzetéből.

    ![A Hozzárendelések parancs kijelölése egy felhasználói vagy csoportlicencek lapon](media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Kattintson a **Mentés** gombra.

A szolgáltatás folytonosságának biztosítása érdekében az Azure AD alkalmazza az új licenceket, és egyszerre eltávolítja a régi licenceket a csoport összes felhasználója számára.

## <a name="next-steps"></a>További lépések

A csoportokon keresztüli licenckezelés egyéb forgatókönyveiről az alábbi cikkekben olvashat:

- [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](../users-groups-roles/licensing-groups-assign.md)
- [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](../users-groups-roles/licensing-groups-resolve-problems.md)
- [Az egyéni licencelt felhasználók áttelepítése az Azure Active Directory licencelésének csoportosítására](../users-groups-roles/licensing-groups-migrate-users.md)
- [Az Azure Active Directory csoport további forgatókönyvek licencelése](../users-groups-roles/licensing-group-advanced.md)
- [Példák a csoportlicencelésre az Azure Active Directoryban](../users-groups-roles/licensing-ps-examples.md)

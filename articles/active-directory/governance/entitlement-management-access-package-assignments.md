---
title: Hozzáférési csomag hozzárendeléseimegtekintése, hozzáadása és eltávolítása az Azure AD jogosultságkezelésében – Azure Active Directory
description: Megtudhatja, hogyan tekintheti meg, veheti fel és távolíthatja el egy hozzáférési csomag hozzárendeléseit az Azure Active Directory jogosultságkezelésében.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a2107974cd63c0d02aaeb555430453c39990bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262021"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag hozzárendeléseinek megtekintése, hozzáadása és eltávolítása az Azure AD-jogosultságkezelésben

Az Azure AD jogosultságkezelés, láthatja, hogy ki van hozzárendelve a csomagok eléréséhez, a szabályzat és az állapot. Ha egy hozzáférési csomag rendelkezik megfelelő házirenddel, közvetlenül is hozzárendelheti a felhasználót egy hozzáférési csomaghoz. Ez a cikk a hozzáférési csomagok hozzárendeléseinek megtekintését, hozzáadását és eltávolítását ismerteti.

## <a name="view-who-has-an-assignment"></a>A hozzárendelést végzők megtekintése

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson **a Hozzárendelések** elemre az aktív hozzárendelések listájának megtekintéséhez.

    ![Hozzáférési csomag hozzárendeléseinek listája](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. A további részletek megtekintéséhez kattintson egy adott hozzárendelésre.

1. Az olyan hozzárendelések listájának megtekintéséhez, amelyek nem rendelkeztek megfelelően kiépített erőforrásszerepkörökkal, kattintson a szűrő állapotára, és válassza a **Szállítás**lehetőséget.

    A kézbesítési hibáktovábbi részleteit a felhasználó megfelelő kérésének megkeresésével láthatja a **Kérések** lapon.

1. A lejárt hozzárendelések megtekintéséhez kattintson a szűrő állapotára, és válassza a **Lejárt**lehetőséget.

1. A szűrt lista CSV-fájljának letöltéséhez kattintson a **Letöltés gombra.**

### <a name="viewing-assignments-programmatically"></a>Hozzárendelések programozott megtekintése

A hozzárendeléseket hozzáférési csomagban is lekérheti a Microsoft Graph használatával.  A delegált `EntitlementManagement.ReadWrite.All` engedéllyel rendelkező alkalmazással rendelkező alkalmazással rendelkező felhasználó meghívhatja az API-t az [accessPackageAssignments listához.](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta)

## <a name="directly-assign-a-user"></a>Felhasználó közvetlen hozzárendelése

Bizonyos esetekben előfordulhat, hogy közvetlenül hozzá szeretne rendelni egy hozzáférési csomaghoz bizonyos felhasználókat, hogy a felhasználóknak ne kelljen végigmenniük a hozzáférési csomag kérésének folyamatán. A felhasználók közvetlen hozzárendeléséhez a hozzáférési csomagnak rendelkeznie kell egy olyan házirenddel, amely lehetővé teszi a rendszergazda közvetlen hozzárendelését.

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson a **Hozzárendelések parancsra.**

1. Kattintson **az Új hozzárendelés gombra** a Felhasználó hozzáadása a hozzáféréshez csomag megnyitásához.

    ![Hozzárendelések – Felhasználó hozzáadása a hozzáférési csomaghoz](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Kattintson a **Felhasználók hozzáadása** gombra, ha ki szeretné jelölni azokat a felhasználókat, akikhez hozzá szeretné rendelni ezt a hozzáférési csomagot.

1. A **Házirend kiválasztása** listában válassza ki azt a házirendet, amely szabályozza és nyomon követi a felhasználók jövőbeli kéréseit és életciklusát. Ha azt szeretné, hogy a kijelölt felhasználók eltérő házirend-beállításokkal rendelkezhessenek, az **Új házirend létrehozása** gombra kattintva új házirendet vehet fel.

1. Állítsa be azt a dátumot és időpontot, amikor a kijelölt felhasználók hozzárendelését el szeretné kezdeni és le állítani. Ha nincs megadva a befejezési dátum, a házirend életciklus-beállításai lesznek használva.

1. Szükség esetén adja meg a nyilvántartási feladat ának indoklását.

1. A **Hozzáadás gombra** kattintva közvetlenül hozzárendelheti a kijelölt felhasználókat a hozzáférési csomaghoz.

    Néhány pillanat múlva kattintson a **Frissítés** gombra a Hozzárendelések lista felhasználóinak megtekintéséhez.

### <a name="directly-assigning-users-programmatically"></a>Felhasználók közvetlen hozzárendelése programozott módon

A Microsoft Graph segítségével közvetlenül is hozzárendelhet egy felhasználót egy hozzáférési csomaghoz.  A delegált `EntitlementManagement.ReadWrite.All` engedéllyel rendelkező alkalmazással rendelkező alkalmazással rendelkező felhasználó meghívhatja az API-t [accessPackageAssignmentRequest létrehozásához.](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta)

## <a name="remove-an-assignment"></a>Hozzárendelés eltávolítása

**Előfeltételi szerepkör:** Globális rendszergazda, Felhasználói rendszergazda, Katalógustulajdonosa vagy Access-csomagkezelő

1. Az Azure Portalon kattintson az **Azure Active Directory,** majd **az identitás-cégirányítási**.

1. A bal oldali menüben kattintson az **Access-csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson a **Hozzárendelések parancsra.**
 
1. Jelölje be azt a felhasználót, akinek a hozzárendelését el szeretné távolítani a hozzáférési csomagból. 

1. Kattintson az **Eltávolítás** gombra a bal oldali ablaktábla tetején. 
 
    ![Hozzárendelések – Felhasználó eltávolítása a hozzáférési csomagból](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Megjelenik egy értesítés, amely tájékoztatja Önt arról, hogy a hozzárendelést eltávolították. 

## <a name="next-steps"></a>További lépések

- [Hozzáférési csomag kérésének és beállításainak módosítása](entitlement-management-access-package-request-policy.md)
- [Jelentések és naplók megtekintése](entitlement-management-reports.md)

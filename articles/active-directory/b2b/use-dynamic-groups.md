---
title: Dinamikus csoportok és B2B együttműködés - Azure Active Directory | Microsoft dokumentumok
description: Az Azure Active Directory B2B-együttműködéssel az Azure AD dinamikus csoportjainak használata
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41e8b81bc3594c6a378757636f70058510a38cc7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78226877"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dinamikus csoportok és az Azure Active Directory B2B együttműködése

## <a name="what-are-dynamic-groups"></a>Mik azok a dinamikus csoportok?
Az Azure Active Directory (Azure AD) biztonsági csoporttagságának dinamikus konfigurálása [elérhető az Azure Portalon.](https://portal.azure.com) A rendszergazdák szabályokat állíthatnak be az Azure AD-ben létrehozott csoportok feltöltésére a felhasználói attribútumok (például userType, részleg vagy ország/régió) alapján. A tagok automatikusan hozzáadhatók a biztonsági csoporthoz, vagy eltávolíthatók onnan az attribútumaik alapján. Ezek a csoportok hozzáférést biztosíthatnak az alkalmazásokhoz vagy a felhőalapú erőforrásokhoz (SharePoint-webhelyek, dokumentumok), és licenceket rendelhetnek a tagokhoz. További információ a dedikált csoportok dedikált csoportokban való megjelenítéséről [az Azure Active Directoryban.](../active-directory-accessmanagement-dedicated-groups.md)

A dinamikus csoportok létrehozásához és használatához szükség van a megfelelő [Azure AD Premium P1 vagy P2 licencelésre.](https://azure.microsoft.com/pricing/details/active-directory/) További információ: [Attribútumalapú szabályok létrehozása dinamikus csoporttagsághoz az Azure Active Directoryban](../users-groups-roles/groups-dynamic-membership.md)című cikkben olvashat bővebben.

## <a name="creating-an-all-users-dynamic-group"></a>"Minden felhasználó" dinamikus csoport létrehozása
Létrehozhat egy csoportot, amely egy bérlő összes felhasználója tagsági szabály használatával. Amikor a felhasználók at ad nak hozzá, vagy eltávolítja a bérlő a jövőben, a csoport tagsága automatikusan módosul.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a globális rendszergazdai vagy felhasználói rendszergazdai szerepkört a bérlőben van hozzárendelve.
1. Válassza az **Azure Active Directory**lehetőséget.
2. A **Kezelés**csoportban válassza a **Csoportok**lehetőséget, majd válassza az **Új csoport**lehetőséget.
1. Az **Új csoport** lap **Csoport típus csoportjában**válassza a **Biztonság**lehetőséget. Adja meg az új csoport **csoportnevét** és **csoportleírását.** 
2. A **Tagság típusa csoportban**válassza a **Dinamikus felhasználó**lehetőséget, majd kattintson a Dinamikus lekérdezés **hozzáadása gombra.** 
4. A **Szabály szintaxisa** mező felett válassza a **Szerkesztés**lehetőséget. A **Szabály szintaxisának szerkesztése** lapon írja be a következő kifejezést a szövegmezőbe:

   ```
   user.objectId -ne null
   ```
1. Válassza **az OK gombot.** A szabály a Szabály szintaxisa mezőben jelenik meg:

   ![Szabály szintaxisa az összes felhasználó dinamikus csoportjához](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Kattintson a **Mentés** gombra. Az új dinamikus csoport mostantól a B2B vendégfelhasználókat és a tagfelhasználókat is tartalmazza.


1. A csoport létrehozásához válassza a **Létrehozás** gombot az **Új csoport** lapon.

## <a name="creating-a-group-of-members-only"></a>Csak tagok csoportjának létrehozása

Ha azt szeretné, hogy a csoport kizárja a vendégfelhasználókat, és csak a bérlő tagjait tartalmazza, hozzon létre egy dinamikus csoportot a fent leírtak szerint, de a **Szabály szintaxis** a következő kifejezést írja be:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

Az alábbi képen egy dinamikus csoport szabályszintaxisa látható, amelyet úgy módosítottak, hogy csak tagokat vegyen fel, és kizárja a vendégeket.

![Azokat a szabályt jeleníti meg, amelyben a felhasználó típusa taggal egyenlő](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Csak vendégcsoport létrehozása

Hasznos lehet egy új dinamikus csoport létrehozása, amely csak vendégfelhasználókat tartalmaz, így szabályzatokat (például az Azure AD feltételes hozzáférésű szabályzatokat) alkalmazhat rájuk. Hozzon létre egy dinamikus csoportot a fent leírtak szerint, de a **Szabály szintaxis** a következő mezőbe írja be a következő kifejezést:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

Az alábbi képen egy dinamikus csoport szabályszintaxisa látható, amelyet úgy módosítottak, hogy csak a vendégeket tartalmazza, és kizárja a tagfelhasználókat.

![Azokat a szabályt jeleníti meg, amelyben a felhasználó típusa egyenlő a vendéggel](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>További lépések

- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)
- [B2B együttműködési felhasználó hozzáadása szerepkörhöz](add-guest-to-role.md)
- [Feltételes hozzáférés a B2B együttműködési felhasználók számára](conditional-access.md)


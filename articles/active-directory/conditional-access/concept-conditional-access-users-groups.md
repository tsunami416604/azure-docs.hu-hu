---
title: Felhasználók és csoportok a feltételes hozzáférési házirendben – Azure Active Directory
description: Felhasználók és csoportok az Azure AD feltételes hozzáférési szabályzatában
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba1fc856ee9093b628bd86b9847f8fc70b7189c2
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552900"
---
# <a name="conditional-access-users-and-groups"></a>Feltételes hozzáférés: felhasználók és csoportok

A feltételes hozzáférési szabályzatnak tartalmaznia kell egy felhasználói hozzárendelést a döntési folyamat egyik jeleként. A felhasználók belefoglalhatják vagy kizárhatják a feltételes hozzáférési szabályzatokat. 

![A felhasználó mint jel a feltételes hozzáférés által hozott döntésekben](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Felhasználók belefoglalása

A felhasználók ezen listája általában a feltételes hozzáférési szabályzatban a szervezet által megcélzott összes felhasználót tartalmazza. 

A feltételes hozzáférési szabályzat létrehozásakor a következő beállítások érhetők el.

- Nincs
   - Nincs kijelölt felhasználó
- Minden felhasználó
   - Minden olyan felhasználó, aki szerepel a címtárban, beleértve a B2B vendégeket is.
- Felhasználók és csoportok kiválasztása
   - Minden vendég és külső felhasználó
      - Ez a beállítás tartalmazza a B2B-vendégeket és a külső felhasználókat, beleértve az attribútummal rendelkező összes felhasználót `user type` `guest` . Ez a kijelölés minden olyan külső felhasználóra érvényes, amely egy másik szervezettől, például egy felhőalapú megoldás-szolgáltatótól (CSP) bejelentkezett. 
   - Címtárbeli szerepkörök
      - Lehetővé teszi a rendszergazdák számára, hogy kiválasszanak bizonyos Azure AD-címtárbeli szerepköröket a hozzárendelés meghatározásához. Előfordulhat például, hogy a szervezetek szigorúbb házirendet hoznak létre a globális rendszergazdai szerepkörrel rendelkező felhasználók számára.
   - Felhasználók és csoportok
      - Engedélyezi a felhasználók meghatározott csoportjainak célzását. Például a szervezetek kiválaszthatnak egy csoportot, amely a HR-részleg összes tagját tartalmazza, ha egy HR-alkalmazás van kiválasztva a felhőalapú alkalmazásként. Egy csoport bármilyen típusú csoport lehet az Azure AD-ben, beleértve a dinamikus vagy a hozzárendelt biztonsági és terjesztési csoportokat is. A házirend a beágyazott felhasználókra és csoportokra lesz alkalmazva.

> [!WARNING]
> Ha a felhasználók vagy csoportok tagjai több mint 2048 csoportnak, a hozzáférésük blokkolva lehet. Ez a korlátozás a közvetlen és a beágyazott csoporttagság is vonatkozik.

> [!WARNING]
> A feltételes hozzáférési szabályzatok nem támogatják azokat a felhasználókat, akik egy [felügyeleti egységre](../users-groups-roles/roles-admin-units-assign-roles.md) vagy címtár-szerepkörre hatókörrel rendelkező, közvetlenül az adott objektumra (például [Egyéni szerepkörökre](../users-groups-roles/roles-create-custom.md)) kiterjedő címtár-szerepkört rendeltek.

## <a name="exclude-users"></a>Felhasználók kizárása

Ha a szervezetek egyaránt tartalmaznak és kizárnak egy felhasználót vagy csoportot, amelyet a felhasználó vagy csoport kizár a szabályzatból, a kizárási művelet felülbírál egy belefoglalást a házirendbe. A kizárásokat gyakran használják vészhelyzeti hozzáféréshez vagy bomlási fiókokhoz. További információ a segélyhívó fiókokról és azok fontos okairól a következő cikkekben talál további információt: 

* [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](../users-groups-roles/directory-emergency-access.md)
* [Rugalmas hozzáférés-vezérlési felügyeleti stratégia létrehozása Azure Active Directory](../authentication/concept-resilient-controls.md)

A feltételes hozzáférési szabályzat létrehozásakor a következő beállítások választhatók ki.

- Minden vendég és külső felhasználó
   - Ez a beállítás tartalmazza a B2B-vendégeket és a külső felhasználókat, beleértve az attribútummal rendelkező összes felhasználót `user type` `guest` . Ez a kijelölés minden olyan külső felhasználóra érvényes, amely egy másik szervezettől, például egy felhőalapú megoldás-szolgáltatótól (CSP) bejelentkezett. 
- Címtárbeli szerepkörök
   - Lehetővé teszi a rendszergazdák számára, hogy kiválasszanak bizonyos Azure AD-címtárbeli szerepköröket a hozzárendelés meghatározásához. Előfordulhat például, hogy a szervezetek szigorúbb házirendet hoznak létre a globális rendszergazdai szerepkörrel rendelkező felhasználók számára.
- Felhasználók és csoportok
   - Engedélyezi a felhasználók meghatározott csoportjainak célzását. Például a szervezetek kiválaszthatnak egy csoportot, amely a HR-részleg összes tagját tartalmazza, ha egy HR-alkalmazás van kiválasztva a felhőalapú alkalmazásként. Egy csoport bármilyen típusú csoport lehet az Azure AD-ben, beleértve a dinamikus vagy a hozzárendelt biztonsági és terjesztési csoportokat is.

### <a name="preventing-administrator-lockout"></a>Rendszergazdai zárolás megakadályozása

Annak megakadályozása érdekében, hogy a rendszergazda a saját címtárában zárolja magát, amikor az **összes felhasználóra** és **alkalmazásra**alkalmazott házirendet hoz létre, a következő figyelmeztetés jelenik meg.

> Ne zárja ki magát! Javasoljuk, hogy a szabályzatot egy kis felhasználói csoportba alkalmazzon először annak ellenőrzéséhez, hogy a várt módon viselkedik-e. Javasoljuk továbbá, hogy a szabályzatból legalább egy rendszergazdát kizárjon. Ezzel biztosíthatja, hogy a rendszer továbbra is hozzáférjen, és ha módosításra van szükség, frissítheti a szabályzatot. Tekintse át az érintett felhasználókat és alkalmazásokat.

Alapértelmezés szerint a házirend lehetővé teszi az aktuális felhasználó kizárását a házirendből, de ezt az alapértelmezett beállítást a rendszergazda felülbírálhatja, ahogy az alábbi képen is látható. 

![Figyelmeztetés, ne zárja ki magát!](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups-lockout-warning.png)

## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférés: felhőalapú alkalmazások vagy műveletek](concept-conditional-access-cloud-apps.md)

- [Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

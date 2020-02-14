---
title: Felhasználók és csoportok a feltételes hozzáférési házirendben – Azure Active Directory
description: Felhasználók és csoportok az Azure AD feltételes hozzáférési szabályzatában
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36898e75680771a9cb084fa142bb635ddbf51c70
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192128"
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
   - Minden vendég és külső felhasználó (előzetes verzió)
      - Ez a kijelölés tartalmazza a B2B-vendégeket és a külső felhasználókat, beleértve a `user type` attribútummal rendelkező felhasználókat, akik `guest`ra vannak beállítva. Ez a kijelölés minden olyan külső felhasználóra érvényes, amely egy másik szervezettől, például egy felhőalapú megoldás-szolgáltatótól (CSP) bejelentkezett. 
   - Címtárbeli szerepkörök (előzetes verzió)
      - Lehetővé teszi a rendszergazdák számára, hogy kiválasszanak bizonyos Azure AD-címtárbeli szerepköröket a hozzárendelés meghatározásához. Előfordulhat például, hogy a szervezetek szigorúbb házirendet hoznak létre a globális rendszergazdai szerepkörrel rendelkező felhasználók számára.
   - Felhasználók és csoportok
      - Engedélyezi a felhasználók meghatározott csoportjainak célzását. Például a szervezetek kiválaszthatnak egy csoportot, amely a HR-részleg összes tagját tartalmazza, ha egy HR-alkalmazás van kiválasztva a felhőalapú alkalmazásként. Egy csoport bármilyen típusú csoport lehet az Azure AD-ben, beleértve a dinamikus vagy a hozzárendelt biztonsági és terjesztési csoportokat is.

## <a name="exclude-users"></a>Felhasználók kizárása

A kizárásokat gyakran használják vészhelyzeti hozzáféréshez vagy bomlási fiókokhoz. További információ a segélyhívó fiókokról és azok fontos okairól a következő cikkekben talál további információt: 

* [Vészhelyzeti hozzáférési fiókok kezelése az Azure AD-ben](../users-groups-roles/directory-emergency-access.md)
* [Rugalmas hozzáférés-vezérlési felügyeleti stratégia létrehozása Azure Active Directory](../authentication/concept-resilient-controls.md)

A feltételes hozzáférési szabályzat létrehozásakor a következő beállítások választhatók ki.

- Minden vendég és külső felhasználó (előzetes verzió)
   - Ez a kijelölés tartalmazza a B2B-vendégeket és a külső felhasználókat, beleértve a `user type` attribútummal rendelkező felhasználókat, akik `guest`ra vannak beállítva. Ez a kijelölés minden olyan külső felhasználóra érvényes, amely egy másik szervezettől, például egy felhőalapú megoldás-szolgáltatótól (CSP) bejelentkezett. 
- Címtárbeli szerepkörök (előzetes verzió)
   - Lehetővé teszi a rendszergazdák számára, hogy kiválasszanak bizonyos Azure AD-címtárbeli szerepköröket a hozzárendelés meghatározásához. Előfordulhat például, hogy a szervezetek szigorúbb házirendet hoznak létre a globális rendszergazdai szerepkörrel rendelkező felhasználók számára.
- Felhasználók és csoportok
   - Engedélyezi a felhasználók meghatározott csoportjainak célzását. Például a szervezetek kiválaszthatnak egy csoportot, amely a HR-részleg összes tagját tartalmazza, ha egy HR-alkalmazás van kiválasztva a felhőalapú alkalmazásként. Egy csoport bármilyen típusú csoport lehet az Azure AD-ben, beleértve a dinamikus vagy a hozzárendelt biztonsági és terjesztési csoportokat is.

## <a name="next-steps"></a>Következő lépések

- [Feltételes hozzáférés: felhőalapú alkalmazások vagy műveletek](concept-conditional-access-cloud-apps.md)

- [Feltételes hozzáférés – közös szabályzatok](concept-conditional-access-policy-common.md)

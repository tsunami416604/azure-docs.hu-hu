---
title: Felhasználók és csoportok a feltételes hozzáférési házirendben – Azure Active Directory
description: Kik felhasználók és csoportok egy Azure AD feltételes hozzáférési szabályzatban
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192128"
---
# <a name="conditional-access-users-and-groups"></a>Feltételes hozzáférés: Felhasználók és csoportok

A feltételes hozzáférési házirendnek tartalmaznia kell egy felhasználói hozzárendelést a döntési folyamat egyik jeleként. A felhasználók bevonhatók vagy kizárhatók a feltételes hozzáférési házirendekből. 

![Felhasználó, mint egy jel a döntéseket a feltételes hozzáférés](./media/concept-conditional-access-users-groups/conditional-access-users-and-groups.png)

## <a name="include-users"></a>Felhasználók felvétele

Ez a lista általában tartalmazza az összes felhasználó t a szervezet által megcélzott feltételes hozzáférési házirend. 

A következő beállítások at lehet felvenni a feltételes hozzáférési házirend ek létrehozásakor.

- None
   - Nincs kijelölt felhasználó
- Minden felhasználó
   - Minden felhasználó, hogy létezik a könyvtárban, beleértve a B2B vendégek.
- Felhasználók és csoportok kiválasztása
   - Minden vendég- és külső felhasználó (előzetes verzió)
      - Ez a választás magában foglalja a B2B `user type` vendégek és `guest`a külső felhasználók, beleértve a felhasználó az attribútum beállítása. Ez a beállítás minden olyan külső felhasználóra is vonatkozik, aki egy másik szervezetből, például egy felhőszolgáltatótól (CSP) jelentkezett be. 
   - Könyvtári szerepkörök (előzetes verzió)
      - Lehetővé teszi a rendszergazdák számára, hogy válasszaki a hozzárendelés meghatározásához használt azure AD címtárszerepkörök. A szervezetek például szigorúbb házirendet hozhatnak létre a globális rendszergazdai szerepkörhöz rendelt felhasználókra vonatkozóan.
   - Felhasználók és csoportok
      - Lehetővé teszi a felhasználók meghatározott csoportjainak célzását. Például a szervezetek kiválaszthatják azt a csoportot, amely a HR-részleg összes tagját tartalmazza, ha egy HR-alkalmazást választanak ki felhőalkalmazásként. A csoport bármilyen típusú csoport lehet az Azure AD-ben, beleértve a dinamikus vagy hozzárendelt biztonsági és terjesztési csoportokat.

## <a name="exclude-users"></a>Felhasználók kizárása

A kizárásokat gyakran használják vészelérési vagy üvegtöréses fiókokhoz. További információ a vészelérési fiókokról és azok fontos tudnivalóiról az alábbi cikkekben található: 

* [Vészelérési fiókok kezelése az Azure AD-ben](../users-groups-roles/directory-emergency-access.md)
* [Rugalmas hozzáférés-vezérlési stratégia létrehozása az Azure Active Directoryval](../authentication/concept-resilient-controls.md)

A következő lehetőségek kizárhatják a feltételes hozzáférési házirendek létrehozásakor.

- Minden vendég- és külső felhasználó (előzetes verzió)
   - Ez a választás magában foglalja a B2B `user type` vendégek és `guest`a külső felhasználók, beleértve a felhasználó az attribútum beállítása. Ez a beállítás minden olyan külső felhasználóra is vonatkozik, aki egy másik szervezetből, például egy felhőszolgáltatótól (CSP) jelentkezett be. 
- Könyvtári szerepkörök (előzetes verzió)
   - Lehetővé teszi a rendszergazdák számára, hogy válasszaki a hozzárendelés meghatározásához használt azure AD címtárszerepkörök. A szervezetek például szigorúbb házirendet hozhatnak létre a globális rendszergazdai szerepkörhöz rendelt felhasználókra vonatkozóan.
- Felhasználók és csoportok
   - Lehetővé teszi a felhasználók meghatározott csoportjainak célzását. Például a szervezetek kiválaszthatják azt a csoportot, amely a HR-részleg összes tagját tartalmazza, ha egy HR-alkalmazást választanak ki felhőalkalmazásként. A csoport bármilyen típusú csoport lehet az Azure AD-ben, beleértve a dinamikus vagy hozzárendelt biztonsági és terjesztési csoportokat.

## <a name="next-steps"></a>További lépések

- [Feltételes hozzáférés: Felhőalapú alkalmazások és műveletek](concept-conditional-access-cloud-apps.md)

- [Feltételes hozzáférés közös házirendjei](concept-conditional-access-policy-common.md)

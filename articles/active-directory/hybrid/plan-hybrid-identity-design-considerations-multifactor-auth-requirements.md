---
title: Hibrid identitástervezés – többtényezős hitelesítési követelmények Azure | Microsoft dokumentumok
description: Feltételes hozzáférés-vezérléssel az Azure Active Directory ellenőrzi a felhasználó hitelesítésekénél és az alkalmazáshoz való hozzáférés engedélyezésekor választott feltételeket. Ha ezek a feltételek teljesülnek, a felhasználó hitelesítve lesz, és hozzáférést biztosít az alkalmazáshoz.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4743195fc79d43571ec79a13b8518edc7e81379b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109295"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás többtényezős hitelesítési követelményeinek meghatározása
A mobilitás ezen világában, amikor a felhasználók a felhőben és bármilyen eszközről férnek hozzá az adatokhoz és az alkalmazásokhoz, ez az információ biztosítása a legfontosabb.  Minden nap van egy új főcím egy biztonsági résről.  Bár nincs garancia az ilyen jogsértések ellen, a többtényezős hitelesítés további biztonsági réteget biztosít a jogsértések megelőzésére.
Első ként értékelje ki a többtényezős hitelesítés szervezetkövetelményeinek. Ez azt, amit a szervezet próbál biztosítani.  Ez az értékelés fontos a többtényezős hitelesítéshez való csoportok felhasználóinak beállítására és engedélyezésére vonatkozó technikai követelmények meghatározásához.

Ügyeljen arra, hogy válaszoljon a következő:

* Vállalata próbálja biztonságossá tenni a Microsoft-alkalmazásokat? 
* Hogyan teszik közzé ezeket az alkalmazásokat?
* A vállalat távoli hozzáférést biztosít az alkalmazottak számára a helyszíni alkalmazások eléréséhez?

Ha igen, milyen típusú távoli hozzáférés? Azt is ki kell értékelnie, hogy az alkalmazásokat elérő felhasználók hol találhatók. Ez az értékelés egy másik fontos lépés a megfelelő többtényezős hitelesítési stratégia meghatározásához. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* Hol találhatók a felhasználók?
* Lehet őket elhelyezni bárhol?
* Szeretné-e a vállalata korlátozásokat megállapítani a felhasználó tartózkodási helye szerint?

Miután megértette ezeket a követelményeket, fontos, hogy a felhasználó többtényezős hitelesítésre vonatkozó követelményeit is kiértékelje. Ez az értékelés azért fontos, mert meghatározza a többtényezős hitelesítés bevezetésének követelményeit. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* A felhasználók ismerik a többtényezős hitelesítést?
* Szükség lesz-e bizonyos célokra a további hitelesítéshez?  
  * Ha igen, mindig, amikor külső hálózatokról érkezik, vagy bizonyos alkalmazásokhoz fér hozzá, vagy más feltételek mellett?
* A felhasználóknak képzésre lesz szükségük a többtényezős hitelesítés beállításáról és megvalósításáról?
* Melyek azok a kulcsfontosságú forgatókönyvek, amelyeket a vállalat a felhasználók számára engedélyezni kíván a többtényezős hitelesítés engedélyezéséhez?

Az előző kérdések megválaszolása után képes lesz megérteni, hogy vannak-e már a helyszínen megvalósított többtényezős hitelesítések. Ez az értékelés fontos a többtényezős hitelesítéshez való csoportok felhasználóinak beállítására és engedélyezésére vonatkozó technikai követelmények meghatározásához. Ügyeljen arra, hogy válaszoljon a következő kérdésekre:

* A vállalatnak védenie kell a kiemelt fiókokat az MFA-val?
* A vállalatnak engedélyeznie kell az MFA-t bizonyos alkalmazásokhoz megfelelőségi okokból?
* A vállalatnak engedélyeznie kell az MFA-t az alkalmazás összes jogosult felhasználója vagy csak a rendszergazdák számára?
* Szüksége van arra, hogy az MFA mindig engedélyezve legyen, vagy csak akkor, ha a felhasználók a vállalati hálózaton kívül vannak bejelentkezve?

## <a name="next-steps"></a>További lépések
[Hibrid identitásbevezetési stratégia definiálása](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Lásd még
[Tervezési szempontok – áttekintés](plan-hybrid-identity-design-considerations-overview.md)


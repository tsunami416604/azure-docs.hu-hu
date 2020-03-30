---
title: Kockázati házirendek – Azure Active Directory identitásvédelem
description: Kockázati házirendek engedélyezése és konfigurálása az Azure Active Directory identitásvédelemben
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ffa08f7ebf013d42d6da0589ce0f1ccc97289de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707005"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Útmutató: Kockázati házirendek konfigurálása és engedélyezése

Amint azt az előző cikkben, [identitásvédelem szabályzatok](concept-identity-protection-policies.md) két kockázati szabályzatok, amelyek et engedélyezhet a címtárban. 

- Bejelentkezési kockázati szabályzat
- Felhasználói kockázati házirend

![A biztonság áttekintése lap a felhasználói és bejelentkezési kockázati házirendek engedélyezéséhez](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Mindkét szabályzat a környezetben a kockázatészlelésre adott válasz automatizálására, valamint a kockázat észleléseesetén a felhasználók önjavító működésének lehetővé teszi. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Előfeltételek 

Ha a szervezet azt szeretné, hogy a felhasználók önjavító kockázatok észlelésekor, a felhasználók regisztrálva kell lennie mind az önkiszolgáló jelszó-visszaállítás és az Azure többtényezős hitelesítés. Javasoljuk, hogy a lehető legjobb élmény érdekében [engedélyezze](../authentication/howto-registration-mfa-sspr-combined.md) a kombinált biztonsági adatok regisztrációs élményét. Lehetővé teszi a felhasználók önjavító teszi őket vissza a produktív állapot gyorsabban rendszergazdai beavatkozás nélkül. A rendszergazdák továbbra is láthatják ezeket az eseményeket, és a tény után kivizsgálhatják azokat. 

## <a name="choosing-acceptable-risk-levels"></a>Elfogadható kockázati szintek kiválasztása

A szervezeteknek el kell dönteniük, hogy milyen kockázati szintet hajlandók elfogadni a felhasználói élmény és a biztonsági állapot kiegyensúlyozása érdekében. 

A Microsoft javaslata az, hogy a felhasználói kockázati házirend küszöbértékét **magasra állítsa,** a bejelentkezési kockázati házirendet **pedig közepesre és felette.**

A **magas** küszöbérték kiválasztása csökkenti a házirend aktiválásának számát, és minimálisra csökkenti a felhasználókra gyakorolt hatást. Azonban kizárja az **alacsony** és **közepes** kockázatú észlelések a házirendből, amely nem akadályozhatja meg a támadót a feltört identitás kihasználásában. Az **alacsony** küszöbérték kiválasztása további felhasználói megszakításokat eredményez, de növeli a biztonsági pozíciót.

## <a name="exclusions"></a>Kizárások

Minden házirend lehetővé teszi a felhasználók kizárását, például a [segélyhívó vagy a törésüveg-rendszergazdai fiókokat.](../users-groups-roles/directory-emergency-access.md) A szervezetek a fiókok használata alapján meghatározhatják, hogy más fiókokat is ki kell zárniuk az egyes házirendekből. Minden kizárást rendszeresen felül kell vizsgálni annak megállapítására, hogy továbbra is alkalmazhatók-e.

A konfigurált megbízható [hálózati helyeket](../conditional-access/location-condition.md) az Identity Protection bizonyos kockázatészlelésekben használja a hamis pozitív értékek csökkentése érdekében.

## <a name="enable-policies"></a>Házirendek engedélyezése

A felhasználói kockázat és a bejelentkezési kockázati házirendek engedélyezéséhez hajtsa végre a következő lépéseket.

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
1. Tallózás az **Azure Active Directory** > **biztonsági** > **identitásvédelem** > **– áttekintés e-címben.**
1. Válassza **a Felhasználói kockázati házirend konfigurálása lehetőséget.**
   1. Hozzárendelések **csoportban**
      1. **Felhasználók** – Válassza a **Minden felhasználó** vagy A személyek és csoportok **kiválasztása lehetőséget,** ha korlátozza a bevezetést.
         1. Tetszés szerint kizárhatja a felhasználókat a házirendből.
      1. **Feltételek** - **A Felhasználó kockázata** A Microsoft ajánlása az, hogy ezt a beállítást **magasra**állítsa.
   1. A **Vezérlők csoportban**
      1. **Access** - A Microsoft javaslata a **hozzáférés engedélyezése** és **a jelszómódosítás megkövetelése**.
   1. **Házirend kényszerítése** - **On**
   1. **Mentés** – Ez a művelet visszaadja az **Áttekintés** lapot.
1. Válassza **a Bejelentkezési kockázati házirend konfigurálása**lehetőséget.
   1. Hozzárendelések **csoportban**
      1. **Felhasználók** – Válassza a **Minden felhasználó** vagy A személyek és csoportok **kiválasztása lehetőséget,** ha korlátozza a bevezetést.
         1. Tetszés szerint kizárhatja a felhasználókat a házirendből.
      1. **Feltételek** - **Bejelentkezési kockázat** A Microsoft ajánlása szerint ezt a beállítást közepes és újabb **beállításra kell állítania.**
   1. A **Vezérlők csoportban**
      1. **Access** - A Microsoft javaslata a **hozzáférés engedélyezése** és **a többtényezős hitelesítés megkövetelése**.
   1. **Házirend kényszerítése** - **On**
   1. **Mentés**

## <a name="next-steps"></a>További lépések

- [Az Azure többtényezős hitelesítésregisztrációs házirendjének engedélyezése](howto-identity-protection-configure-mfa-policy.md)

- [Mi a kockázat?](concept-identity-protection-risks.md)

- [Kockázatészlelések vizsgálata](howto-identity-protection-investigate-risk.md)

- [Kockázatészlelés szimulálása](howto-identity-protection-simulate-risk.md)

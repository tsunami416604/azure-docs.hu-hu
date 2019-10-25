---
title: Kockázatkezelési szabályzatok konfigurálása és engedélyezése a Azure Active Directory Identity Protectionban
description: Kockázatkezelési szabályzatok engedélyezése és konfigurálása Azure Active Directory Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79f919633f6b1912ef07b7ff636eb60fb3d5859f
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72886960"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>Útmutató: kockázatkezelési szabályzatok konfigurálása és engedélyezése

Az előző cikkben megtanultuk, hogy az [Identity Protection-szabályzatok](concept-identity-protection-policies.md) két kockázati szabályzattal rendelkezünk, amelyeket a címtárban engedélyezünk. 

- Bejelentkezési kockázati házirend
- Felhasználói kockázati házirend

![Biztonsági Áttekintés lap a felhasználói és bejelentkezési kockázati házirendek engedélyezéséhez](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

Mindkét szabályzat úgy működik, hogy automatizálja a kockázati észlelésekre adott válaszokat a környezetben, és lehetővé teszi a felhasználók számára, hogy a kockázatok észlelése után önmagukban is szervizelni lehessen őket. 

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

## <a name="prerequisites"></a>Előfeltételek 

Ha a szervezet szeretné lehetővé tenni a felhasználók számára, hogy a kockázatok észlelése után is önmagukban tudják javítani a felhasználókat, regisztrálni kell mind az önkiszolgáló jelszó-visszaállítást, mind az Azure-Multi-Factor Authentication. Javasoljuk, hogy a lehető legjobb felhasználói élmény érdekében [engedélyezze a kombinált biztonsági információk regisztrálását](../authentication/howto-registration-mfa-sspr-combined.md) . Lehetővé teheti a felhasználók számára, hogy a rendszergazda beavatkozása nélkül gyorsabban visszakapjanak produktív állapotba. A rendszergazdák továbbra is láthatják ezeket az eseményeket, és a tény után kivizsgálják azokat. 

## <a name="choosing-acceptable-risk-levels"></a>Elfogadható kockázati szintek kiválasztása

A szervezeteknek el kell dönteniük, hogy milyen kockázati szintet kívánnak elfogadni a felhasználói élmény és a biztonsági helyzet elfogadásához. 

A Microsoft javaslata, hogy a felhasználói kockázati házirend küszöbértékét **magas** értékre állítsa, a bejelentkezési kockázati házirendet pedig **közepesre vagy annál magasabbra**.

A **magas** küszöbérték kiválasztása csökkenti a szabályzatok indításának számát, és minimálisra csökkenti a felhasználókra gyakorolt hatást. Azonban nem zárja ki az **alacsony** és **közepes** kockázati észleléseket a szabályzatból, ami esetleg nem gátolja meg, hogy a támadók illetéktelenül feltört identitást használjanak. Az **alacsony** küszöbérték kiválasztásával további felhasználói megszakítások jelennek meg, a biztonsági helyzet azonban nagyobb.

## <a name="exclusions"></a>Korlátozások

Az összes házirend lehetővé teszi a felhasználók kizárását, például a [vészhelyzeti vagy a break-Glass rendszergazdai fiókjait](../users-groups-roles/directory-emergency-access.md). A szervezetek meghatározhatják, hogy a fiókok használatának módja alapján ki kell zárniuk a más fiókokat a meghatározott házirendekből. Az összes kizárást rendszeresen felül kell vizsgálni, hogy ellenőrizze, hogy továbbra is alkalmazhatók-e.

## <a name="enable-policies"></a>Házirendek engedélyezése

A felhasználói kockázat és a bejelentkezési kockázati házirendek engedélyezéséhez hajtsa végre az alábbi lépéseket.

1. Lépjen az [Azure Portalra](https://portal.azure.com).
1. Keresse meg **Azure Active Directory** > **biztonsági** > **Identity Protection** > **áttekintését**.
1. Válassza a **felhasználói kockázati házirend konfigurálása**lehetőséget.
   1. A **hozzárendelések** alatt
      1. **Felhasználók** – válassza a **minden felhasználó** lehetőséget **, vagy válassza az egyének és csoportok lehetőséget,** ha korlátozza a bevezetést.
         1. Opcionálisan dönthet úgy is, hogy kizárja a felhasználókat a szabályzatból.
      1. **Feltételek** - **felhasználói kockázat** a Microsoft javaslata, hogy ezt a beállítást **magas**értékre állítsa be.
   1. A **vezérlők** területen
      1. **Hozzáférés** – a Microsoft javaslata a **hozzáférés engedélyezése** és a **jelszó megkövetelése**.
   1. Házirend **- - ** **érvénybe léptetése**
   1. **Mentés** – ez a művelet visszaküldi az **Áttekintés** oldalra.
1. Válassza a **bejelentkezési kockázati házirend konfigurálása**lehetőséget.
   1. A **hozzárendelések** alatt
      1. **Felhasználók** – válassza a **minden felhasználó** lehetőséget **, vagy válassza az egyének és csoportok lehetőséget,** ha korlátozza a bevezetést.
         1. Opcionálisan dönthet úgy is, hogy kizárja a felhasználókat a szabályzatból.
      1. A **Feltételeit** - **bejelentkezési kockázat** a Microsoft javaslata, hogy ezt a beállítást **közepes vagy magasabb**értékre állítsa.
   1. A **vezérlők** területen
      1. **Hozzáférés** – a Microsoft javaslata, hogy **engedélyezze a hozzáférést** és a **többtényezős hitelesítés megkövetelését**.
   1. Házirend **- - ** **érvénybe léptetése**
   1. **Mentés**

## <a name="next-steps"></a>Következő lépések

- [Az Azure Multi-Factor Authentication regisztrációs szabályzatának engedélyezése](howto-identity-protection-configure-mfa-policy.md)

- [Mi a kockázat](concept-identity-protection-risks.md)

- [Kockázati észlelések vizsgálata](howto-identity-protection-investigate-risk.md)

- [Kockázati észlelések szimulálása](howto-identity-protection-simulate-risk.md)

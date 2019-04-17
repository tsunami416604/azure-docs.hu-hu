---
title: Felhasználónév keresése során bejelentkezési hitelesítés – az Azure Active Directory |} A Microsoft Docs
description: Képernyő-felhasználónév keresési üzenetkezelési tükrözi a bejelentkezés során
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/15/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b6846c5f907c41db16e99883be7041a68357586
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608775"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Azure Active Directory bejelentkezési lapok hitelesítőtartomány

Módosítjuk az Azure Active Directory (Azure AD) bejelentkezési folyamatát, hogy lehetővé tegyük az új hitelesítési módszerek használatát, és hogy javítsuk a használati élményt. A bejelentkezés során az Azure AD meghatározza, hogy a felhasználónak hol kell hitelesítést végeznie. Az Azure AD intelligens döntéseket hoz a bejelentkezési oldalon megadott felhasználónévhez tartozó szervezeti és felhasználói beállítások alapján. Ez egy újabb lépés végeredményben a jelszó nélküli használathoz, amelynél lehetővé válik majd további hitelesítő adatok, például a FIDO 2.0 használata is.

## <a name="home-realm-discovery-behavior"></a>Hitelesítőtartomány felderítési működését

Hitelesítőtartomány hagyományosan szabályozott volt a bejelentkezéskor megadott tartomány vagy egy otthoni Kezdőtartomány-felderítés házirend néhány örökölt alkalmazások számára. Például a felderítési működését a egy Azure Active Directory-felhasználót sikerült elírta a felhasználóneve, de továbbra is érkezik, a szervezeti hitelesítő adatok gyűjtése képernyőn. Ez akkor fordul elő, amikor a felhasználó megfelelően biztosít a szervezet azon tartomány neve "contoso.com". Ez a viselkedés nem teszi lehetővé, hogy a folyamatot az egyes felhasználók szintjén testre szabjuk.

Hitelesítő adatok szélesebb körének támogatására, és a használhatóságot, az Azure Active Directory felhasználónevet keresési viselkedés a bejelentkezési folyamat során most frissült. Az új viselkedés intelligens döntéseket bérlő és a felhasználói szintű beállítások alapján a megadott felhasználónév egyeztetése a bejelentkezési oldalon olvassa el. Ahhoz, hogy ez lehetséges, az Azure Active Directory ellenőrzi, hogy ha a felhasználónév, amelyet is meg kell adni a bejelentkezési oldalon létezik a megadott tartományhoz, vagy az átirányítja a felhasználót a hitelesítő adatok megadására.

További előnye ennek, javult a hibajelentések üzenetkezelés. Szívesen adunk néhány ötletet a üzenetküldési bejelentkezés egy alkalmazásba, amely támogatja a csak az Azure Active Directory-felhasználók továbbfejlesztett hiba.

1. A felhasználónév rosszul van, vagy a felhasználónév már nem szinkronizált Azure ad-hez:
  
    ![a felhasználónév rosszul adott meg, vagy nem található](./media/signin-realm-discovery/typo-username.png)
  
2. A tartománynév rosszul van:
  
    ![a tartománynév rosszul adott meg, vagy nem található](./media/signin-realm-discovery/typo-domain.png)
  
3. Felhasználó megpróbál bejelentkezni egy ismert fogyasztói tartománynak:
  
    ![Jelentkezzen be egy ismert fogyasztói tartománynak](./media/signin-realm-discovery/consumer-domain.png)
  
4. A jelszó rosszul van, de a felhasználónév pontos:  
  
    ![jelszava hibásan írta be a helyes felhasználónévvel](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> Ez a funkció lehet hatással a függő entitások kényszerített az összevonási Kezdőtartomány felderítésének régi tartományi szint összevont tartományok. Ha összevont tartományt fogja támogatni a frissítéseket, lásd: [kezdőlap kezdőtartomány-felderítés során jelentkezzen be a Microsoft 365-szolgáltatásokhoz](https://azure.microsoft.com/en-us/updates/signin-hrd/). Addig is egyes szervezetek számára, hogy jelentkezzen be az Azure Active Directoryban nem létezik, de tartalmazza a megfelelő tartománynevet, felhasználónevet, mivel a tartománynevek átirányítja a felhasználókat jelenleg a szervezeti tartományi végpontra szakképzett. Az új bejelentkezés működése nem engedélyezi ezt. A felhasználó értesítést kap, javítsa ki a felhasználó nevét, és azok nincs engedélye arra, hogy jelentkezzen be egy felhasználónevet, amely nem létezik az Azure Active Directoryban.
>
> Ha Ön vagy a szervezet rendelkezik gyakorlatokat, amelyek a régi viselkedése attól függ, fontos a szervezet rendszergazdák alkalmazott bejelentkezési és hitelesítési dokumentáció frissítésére és alkalmazottak számára az Azure Active Directory felhasználónevet használva a bejelentkezéshez betanításához.
  
Ha kérdése merül fel a új működése, hagyja meg a megjegyzéseit alfejlécekkel a **visszajelzés** című szakaszát.  

## <a name="next-steps"></a>További lépések

[A bejelentkezési márkajelzés testreszabása](../fundamentals/add-custom-domain.md)

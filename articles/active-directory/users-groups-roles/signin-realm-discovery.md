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
ms.date: 04/03/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91d0dbaf9b648f42ef535d8b491df04b2c7042d7
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007569"
---
# <a name="home-realm-discovery-during-sign-in-for-microsoft-365-services"></a>Hitelesítőtartomány bejelentkezés során a Microsoft 365-szolgáltatásokhoz

Az Azure Active Directory (Azure AD) bejelentkezési viselkedés kivonva biztosíthat hitelesítési módszereket, és a modulok használhatóságának fejlesztésében megváltozik. Bejelentkezés, során az Azure AD határozza meg, ahol a felhasználó hitelesítenie kell. Azure AD-ban az intelligens döntések által megadott felhasználónév egyeztetése a bejelentkezési oldalon a szervezet és a felhasználói beállítások. Ez az egy lépésre a jelszó-mentes jövőbeli, amely lehetővé teszi további hitelesítő adatok, például a FIDO 2.0.

## <a name="home-realm-discovery-behavior"></a>Hitelesítőtartomány felderítési működését

Hitelesítőtartomány hagyományosan szabályozott volt a bejelentkezéskor megadott tartomány vagy egy otthoni Kezdőtartomány-felderítés házirend néhány örökölt alkalmazások számára. Például a régi felderítési működését a egy Azure Active Directory-felhasználót sikerült elírta a felhasználóneve, de továbbra is érkezik, a szervezeti hitelesítő adatok gyűjtése képernyőn. Ez akkor fordul elő, amikor a felhasználó megfelelően biztosít a szervezet azon tartomány neve "contoso.com". Ez a viselkedés nem engedélyezi a granularitási használata során egy adott felhasználó testreszabhatja.

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
  
## <a name="additional-info"></a>Additional Info (További információ)

A továbbfejlesztett bejelentkezési felhasználói élmény mellett ez a változás, amely segíthet csökkenteni a nagy méretű felhasználónév enumerálás való visszaélés mechanizmusok magában foglalja.

Ez a változás kezdetben célja a felügyelt tartományok és a 2019. május bevezetéséről kezdődik, de nem indul el, a végén a 2019 összevont tartományokban való bevezetéséről. Összevont tartományok pontos bevezetés dátumai ügyfélvisszajelzések függ.

> [!IMPORTANT]
> Ez a szolgáltatás jelentős hatással lesz a függő entitások kényszerített az összevonási Kezdőtartomány felderítésének régi tartományi szint összevont tartományokban. Egyes szervezetek számára, hogy jelentkezzen be az Azure Active Directoryban nem létezik, de tartalmazza a megfelelő tartománynevet, felhasználónevet, mivel a tartománynevek átirányítja a felhasználókat jelenleg a szervezeti tartományi végpontra szakképzett. Az új bejelentkezés működése nem engedélyezi ezt. A felhasználó értesítést kap, javítsa ki a felhasználó nevét, és azok nincs engedélye arra, hogy jelentkezzen be egy felhasználónevet, amely nem létezik az Azure Active Directoryban.
>
> Ha Ön vagy a szervezet rendelkezik gyakorlatokat, amelyek a régi viselkedése attól függ, fontos a szervezet rendszergazdák alkalmazott bejelentkezési és hitelesítési dokumentáció frissítésére és alkalmazottak számára az Azure Active Directory felhasználónevet használva a bejelentkezéshez betanításához.
  
Ha kérdése merül fel a új működése, írja meg a megjegyzéseit alfejlécekkel a **visszajelzés** című szakaszát.  

## <a name="next-steps"></a>További lépések

[A bejelentkezési márkajelzés testreszabása](../fundamentals/add-custom-domain.md)
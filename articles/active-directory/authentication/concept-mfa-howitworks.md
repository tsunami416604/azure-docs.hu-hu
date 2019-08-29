---
title: Azure Multi-Factor Authentication – működés – Azure Active Directory
description: Az Azure Multi-Factor Authentication szolgáltatás révén biztonságosabb a hozzáférés az adatokhoz és az alkalmazásokhoz, és a felhasználók is egyszerűbben jelentkezhetnek be.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57b441803a066b794d17b46afec4d930b94ebdea
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125133"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Működés: Azure Multi-Factor Authentication

A kétlépéses ellenőrzés biztonsága a rétegzett megközelítésben rejlik. A több hitelesítési tényező kiegyezése jelentős kihívást jelent a támadók számára. Még ha a támadó a felhasználó jelszavának megismerésére is képes, a további hitelesítési módszer birtoklása nélkül is használhatatlan marad. Úgy működik, hogy a következő hitelesítési módszerek közül kettőt vagy többet igényel:

* Amit ismer (általában jelszó)
* Valami, ami rendelkezik (olyan megbízható eszköz, amely nem könnyen duplikált, például telefon)
* Egy dolog (biometria)

<center>

![Fogalmi hitelesítési módszerek képe](./media/concept-mfa-howitworks/methods.png)</center>

Az Azure Multi-Factor Authentication (MFA) segíti az adathozzáférést és az alkalmazásokhoz való hozzáférést a felhasználók egyszerűségének fenntartása mellett. További biztonságot nyújt a hitelesítés második formáját igényli, és erős hitelesítést tesz lehetővé számos könnyen használható [hitelesítési módszer](concept-authentication-methods.md)segítségével. A rendszergazda által létrehozott konfigurációs döntések alapján előfordulhat, hogy a felhasználók nem tudják feltámadni az MFA-t.

## <a name="how-to-get-multi-factor-authentication"></a>Hogyan szerezhet be Multi-Factor Authentication?

Multi-Factor Authentication a következő ajánlatok részeként érkezik:

* **Prémium szintű Azure Active Directory** vagy **Microsoft 365 vállalati verzió** – a többtényezős hitelesítés megkövetelése érdekében feltételes hozzáférési szabályzatokkal teljes funkcionalitású Azure-multi-Factor Authentication használ.

* **Ingyenes Azure ad** vagy önálló **Office 365** -licencek – az előre létrehozott feltételes hozzáférési alapszintű [védelmi házirendek](../conditional-access/concept-baseline-protection.md) használatával többtényezős hitelesítést igényelhet a felhasználók és a rendszergazdák számára.

* **Azure Active Directory globális rendszergazdák** – az Azure multi-Factor Authentication képességeinek egy részhalmaza elérhető a globális rendszergazdai fiókok elleni védelemhez.

> [!NOTE]
> Az új ügyfelek már nem vásárolhatják meg az Azure Multi-Factor Authentication önálló ajánlatként, amely 2018. szeptember 1-től érvényes. A többtényezős hitelesítés továbbra is elérhető lesz prémium szintű Azure AD licencekben.

## <a name="supportability"></a>Támogatási lehetőségek

Mivel a legtöbb felhasználó megszokta, hogy csak a jelszavakat használja a hitelesítéshez, fontos, hogy a szervezet minden felhasználóval kommunikáljon a folyamattal kapcsolatban. A tudatosságnövelő szolgáltatás csökkenti annak a valószínűségét, hogy a felhasználók az MFA-hoz kapcsolódó kisebb problémák esetén meghívja az ügyfélszolgálatot. Vannak azonban olyan helyzetek, amikor átmenetileg le kell tiltani az MFA-t. Az alábbi irányelvek segítségével megismerheti, hogyan kezelheti ezeket a forgatókönyveket:

* A támogatási munkatársak betanítása olyan helyzetek kezelésére, amikor a felhasználó nem tud bejelentkezni, mert nem férnek hozzá a hitelesítési módszerekhez, vagy nem működnek megfelelően.
   * Ha feltételes hozzáférési szabályzatokat használ az Azure MFA szolgáltatáshoz, a támogatási munkatársak hozzáadhatnak egy felhasználót az MFA-t igénylő szabályzatból kizárt csoportokhoz.
* A kétlépéses ellenőrzési kérések minimalizálásához érdemes megfontolnia a feltételes hozzáférés elnevezett helyeinek használatát. Ezzel a funkcióval a rendszergazdák megkerülhetik a kétlépéses ellenőrzést olyan felhasználók számára, akik biztonságos megbízható hálózati helyről jelentkeznek be, például az új felhasználók bevezetéséhez használt hálózati szegmensek.
* [Azure ad Identity Protection](../active-directory-identityprotection.md) üzembe helyezése és a kockázati észlelések alapján történő kétlépéses ellenőrzés elindítása.

## <a name="next-steps"></a>További lépések

- [Lépésenkénti Azure Multi-Factor Authentication üzembe helyezés](howto-mfa-getstarted.md)

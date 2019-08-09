---
title: Azure multi-Factor Authentication – hogyan működik – Azure Active Directory
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
ms.openlocfilehash: 0d4f19c16149dbed34c16d021ff7100cace9a06a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879147"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Működés: Azure Multi-Factor Authentication

A kétlépéses ellenőrzés biztonsága a rétegzett megközelítésben rejlik. A több hitelesítési tényező kiegyezése jelentős kihívást jelent a támadók számára. Még ha a támadó a felhasználó jelszavának megismerésére is képes, a további hitelesítési módszer birtoklása nélkül is használhatatlan marad. Úgy működik, hogy a következő hitelesítési módszerek közül kettőt vagy többet igényel:

* Amit ismer (általában jelszó)
* Valami, ami rendelkezik (olyan megbízható eszköz, amely nem könnyen duplikált, például telefon)
* Egy dolog (biometria)

<center>

![Fogalmi hitelesítési módszerek képe](./media/concept-mfa-howitworks/methods.png)</center>

Az Azure multi-Factor Authentication (MFA) segítségével biztosítható az adat-és alkalmazásokhoz való hozzáférés, miközben a felhasználók egyszerűsége is megmarad. További biztonságot nyújt a hitelesítés második formáját igényli, és erős hitelesítést tesz lehetővé számos könnyen használható [hitelesítési módszer](concept-authentication-methods.md)segítségével. A rendszergazda által létrehozott konfigurációs döntések alapján előfordulhat, hogy a felhasználók nem tudják feltámadni az MFA-t.

## <a name="how-to-get-multi-factor-authentication"></a>Hogyan lehet beolvasni a multi-Factor Authentication szolgáltatást?

A multi-Factor Authentication a következő ajánlatok részeként érhető el:

* A többtényezős hitelesítés megköveteléséhez a feltételes hozzáférési szabályzatokkal **prémium szintű Azure Active Directory** vagy **Microsoft 365 vállalati verzió** – az Azure multi-Factor Authentication teljes funkcionalitású használata.

* **Ingyenes Azure ad** vagy önálló **Office 365** -licencek – az előre létrehozott feltételes hozzáférési alapszintű [védelmi házirendek](../conditional-access/concept-baseline-protection.md) használatával többtényezős hitelesítést igényelhet a felhasználók és a rendszergazdák számára.

* **Azure Active Directory globális rendszergazdák** – az Azure multi-Factor Authentication funkcióinak egy része elérhető a globális rendszergazdai fiókok elleni védelemhez.

> [!NOTE]
> Az új ügyfelek már nem vásárolhatják meg az Azure multi-Factor Authenticationt önálló ajánlatként, amely 2018. szeptember 1-től érvényes. A többtényezős hitelesítés továbbra is elérhető lesz prémium szintű Azure AD licencekben.

## <a name="supportability"></a>Támogatási lehetőségek

Mivel a legtöbb felhasználó megszokta, hogy csak a jelszavakat használja a hitelesítéshez, fontos, hogy a szervezet minden felhasználóval kommunikáljon a folyamattal kapcsolatban. A tudatosságnövelő szolgáltatás csökkenti annak a valószínűségét, hogy a felhasználók az MFA-hoz kapcsolódó kisebb problémák esetén meghívja az ügyfélszolgálatot. Vannak azonban olyan helyzetek, amikor átmenetileg le kell tiltani az MFA-t. Az alábbi irányelvek segítségével megismerheti, hogyan kezelheti ezeket a forgatókönyveket:

* A támogatási munkatársak betanítása olyan helyzetek kezelésére, amikor a felhasználó nem tud bejelentkezni, mert nem férnek hozzá a hitelesítési módszerekhez, vagy nem működnek megfelelően.
   * Ha feltételes hozzáférési szabályzatokat használ az Azure MFA szolgáltatáshoz, a támogatási munkatársak hozzáadhatnak egy felhasználót az MFA-t igénylő szabályzatból kizárt csoportokhoz.
* A kétlépéses ellenőrzési kérések minimalizálásához érdemes megfontolnia a feltételes hozzáférés elnevezett helyeinek használatát. Ezzel a funkcióval a rendszergazdák megkerülhetik a kétlépéses ellenőrzést olyan felhasználók számára, akik biztonságos megbízható hálózati helyről jelentkeznek be, például az új felhasználók bevezetéséhez használt hálózati szegmensek.
* [Azure ad Identity Protection](../active-directory-identityprotection.md) üzembe helyezése és a kockázati események alapján történő kétlépéses ellenőrzés indítása.

## <a name="next-steps"></a>További lépések

- [Lépésenkénti Azure multi-Factor Authentication-telepítés](howto-mfa-getstarted.md)

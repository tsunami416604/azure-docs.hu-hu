---
title: Az Azure multi-factor Authentication - működését – Azure Active Directory
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
ms.openlocfilehash: fa25e8a965b89c4e97263e3767a9400079fcad7a
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496806"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Működés: Azure Multi-Factor Authentication

A kétlépéses ellenőrzés biztonságát a rétegelt megközelítést rejlik. A támadók több hitelesítési tényezők veszélyeztetése mutat be komoly nehézségeket jelenthet. Akkor is, ha egy támadó kezeli, és ismerje meg, a felhasználó jelszava, fontos használhatatlan nélkül is rendelkezik a további hitelesítési módszerként. Azzal, hogy a következő hitelesítési módszerek közül kettő vagy több működik:

* Valami tudja (általában jelszót)
* Hiba (megbízható eszközzel rendelkezik, amely nem könnyen lettek duplikálva, például telefon)
* Hiba (biometrikus adatok) áll

<center>

![Fogalmi hitelesítési módszerek kép](./media/concept-mfa-howitworks/methods.png)</center>

Az Azure multi-factor Authentication (MFA) segítségével biztonságosabb a hozzáférés az adatokhoz és alkalmazásokhoz, miközben fenntartja az egyszerűség kedvéért a felhasználók számára. Azzal, hogy egy második hitelesítési mód további biztonságot nyújt, és a könnyen használható számos szigorú hitelesítést biztosít [hitelesítési módszerek](concept-authentication-methods.md). A felhasználók is, vagy előfordulhat, hogy nem merül fel az MFA-konfigurációval kapcsolatos döntéseket a rendszergazda által alapján.

## <a name="how-to-get-multi-factor-authentication"></a>Hogyan lehet lekérni a multi-factor Authentication?

A multi-factor Authentication szolgáltatás része a következő ajánlatokra:

* **Az Azure Active Directory Premium** vagy **Microsoft 365 vállalati** – teljes kiemelt feltételes hozzáférési szabályzatok segítségével a többtényezős hitelesítés kötelezővé tétele az Azure multi-factor Authentication használatát.

* **Az Azure AD ingyenes**, **alapszintű Azure AD**, vagy önálló **Office 365** licencek – használja az előre létrehozott [feltételes hozzáférési alapkonfiguráció alkalmazásvédelmi szabályzatok](../conditional-access/concept-baseline-protection.md) megkövetelése többtényezős hitelesítés a felhasználók és rendszergazdák számára.

* **Az Azure Active Directory globális rendszergazdái** – Azure multi-factor Authentication funkcióinak egy részét, globális rendszergazdai fiókok védelme való érhetők el.

> [!NOTE]
> Új ügyfeleket már nem vásárolhat Azure multi-factor Authentication kínál hatékony 1-től, 2018 szeptember önálló. A multi-factor authentication továbbra is egy elérhető funkciónak az Azure AD Premium-licencet.

## <a name="supportability"></a>Támogatási lehetőségek

Mivel a legtöbb felhasználó vannak bemutatásával csak jelszavak használatával hitelesíteni, fontos, hogy a szervezet kapcsolatban a folyamat minden felhasználó kommunikál. Tájékoztatási valószínűsége, hogy a felhasználók hívja a segélyszolgálatot MFA kisebb kapcsolatban csökkenthető. Vannak azonban néhány olyan forgatókönyvekben, ahol ideiglenesen letilthatja a többtényezős hitelesítés szükséges. Ezek a forgatókönyvek kezelése megértéséhez használja az alábbi irányelveket:

* A támogatási csapat találkozik forgatókönyvek kezeléséhez, ahol a felhasználó nem tud bejelentkezni, mert nem rendelkeznek hozzáféréssel a hitelesítési módszereiket, vagy nem működnek megfelelően betanításához.
   * A feltételes hozzáférési szabályzatokkal az Azure MFA szolgáltatás, a támogatási csapat adhat hozzá egy felhasználó egy csoportot, amely ki van zárva a többtényezős hitelesítés szabályzat.
* Érdemes lehet a feltételes hozzáférés – nevesített helyek, a kétlépéses ellenőrzés minimalizálása érdekében olyan módon kéri. Ezzel a funkcióval a rendszergazdák megkerülhetik a kétlépéses ellenőrzést, a felhasználók számára, amely egy biztonságos, megbízható hálózati helyről, például egy hálózati bejelentkezés használt új felhasználói bevezetésére vonatkozó szegmens.
* Üzembe helyezése [Azure AD Identity Protection](../active-directory-identityprotection.md) , és aktiválja a kétlépéses ellenőrzést, a kockázati események alapján.

## <a name="next-steps"></a>További lépések

- [Részletes Azure multi-factor Authentication üzembe helyezés](howto-mfa-getstarted.md)

---
title: Az Azure MFA működése – Azure Active Directory
description: Az Azure többtényezős hitelesítése segít megőrizni az adatokhoz és alkalmazásokhoz való hozzáférést, miközben kielégíti a felhasználói igényeket egy egyszerű bejelentkezési folyamathoz.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39948214f5bd080be417ed515bea6bff87d3b303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484060"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Az Azure Multi-Factor Authentication működése

A kétlépcsős ellenőrzés biztonsága a réteges megközelítésben rejlik. A több hitelesítési tényező veszélyeztetése jelentős kihívást jelent a támadók számára. Még akkor is, ha a támadó sikerül megtanulni a felhasználó jelszavát, akkor haszontalan anélkül, hogy rendelkezik a további hitelesítési módszer. Úgy működik, hogy két vagy több hitelesítési módszert igényel:

* Valami, amit tud (általában egy jelszó)
* Valami, ami van (egy megbízható eszköz, amely nem könnyen duplikált, mint egy telefon)
* Valami, amit (biometria)

<center>

![Koncepcionális hitelesítési módszerek képe](./media/concept-mfa-howitworks/methods.png)</center>

Az Azure többtényezős hitelesítés (MFA) segít az adatokhoz és alkalmazásokhoz való hozzáférés biztosításában, miközben megőrzi az egyszerűséget a felhasználók számára. További biztonságot nyújt a hitelesítés második formájának megkövetelésével, és erős hitelesítést biztosít számos könnyen használható [hitelesítési módszerrel.](concept-authentication-methods.md) A rendszergazdák által hozott konfigurációs döntések alapján a felhasználók at az MFA-ban nem lehet megtámadni.

## <a name="how-to-get-multi-factor-authentication"></a>A Multi-Factor Authentication beszerzése

A Multi-Factor Authentication a következő ajánlatok részét képezi:

* **Azure Active Directory Premium** vagy **Microsoft 365 Business** – Az Azure többtényezős hitelesítés teljes körű használata feltételes hozzáférési szabályzatok használatával a többtényezős hitelesítés megköveteléséhez.

* **Azure AD Free** vagy önálló **Office 365-licencek** – A [biztonsági alapértékek](../fundamentals/concept-fundamentals-security-defaults.md) használatával többtényezős hitelesítést igényelhet a felhasználók és a rendszergazdák számára.

* **Azure Active Directory globális rendszergazdák** – Az Azure Multi-Factor Authentication funkcióinak egy része elérhető a globális rendszergazdai fiókok védelméhez.

> [!NOTE]
> Az új ügyfelek már nem vásárolhatnak Azure multi-factor authentication-t önálló ajánlatként 2018. A többtényezős hitelesítés továbbra is elérhető funkció marad az Azure AD Premium-licencekben.

## <a name="supportability"></a>Támogatási lehetőségek

Mivel a legtöbb felhasználó megszokta, hogy csak jelszavakat használ a hitelesítéshez, fontos, hogy a szervezet kommunikáljon az összes felhasználóval ezzel a folyamattal kapcsolatban. A tudatosság csökkentheti annak valószínűségét, hogy a felhasználók az MFA-val kapcsolatos kisebb problémák miatt hívják az ügyfélszolgálatot. Vannak azonban olyan forgatókönyvek, ahol ideiglenesen letiltja az MFA-t. Az alábbi irányelvek segítségével megtudhatja, hogyan kezelhetők ezek a forgatókönyvek:

* A támogatási személyzet et úgy tanítsa be, hogy kezelni tudja azokat a forgatókönyveket, amelyekben a felhasználó nem tud bejelentkezni, mert nem férnek hozzá a hitelesítési módszereikhez, vagy nem működnek megfelelően.
   * Feltételes hozzáférés szabályzatok használatával az Azure MFA-szolgáltatás, a támogatási munkatársak hozzáadhat egy felhasználót egy csoporthoz, amely ki van zárva a házirend et igénylő Többfa-szolgáltatás.
* Fontolja meg a feltételes hozzáférés nevű helyek használatát a kétlépéses ellenőrzési kérések minimalizálása érdekében. Ezzel a funkcióval a rendszergazdák megkerülhetik a kétlépéses ellenőrzést azon felhasználók számára, akik biztonságos, megbízható hálózati helyről, például az új felhasználók bevezetéséhez használt hálózati szegmensből jelentkeznek be.
* Telepítse [az Azure AD Identity Protection-t,](../active-directory-identityprotection.md) és indítsa el a kétlépéses ellenőrzést a kockázatészlelések alapján.

## <a name="next-steps"></a>További lépések

- [Az Azure többtényezős hitelesítésének részletes üzembe helyezése](howto-mfa-getstarted.md)

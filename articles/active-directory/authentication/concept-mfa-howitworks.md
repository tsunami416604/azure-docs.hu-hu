---
title: Az Azure multi-factor Authentication – hogyan működik
description: Az Azure Multi-Factor Authentication szolgáltatás révén biztonságosabb a hozzáférés az adatokhoz és az alkalmazásokhoz, és a felhasználók is egyszerűbben jelentkezhetnek be.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: cbe1dc351139645f5516bce79b1792103e90cf08
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430296"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>A működési elv: Azure Multi-Factor Authentication

A kétlépéses ellenőrzés biztonságát a rétegelt megközelítést rejlik. A támadók több hitelesítési tényezők veszélyeztetése mutat be komoly nehézségeket jelenthet. Akkor is, ha egy támadó kezeli, és ismerje meg, a felhasználó jelszava, fontos használhatatlan nélkül is rendelkezik a további hitelesítési módszerként. Azzal, hogy a következő hitelesítési módszerek közül kettő vagy több működik:

* Valami tudja (általában jelszót)
* Hiba (megbízható eszközzel rendelkezik, amely nem könnyen lettek duplikálva, például telefon)
* Hiba (biometrikus adatok) áll

<center>![Fogalmi hitelesítési módszerek kép](./media/concept-mfa-howitworks/methods.png)</center>

Az Azure multi-factor Authentication (MFA) segítségével biztonságosabb a hozzáférés az adatokhoz és alkalmazásokhoz, miközben fenntartja az egyszerűség kedvéért a felhasználók számára. Azzal, hogy egy második hitelesítési mód további biztonságot nyújt, és a könnyen használható számos szigorú hitelesítést biztosít [hitelesítési módszerek](concept-authentication-methods.md). A felhasználók is, vagy előfordulhat, hogy nem merül fel az MFA-konfigurációval kapcsolatos döntéseket a rendszergazda által alapján.

## <a name="how-to-get-multi-factor-authentication"></a>Hogyan lehet lekérni a multi-factor Authentication?

A multi-factor Authentication szolgáltatás része a következő ajánlatokra:

* **Az Azure Active Directory Premium licenccel** – teljes kiemelt Azure multi-factor Authentication szolgáltatás (felhő) vagy az Azure multi-factor Authentication-kiszolgáló (helyszíni) használatát.
   * **Az Azure MFA szolgáltatáshoz (felhő)** - **Ez a beállítás akkor az új üzembe helyezésekhez javasolt elérési**. Az Azure MFA a felhőben nincsenek a helyszíni infrastruktúrát igényel, és az összevont vagy kizárólag felhőbeli felhasználó használható.
   * **Az Azure MFA-kiszolgáló** – Ha a szervezet szeretne a kapcsolódó infrastruktúra-elemek kezelése és az AD FS a helyszíni környezetben telepített ezzel a módszerrel lehet, hogy egy lehetőséget.
* **A multi-factor Authentication for Office 365** – Azure multi-factor Authentication funkcióinak egy részét, az előfizetés részeként érhetők el. Az Office 365-höz MFA kapcsolatos további információkért tekintse meg a cikket [a multi-factor authentication for Office 365 központi telepítések tervezése](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba).
* **Az Azure Active Directory globális rendszergazdái** – Azure multi-factor Authentication funkcióinak egy részét, globális rendszergazdai fiókok védelme való érhetők el.

> [!NOTE]
> Új ügyfeleket már nem vásárolhat Azure multi-factor Authentication kínál hatékony 1-től, 2018 szeptember önálló. A multi-factor authentication továbbra is egy elérhető funkciónak az Azure AD Premium-licencet.

## <a name="supportability"></a>Támogatási lehetőségek

Mivel a legtöbb felhasználó vannak bemutatásával csak jelszavak használatával hitelesíteni, fontos, hogy a szervezet kapcsolatban a folyamat minden felhasználó kommunikál. Tájékoztatási valószínűsége, hogy a felhasználók hívja a segélyszolgálatot MFA kisebb kapcsolatban csökkenthető. Vannak azonban néhány olyan forgatókönyvekben, ahol ideiglenesen letilthatja a többtényezős hitelesítés szükséges. Ezek a forgatókönyvek kezelése megértéséhez használja az alábbi irányelveket:

* A támogatási csapat találkozik forgatókönyvek kezeléséhez, ahol a felhasználó nem tud bejelentkezni, mert nem rendelkeznek hozzáféréssel a hitelesítési módszereiket, vagy nem működnek megfelelően betanításához.
   * A feltételes hozzáférési szabályzatokkal az Azure MFA szolgáltatás, a támogatási csapat adhat hozzá egy felhasználó egy csoportot, amely ki van zárva a többtényezős hitelesítés szabályzat.
   * Támogatási csapattal ideiglenes az egyszeri Mellőzés lehetővé teszi, hogy a kétlépéses ellenőrzés nélkül végezzen hitelesítést az Azure MFA-kiszolgáló felhasználók számára engedélyezheti. A Mellőzés átmeneti, és a megadott számú másodperc után lejár.   
* Érdemes lehet használni a megbízható IP-címek vagy nevesített helyek arra, hogy minimalizálja a kétlépéses ellenőrzés utasításokat. Ezzel a funkcióval a felügyelt vagy összevont bérlők rendszergazdái megkerülhetik a kétlépéses ellenőrzést, a felhasználók számára, amely egy megbízható hálózati helyről, például a szervezeti intraneten bejelentkezés.
* Üzembe helyezése [Azure AD Identity Protection](../active-directory-identityprotection.md) , és aktiválja a kétlépéses ellenőrzést, a kockázati események alapján.

## <a name="next-steps"></a>További lépések

- A részletes MFA beszerzése [központi telepítési csomag](https://aka.ms/MFADeploymentPlan)

- További részletek a [felhasználók licenceléséről](concept-mfa-licensing.md)

- További részletek arról, hogy [melyik verziót telepítse](concept-mfa-whichversion.md)

- Választ kaphat a [gyakori kérdésekre](multi-factor-authentication-faq.md)

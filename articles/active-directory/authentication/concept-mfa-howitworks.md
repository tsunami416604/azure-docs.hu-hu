---
title: Az Azure multi-factor Authentication – hogyan működik
description: Az Azure Multi-Factor Authentication szolgáltatás révén biztonságosabb a hozzáférés az adatokhoz és az alkalmazásokhoz, és a felhasználók is egyszerűbben jelentkezhetnek be.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 46c99011a22f855f6faf53e03169b2d1e4c6ce85
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669007"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Hogyan működik: az Azure multi-factor Authentication

A kétlépéses ellenőrzés biztonságát a rétegelt megközelítést rejlik. A támadók több hitelesítési tényezők veszélyeztetése mutat be komoly nehézségeket jelenthet. Akkor is, ha egy támadó kezeli, és ismerje meg, a felhasználó jelszava, fontos használhatatlan nélkül is rendelkezik a további hitelesítési módszerként. Azzal, hogy a következő hitelesítési módszerek közül kettő vagy több működik:

* Valami tudja (általában jelszót)
* Hiba (megbízható eszközzel rendelkezik, amely nem könnyen lettek duplikálva, például telefon)
* Hiba (biometrikus adatok) áll

<center>![Fogalmi hitelesítési módszerek kép](./media/concept-mfa-howitworks/methods.png)</center>

Az Azure multi-factor Authentication (MFA) segítségével biztonságosabb a hozzáférés az adatokhoz és alkalmazásokhoz, miközben fenntartja az egyszerűség kedvéért a felhasználók számára. Azzal, hogy egy második hitelesítési mód további biztonságot nyújt, és a könnyen használható számos szigorú hitelesítést biztosít [hitelesítési módszerek](concept-authentication-methods.md).

## <a name="how-to-get-multi-factor-authentication"></a>Hogyan lehet lekérni a multi-factor Authentication?

A multi-factor Authentication szolgáltatás része a következő ajánlatokra:

* **Az Azure Active Directory Premium licenccel** – teljes kiemelt Azure multi-factor Authentication szolgáltatás (felhő) vagy az Azure multi-factor Authentication-kiszolgáló (helyszíni) használatát.
   * **Az Azure MFA szolgáltatáshoz (felhő)** - **Ez a beállítás akkor az új üzembe helyezésekhez javasolt elérési**. Az Azure MFA a felhőben nincsenek a helyszíni infrastruktúrát igényel, és az összevont vagy kizárólag felhőbeli felhasználó használható.
   * **Az Azure MFA-kiszolgáló** – Ha a szervezet szeretne a kapcsolódó infrastruktúra-elemek kezelése és az AD FS-előfizetésébe a környezetében telepített ezzel a módszerrel lehet, hogy egy lehetőséget.
* **A multi-factor Authentication for Office 365** – Azure multi-factor Authentication funkcióinak egy részét, az előfizetés részeként érhetők el. Az Office 365-höz MFA kapcsolatos további információkért tekintse meg a cikket [a multi-factor authentication for Office 365 központi telepítések tervezése](https://support.office.com/article/plan-for-multi-factor-authentication-for-office-365-deployments-043807b2-21db-4d5c-b430-c8a6dee0e6ba).
* **Az Azure Active Directory globális rendszergazdái** – Azure multi-factor Authentication funkcióinak egy részét, globális rendszergazdai fiókok védelme való érhetők el.

> [!NOTE]
> Új ügyfeleket már nem vásárolhat Azure multi-factor Authentication kínál hatékony 1-től, 2018 szeptember önálló. A multi-factor authentication továbbra is egy elérhető funkciónak az Azure AD Premium-licencet.

### <a name="auth-provider-or-mfa-license"></a>Hitelesítési szolgáltató vagy az MFA-licenc

Ha rendelkezik Azure AD prémium vagy egy másik, amely tartalmazza az Azure AD Premium licenc-csomagot, már rendelkezik Azure MFA. A szervezet nem kell semmi mást a kétlépéses ellenőrzés képesség kiterjesztését minden felhasználó számára. Csak kell egy licencet hozzárendelni egy felhasználóhoz, és ezután többtényezős hitelesítés bekapcsolása.

Ha nem rendelkezik licencekkel, amelyek tartalmazzák az Azure MFA, vagy nem rendelkezik elegendő licenccel ahhoz, hogy biztosítsák az összes felhasználót, létrehozhat egy [MFA hitelesítési szolgáltatót](concept-mfa-authprovider.md) MFA teljes funkcionalitásának kiterjesztése a felhasználók számára szükség van rájuk.

> [!IMPORTANT]
> Ha minden felhasználó számára nem elegendő licenccel rendelkezik, létrehozhat egy felhasználónkénti multi-factor Auth szolgáltatót, hogy biztosítsák a szervezet többi tagja. Ne hozzon létre egy hitelesítésenkénti multi-factor Auth szolgáltatót. Ha így tesz, akkor sikerült végül ellenőrzési kérések licenccel már rendelkező felhasználóktól.

## <a name="supportability"></a>Támogatási lehetőségek

Mivel a legtöbb felhasználó vannak bemutatásával csak jelszavak használatával hitelesíteni, fontos, hogy a szervezet kapcsolatban a folyamat minden felhasználó kommunikál. Tájékoztatási valószínűsége, hogy a felhasználók hívja a segélyszolgálatot MFA kisebb kapcsolatban csökkenthető. Vannak azonban néhány olyan forgatókönyvekben, ahol ideiglenesen letilthatja a többtényezős hitelesítés szükséges. Ezek a forgatókönyvek kezelése megértéséhez használja az alábbi irányelveket:

* A támogatási csapat találkozik forgatókönyvek kezeléséhez, ahol a felhasználó nem tud bejelentkezni, mert nem rendelkeznek hozzáféréssel a hitelesítési módszereiket, vagy nem működnek megfelelően betanításához.
   * A feltételes hozzáférési szabályzatokkal az Azure MFA szolgáltatás, a támogatási csapat adhat hozzá egy felhasználó egy csoportot, amely ki van zárva a többtényezős hitelesítés szabályzat.
   * Támogatási csapattal ideiglenes az egyszeri Mellőzés lehetővé teszi, hogy a kétlépéses ellenőrzés nélkül végezzen hitelesítést az Azure MFA-kiszolgáló felhasználók számára engedélyezheti. A Mellőzés átmeneti, és a megadott számú másodperc után lejár.
   * A feltételes hozzáférési szabályzatokkal az Azure MFA szolgáltatás a támogatási csapat adhat hozzá egy felhasználót egy csoportot, amely ki van zárva a többtényezős hitelesítés szabályzat.
* Érdemes lehet használni a megbízható IP-címek vagy nevesített helyek arra, hogy minimalizálja a kétlépéses ellenőrzés utasításokat. Ezzel a funkcióval a felügyelt vagy összevont bérlők rendszergazdái megkerülhetik a kétlépéses ellenőrzést, a felhasználók számára, amely egy megbízható hálózati helyről, például a szervezeti intraneten bejelentkezés.
* Üzembe helyezése [Azure AD Identity Protection](../active-directory-identityprotection.md) , és aktiválja a kétlépéses ellenőrzést, a kockázati események alapján.

## <a name="next-steps"></a>További lépések

- A részletes MFA beszerzése [központi telepítési csomag](https://aka.ms/MFADeploymentPlan)

- Keresse meg a részleteket kapcsolatos [licencelése a felhasználók számára](concept-mfa-licensing.md)

- Részletekért [verziót üzembe helyezése](concept-mfa-whichversion.md)

- Választ találhat [– gyakori kérdések](multi-factor-authentication-faq.md)

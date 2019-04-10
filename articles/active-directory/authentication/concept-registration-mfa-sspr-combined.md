---
title: Az Azure AD SSPR és a multi-factor Authentication (előzetes verzió) – az Azure Active Directory eszközregisztrációs kombinált
description: Az Azure AD multi-factor Authentication és az önkiszolgáló jelszó-átállítási regisztrációk (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f3eec1f846f1b74ab3e19bca022d4e009540d1a
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59280009"
---
# <a name="combined-security-information-registration-preview"></a>Egyesített biztonsági információk regisztrációs (előzetes verzió)

Mielőtt kombinált regisztrációs regisztrált felhasználók hitelesítési módszerek az Azure multi-factor Authentication és az önkiszolgáló jelszó-visszaállítás (SSPR) külön-külön. Személyek is összetéveszteni őket, hogy a hasonló módszerek multi-factor Authentication és az SSPR használták, de mindkét funkció regisztrálása rendelkeztek. Most a kombinált regisztrációs felhasználók regisztrálása után, és kihasználni a multi-factor Authentication és az SSPR.

![Saját profil ábrázoló regisztrálva a felhasználó biztonsági adatai](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Mielőtt engedélyezné az új felhasználói felületre, tekintse át a rendszergazda témájú dokumentáció és a felhasználó témájú dokumentációját, hogy biztosan megismerje az a funkciók és a hatását, hogy ez a funkció. A felhasználói dokumentációra készítheti elő a felhasználók az új felhasználói felületre, és annak biztosítása érdekében a sikeres bevezetése érdekében a képzési alapjául.

|     |
| --- |
| Egyesített biztonsági információkat regisztráció a multi-factor Authentication és az Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás az az Azure ad-ben nyilvános előzetes verziójú funkció. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Felhasználók, akik az eredeti előzetes verziójának és a továbbfejlesztett kombinált regisztrációs felületet is engedélyezett az új algoritmus jelenik meg. Felhasználók, akik mindkét élmény engedélyezve vannak az új profil felület jelenik meg. Az új profil igazodik a kombinált regisztrációs megjelenését és működését, és a felhasználók számára zökkenőmentes élményt nyújt. Felhasználók megtekinthetik a saját profil a [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

Saját profil lapok vannak honosított, a számítógép elérése a lap a nyelvi beállítások alapján. A Microsoft legújabb nyelve a böngésző gyorsítótárát, így továbbra is ezt követő kísérletek eléréséhez a lapok jelennek meg a legutóbbi használt nyelv tárolja. Ha törli a gyorsítótár, a lapok újra jelenik meg. Ha egy adott nyelven kényszeríteni kívánja, hozzáadhat `?lng=<language>` az URL-cím végéhez ahol `<language>` megjeleníteni kívánt nyelv kódja.

![SSPR vagy más biztonsági ellenőrzési módszerek beállítása](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>A kombinált regisztrációs módszerek

Kombinált regisztrációs támogatja a következő hitelesítési módszerek és a műveletek:

|   | Regisztráljon | Módosítás | Törlés |
| --- | --- | --- | --- |
| Microsoft Authenticator | Igen (legfeljebb 5) | Nem | Igen |
| Más hitelesítő alkalmazást | Igen (legfeljebb 5) | Nem | Igen |
| Hardvertoken | Nem | Nem | Igen |
| Telefonszám | Igen | Igen | Igen |
| Másodlagos telefon | Igen | Igen | Igen |
| Irodai telefon | Nem | Nem | Nem |
| E-mail | Igen | Igen | Igen |
| Biztonsági kérdések | Igen | Nem | Igen |
| Alkalmazásjelszók | Igen | Nem | Igen |

> [!NOTE]
> Alkalmazásjelszók csak a felhasználók számára, akik kényszerítve lett, a multi-factor Authentication érhetők el. Az alkalmazásjelszavak nem érhetők el a felhasználók számára a multi-factor Authentication egy feltételes hozzáférési szabályzat engedélyezve vannak.

Felhasználók állíthatják be alapértelmezett multi-factor Authentication módszer a következő lehetőségek közül:

- A Microsoft Authenticator – értesítés.
- Hitelesítő alkalmazás vagy a hardveres token – kód.
- Telefonhívás.
- Szöveges üzenet.

Az Azure AD további hitelesítési módszerek hozzáadása folyamatosan történik, azokat a módszereket kombinált regisztrációs elérhető lesz.

## <a name="combined-registration-modes"></a>Egyesített regisztrációs módok

Egyesített regisztrációs két módban: megszakítás és kezelése.

- **Megszakítási mód** varázsló-szerű felület, a felhasználók számára jelennek meg, ha regisztráció vagy bejelentkezés biztonsági adataikat frissítése.

- **Mód kezelése** a felhasználói profil részeként van, és lehetővé teszi a felhasználók saját biztonsági adatok kezeléséhez.

Két mód esetében a felhasználók, akik korábban regisztrált olyan módszer, amely a multi-factor Authentication használható kell a multi-factor Authentication végrehajtása a biztonsági adatok eléréséhez.

### <a name="interrupt-mode"></a>Megszakítási mód

Egyesített regisztrációs figyelembe veszi a multi-factor Authentication és az SSPR-házirendek, ha mindkettő a bérlő számára engedélyezve van. Ezek a szabályzatok az e egy felhasználó megszakad a regisztrációhoz bejelentkezés során, és milyen módszerek érhetők el a regisztrációs szabályozzák.

Az alábbiakban számos forgatókönyv, ahol előfordulhat, hogy a rendszer kérni fogja regisztrálni vagy frissíteni a biztonsági adatok:

* A multi-factor Authentication regisztrációs Identity Protection segítségével kényszeríthető ki: Regisztrálja a bejelentkezés során rendszer kéri a felhasználóktól. (Ha a felhasználó engedélyezve van az SSPR) multi-factor Authentication és az SSPR módszereket regisztrálják.
* A multi-factor Authentication regisztrációs felhasználónkénti multi-factor Authentication segítségével kényszeríthető ki: Regisztrálja a bejelentkezés során rendszer kéri a felhasználóktól. (Ha a felhasználó engedélyezve van az SSPR) multi-factor Authentication és az SSPR módszereket regisztrálják.
* A multi-factor Authentication regisztrációs feltételes hozzáférési vagy egyéb házirendek segítségével kényszeríthető ki: Felhasználók regisztrálása egy erőforrás, amely megköveteli a multi-factor Authentication szolgáltatás használatakor a rendszer felkéri. (Ha a felhasználó engedélyezve van az SSPR) multi-factor Authentication és az SSPR módszereket regisztrálják.
* SSPR regisztrációs kényszerítése: Regisztrálja a bejelentkezés során rendszer kéri a felhasználóktól. Csak az SSPR módszerek regisztrálják.
* SSPR-frissítés kényszerítése: Tekintse át a biztonsági adataikat a rendszergazda által beállított egy intervallumot, a felhasználóknak kell Felhasználók jelennek meg az adatokat, és erősítse meg a jelenlegi adatait vagy szükség esetén végezze el a módosításokat.

Regisztrációs van érvényben, amikor a felhasználók meg kell felelnie a multi-factor Authentication és az SSPR-házirendek a legkevésbé biztonságos leginkább szükséges minimális többféle jelennek meg.

Példa:

* A felhasználó az SSPR engedélyezve van. Az SSPR-szabályzat két módszer alaphelyzetbe állítása szükséges, és engedélyezte a mobilalkalmazás-kód, e-mailben és telefonon.
   * Ez a felhasználó két módszer regisztrálásához szükséges.
      * A felhasználó alapértelmezés szerint authenticator alkalmazás és a telefon látható.
      * A felhasználó kiválaszthatja a hitelesítő alkalmazás vagy a telefon helyett e-mail regisztrálni.

Ez a folyamatábra ismerteti, milyen módszerek regisztrálása a bejelentkezés során egy felhasználónak, amikor megszakad látható:

![Egyesített biztonsági adatai folyamatábrája](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Ha multi-factor Authentication és az SSPR engedélyezve van, azt javasoljuk, hogy a többtényezős hitelesítési regisztráció érvényesítése.

Az SSPR-házirend megköveteli a felhasználóktól tekintse át a biztonsági adatok rendszeres időközönként, ha a felhasználók bejelentkezés során megszakad és a regisztrált módszereket látható. A jelenlegi adatait, ellenőrizheti, ha a naprakész adatok, vagy olyan módosításokat, ha szükségük van.

### <a name="manage-mode"></a>Mód kezelése

A felhasználók mód kezelése a [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) vagy kiválasztásával **biztonsági adatok** a saját profil. Itt felhasználók is módszerek hozzáadása, törlése, vagy módosítsa a meglévő módszerek, módosítsa az alapértelmezett mód és más.

## <a name="key-usage-scenarios"></a>Fő használati forgatókönyvek

### <a name="set-up-security-info-during-sign-in"></a>Biztonsági adatok beállítása a bejelentkezés során

Egy rendszergazda regisztrációs van kényszerítve.

A felhasználó nem állította be az összes szükséges biztonsági adatait, és az Azure Portalra kerül. Miután megadta a felhasználónevét és jelszavát, a rendszer kéri a felhasználótól állítsa be a biztonsági adatok. A felhasználó ezután állíthatja be a szükséges biztonsági adatait a varázslóban látható lépéseket követi. A beállításokban engedélyezve van, ha a felhasználó választhat módszereket, amelyek nem jelenik meg alapértelmezés szerint beállításához. A varázsló befejezése után felhasználók tekintse át a módszereket, állítsa be, és az alapértelmezett módszer a multi-factor Authentication hitelesítéshez. A telepítési folyamatot elvégezni, a felhasználó megerősíti, hogy az adatokat, és továbbra is az Azure Portalon.

### <a name="set-up-security-info-from-my-profile"></a>Saját profil a biztonsági adatok beállítása

Egy rendszergazda nem rendelkezik érvényesíti a regisztráció.

Ugrás a felhasználó, aki még nem beállítása az összes szükséges biztonsági adatok [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). A felhasználó kiválasztja **biztonsági adatok** a bal oldali panelen. Itt a felhasználó úgy dönt, hogy adjon meg egy metódust, kiválasztja az elérhető módszerek bármelyikét és a lépések a módszer beállítása. Amikor végzett, a felhasználó látja a módszert, amelyet csak be lett állítva a biztonsági adatok lapján.

### <a name="delete-security-info-from-my-profile"></a>Biztonsági adat törlése a saját profil

A felhasználók, akik korábban már beállított legalább egy módszert [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). A felhasználó úgy dönt, hogy törli a korábban regisztrált módszerekkel. Amikor végzett, a felhasználó többé nem látja a módszer a biztonsági adatok lapján.

### <a name="change-the-default-method-from-my-profile"></a>Az alapértelmezett módszer módosításához a profil

A felhasználók, akik korábban már beállított legalább egy olyan módszer, amelyet a multi-factor Authentication [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). A felhasználó egy másik alapértelmezett metódus az aktuális alapértelmezett módszer változik. Amikor végzett, a felhasználó látja az új alapértelmezett módszer a biztonsági adatok lapján.

## <a name="next-steps"></a>További lépések

[A bérlő kombinált regisztrációs engedélyezése](howto-registration-mfa-sspr-combined.md)

[A multi-factor Authentication és az SSPR elérhető módszerek](concept-authentication-methods.md)

[Új jelszó önkiszolgáló kérésének konfigurálása](howto-sspr-deployment.md)

[Az Azure Multi-Factor Authentication konfigurálása](howto-mfa-getstarted.md)

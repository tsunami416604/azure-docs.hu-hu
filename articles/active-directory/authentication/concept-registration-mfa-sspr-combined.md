---
title: Az Azure AD SSPR és a többtényezős hitelesítés (előzetes verzió) – az Azure Active Directory kombinált regisztrációs
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
ms.openlocfilehash: 2865c19e747ca1c5b0a6cda84b8be18bfaeb9335
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317663"
---
# <a name="combined-security-information-registration-preview"></a>Egyesített biztonsági információk regisztrációs (előzetes verzió)

Mielőtt kombinált regisztrációs regisztrált felhasználók hitelesítési módszerek az Azure multi-factor Authentication (MFA) és az önkiszolgáló jelszó-visszaállítás (SSPR) két különböző módokon. Személyek is összetéveszteni őket, hogy a hasonló módszerekkel használták az Azure MFA és az SSPR, de külön regisztrálni az egyes szolgáltatásokhoz rendelkeztek. Most, kombinált regisztrációját a felhasználók egyszer regisztrálni és Azure MFA és az SSPR előnyeinek kihasználása.

![Biztonságiadat - felhasználó, például a Microsoft Authenticator és Phone Mintafelhasználó a címtárban regisztrált biztonsági információ megjelenítése a saját profil együtt.](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Mielőtt engedélyezné az új felhasználói felületre, tekintse át a rendszergazda témájú dokumentáció és a felhasználó témájú dokumentációját, hogy biztosan megismerje az a funkciók és lehetséges hatásait, ez a funkció. A felhasználói dokumentációra készítheti elő a felhasználók az új felhasználói felületre, és annak biztosítása érdekében a sikeres bevezetése érdekében a képzési alapjául.

|     |
| --- |
| Egyesített biztonsági információkat regisztráció Azure multi-factor Authentication és az Azure AD önkiszolgáló jelszó-visszaállítás az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

> [!IMPORTANT]
> Ha egy felhasználó az eredeti előzetes verziójának és a továbbfejlesztett kombinált regisztrációs felületet is engedélyezve van, az új felület jelenik meg. Felhasználók, akik mindkét élmény engedélyezve vannak az új profil funkció csak jelenik meg. Az új profil igazodik a kombinált regisztrációs megjelenését és működését, és a felhasználók számára zökkenőmentes élményt nyújt. Felhasználók megtekinthetik a saját profil a [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

A MyProfile lapok honosítva megnyitni a lapot a gép az aktuális nyelvi beállításai alapján. A Microsoft tárolja a legutóbbi nyelv, a böngésző gyorsítótárában használt fel, így el az ezt követő kísérletek továbbra is a legutóbbi használt nyelven jelennek meg. A gyorsítótár kiürítése miatt újra kell renderelnie oldalra. Ha szeretné-e egy adott nyelv hozzáadása kényszerítése egy `?lng=de-DE` az URL-cím végéhez ahol `de-DE` be van állítva a megfelelő nyelvi kód kényszeríti a lapok azon a nyelven jelennek meg.

![Saját profil felület képesség állítsa be az SSPR vagy más további biztonsági ellenőrzési módszerek és a biztonsági adatok megjelenítése.](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>Az összevont regisztrációs módszerek

Jelenleg kombinált regisztrációs támogatja azokat a módszereket a következő módszerek és a műveletek:

|   | Regisztráljon | Módosítás | Törlés |
| --- | --- | --- | --- |
| Microsoft Authenticator | Igen (max. 5) | Nem | Igen |
| Más hitelesítő alkalmazást | Igen (max. 5) | Nem | Igen |
| Hardvertoken | Nem | Nem | Igen |
| Telefonszám | Igen | Igen | Igen |
| Másodlagos telefon | Igen | Igen | Igen |
| Irodai telefon | Nem | Nem | Nem |
| E-mail | Igen | Igen | Igen |
| Biztonsági kérdések | Igen | Nem | Igen |
| Alkalmazásjelszók | Igen | Nem | Igen |

> [!NOTE]
> Alkalmazásjelszavak azok kényszerítve lett, a multi-factor Authentication a felhasználók csak érhetők el. Az alkalmazásjelszavak nem érhetők el a felhasználók számára a multi-factor Authentication egy feltételes hozzáférési szabályzat engedélyezve vannak.

Felhasználók a következő beállítások adhatók meg alapértelmezett módszerként az MFA-hoz:

- A Microsoft Authenticator – értesítés
- Hitelesítő alkalmazás vagy a hardveres token – kód
- Telefonhívás
- Szöveges üzenet

Az Azure AD további hitelesítési módszerek ilyen hozzáadása folyamatosan történik, azokat a módszereket kombinált regisztrációs elérhető lesz.

## <a name="combined-registration-modes"></a>Egyesített regisztrációs módok

Két "módban" kombinált regisztrációs: megszakítás és kezelése.

Megszakítási mód, a varázsló-szerű felület, egy felhasználó számára látható, ha regisztrálni vagy frissíteni a biztonsági adataikat bejelentkezéskor.

Kezelése mód része a felhasználó profilját, és lehetővé teszi, hogy a biztonsági adatok kezeléséhez.

Két mód esetében a felhasználó korábban már regisztrálva van egy módszer, amelyet a multi-factor Authentication, ha azok kell hajthatok végre MFA saját biztonsági adatok eléréséhez.

### <a name="interrupt-mode"></a>Megszakítási mód

Egyesített regisztrációs MFA és az SSPR-házirendek, figyelembe veszi, ha mindkettő a bérlő számára engedélyezve van. Ezek a szabályzatok határozzák meg, e felhasználó regisztrálja a bejelentkezés során megszakad, és milyen módszerek érhetők el regisztrálni.

Az alábbi listában számos forgatókönyv, ahol a felhasználó kérheti, hogy regisztrálja, vagy frissítse a biztonsági adatok:

* MFA-regisztráció Identity Protection segítségével kényszeríthető ki: Felhasználói bejelentkezés során regisztrálni kell adnia. (Ha a felhasználó engedélyezve van az SSPR) MFA és az SSPR módszereket regisztrálják.
* MFA-regisztráció felhasználónkénti MFA segítségével kényszeríthető ki: Felhasználói bejelentkezés során regisztrálni kell adnia. (Ha a felhasználó engedélyezve van az SSPR) MFA és az SSPR módszereket regisztrálják.
* MFA-regisztráció feltételes hozzáférési vagy egyéb házirendek segítségével kényszeríthető ki: Regisztrálja a többtényezős Hitelesítést követel meg erőforrás elérésekor rendszer kéri a felhasználóktól. Felhasználó regisztrálja az MFA és az SSPR módszereket (Ha a felhasználó engedélyezve van az SSPR).
* SSPR regisztrációs kényszerítése: Regisztrálja a bejelentkezés során rendszer kéri a felhasználóktól. Csak az SSPR módszerek regisztrálása
* SSPR-frissítés kényszerítése: Tekintse át a biztonsági adataikat a rendszergazda által beállított egy intervallumot, a felhasználóknak kell Felhasználók jelennek meg az adatokat, és válassza a "Jól" vagy szükség esetén végezze el a módosításokat.

Ha kényszerítve van az eszközregisztrációs felhasználók a legkevésbé biztonságos meg kell felelnie az MFA és az SSPR-házirendek a leginkább szükséges minimális többféle jelennek meg.

Példa:

* A felhasználó az SSPR engedélyezve van. Az SSPR-szabályzat két módszer alaphelyzetbe állítása szükséges, és engedélyezte a mobilalkalmazás-kód, e-mailben és telefonon.
   * Ez a felhasználó két módszer regisztrálásához szükséges.
      * Ezek még látható authenticator alkalmazás és a telefon alapértelmezés szerint.
      * A felhasználó kiválaszthatja a hitelesítő alkalmazás vagy a telefon helyett e-mail regisztrálni.

A következő folyamatábra szemlélteti azt ismerteti, milyen módszerek regisztrálása a bejelentkezés során egy felhasználónak, amikor megszakad látható:

![Mostantól a biztonsági adatok folyamatábra elmagyarázza, ha további információra szükség, amikor bejelentkezik szükséges módszerek száma. Ezt módosíthatja, ha csak az MFA vagy csak az SSPR megadása kötelező](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

MFA és az SSPR engedélyezve van, azt javasoljuk, hogy az MFA regisztrációs kényszeríti.

Az SSPR-házirend megköveteli a felhasználóktól tekintse át a biztonsági adatok rendszeres időközönként, ha a felhasználók bejelentkezés során megszakad és a regisztrált módszereket látható. Ha az adatok naprakész vagy a "Szerkesztés info" kiválaszthatnak kiválaszthatnak "Jól" módosításokat.

### <a name="manage-mode"></a>Mód kezelése

A felhasználók a mód kezelése [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) vagy a saját profil "Biztonsági adatok" parancsával. Itt felhasználók is módszerek hozzáadása, törlése, vagy módosítsa a meglévő módszerek, módosítsa az alapértelmezett módszer és egyéb.

## <a name="key-usage-scenarios"></a>Fő használati forgatókönyvek

### <a name="set-up-security-info-during-sign-in"></a>Biztonsági adatok beállítása a bejelentkezés során

Egy rendszergazda regisztrációs van kényszerítve.

A felhasználó nem állította be az összes szükséges biztonsági adatait, és lép az Azure Portalon. Felhasználónév és jelszó megadása után a rendszer kéri a felhasználótól állítsa be a biztonsági adatokat. A felhasználó ezután állíthatja be a szükséges biztonsági adatait a varázslóban látható lépéseket követi. A felhasználó választhat a mi jelenik meg alapértelmezés szerint ha a beállítások lehetővé teszik a lépéseire beállításához. A felhasználó a multi-factor Authentication a varázsló végén áttekinti a módszereket, állítsa be, és az alapértelmezett módszer. A telepítési folyamatot elvégezni, a felhasználó megerősíti, hogy az adatokat, és továbbra is az Azure Portalon.

### <a name="set-up-security-info-from-my-profile"></a>Saját profil a biztonsági adatok beállítása

Egy rendszergazda nem rendelkezik érvényesíti a regisztráció.

A felhasználók, akik még nem állította be az összes szükséges biztonsági adatok [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). A felhasználó ezután választ **biztonsági adatok** a bal oldali navigációs sávon. Itt a felhasználó úgy dönt, hogy adjon meg egy metódust, kiválasztja a rendelkezésükre álló módszerekkel és a lépések a módszer beállítása. Amikor végzett, a felhasználó látja a módszer, azok csak állítsa be, a biztonsági adatok weblapján.

### <a name="delete-security-info-from-my-profile"></a>Biztonsági adat törlése a saját profil

A felhasználók, akik korábban már beállított legalább egy módszert [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). A felhasználó úgy dönt, hogy törli a korábban regisztrált módszerekkel. Amikor végzett, a felhasználó többé nem látja a módszer a biztonsági adatok lapján.

### <a name="change-default-method-from-my-profile"></a>Alapértelmezett módszer módosításához a profil

A felhasználók, akik korábban már beállított legalább egy olyan módszer, amelyet a multi-factor Authentication [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). A felhasználó egy másik alapértelmezett metódus az aktuális alapértelmezett módszer változik. Amikor végzett, a felhasználó látja az új alapértelmezett módszer a biztonsági adatok lapján.

## <a name="next-steps"></a>További lépések

[A bérlő kombinált regisztrációs engedélyezése](howto-registration-mfa-sspr-combined.md)

[A többtényezős hitelesítés és az SSPR elérhető módszerek](concept-authentication-methods.md)

[Új jelszó önkiszolgáló kérésének konfigurálása](howto-sspr-deployment.md)

[Az Azure multi-factor Authentication konfigurálása](howto-mfa-getstarted.md)

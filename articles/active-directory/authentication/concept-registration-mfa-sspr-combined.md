---
title: A SSPR és az MFA együttes regisztrálása – Azure Active Directory
description: Az Azure AD Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási regisztráció (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b484acc0dc1a92a857f254ed37392ffb29eddb8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848629"
---
# <a name="combined-security-information-registration-preview"></a>Kombinált biztonsági információk regisztrálása (előzetes verzió)

A kombinált regisztráció előtt a felhasználók az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás (SSPR) hitelesítési módszereit külön-külön regisztrálták. Az emberek zavarosak voltak, hogy hasonló módszerek voltak használatban Multi-Factor Authentication és SSPR, de mindkét szolgáltatáshoz regisztrálniuk kellett őket. A kombinált regisztráció révén a felhasználók egyszer regisztrálhatnak, és a Multi-Factor Authentication és a SSPR előnyeit is igénybe vehetik.

![Saját profil, amely egy felhasználó regisztrált biztonsági adatait jeleníti meg](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Az új felület engedélyezése előtt tekintse át ezt a rendszergazda által irányított dokumentációt és a felhasználó által irányított dokumentációt, hogy megértse a funkció funkcióit és hatását. A [felhasználói dokumentáció](../user-help/user-help-security-info-overview.md) alapján felkészítheti a felhasználókat az új élményre, és segítheti a sikeres bevezetést.

Az Azure AD kombinált biztonsági információinak regisztrációja jelenleg nem érhető el olyan nemzeti felhők számára, mint az Azure US government, az Azure Germany vagy az Azure China 21Vianet.

|     |
| --- |
| A Multi-Factor Authentication és Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítási szolgáltatásának együttes biztonsági információinak regisztrálása az Azure AD nyilvános előzetes verziója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Azok a felhasználók, akik számára engedélyezve van az eredeti előzetes verzió és a bővített kombinált regisztrációs élmény, az új viselkedést fogják látni. Azok a felhasználók, akik mindkét élmény esetében engedélyezve vannak, csak az új saját profilt fogják látni. Az új saját profil a közös regisztráció megjelenésével és működésével igazodik, és zökkenőmentes felhasználói élményt nyújt a felhasználóknak. A felhasználók a [https://myprofile.microsoft.com](https://myprofile.microsoft.com)címen tekinthetik meg a profilt.

> [!NOTE] 
> Előfordulhat, hogy hibaüzenet jelenik meg, amikor megpróbál hozzáférni a biztonsági adatok lehetőséghez. Például: "sajnos nem tudjuk bejelentkezni". Ebben az esetben győződjön meg arról, hogy nincs olyan konfigurációs vagy csoportházirend-objektuma, amely blokkolja a külső felek cookie-jait a böngészőben. 

A saját profil oldalai honosítva vannak az oldalt elérő számítógép nyelvi beállításai alapján. A Microsoft a böngésző gyorsítótárában használt legújabb nyelvet tárolja, így a lapok elérésére irányuló próbálkozások továbbra is az utolsó használt nyelven jelennek meg. Ha törli a gyorsítótárat, az oldalak újra megjelennek. Ha egy adott nyelvet szeretne kényszeríteni, `?lng=<language>` adhat hozzá az URL végéhez, ahol `<language>` a megjeleníteni kívánt nyelv kódja.

![SSPR vagy egyéb biztonsági ellenőrzési módszerek beállítása](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>A kombinált regisztrációban elérhető módszerek

A kombinált regisztráció a következő hitelesítési módszereket és műveleteket támogatja:

|   | Regisztráció | Módosítás | Törlés |
| --- | --- | --- | --- |
| Microsoft Authenticator | Igen (legfeljebb 5) | Nem | Igen |
| Egyéb hitelesítő alkalmazás | Igen (legfeljebb 5) | Nem | Igen |
| Hardver jogkivonata | Nem | Nem | Igen |
| Telefonszám | Igen | Igen | Igen |
| Másik telefon | Igen | Igen | Igen |
| Irodai telefon | Nem | Nem | Nem |
| E-mail cím | Igen | Igen | Igen |
| Biztonsági kérdések | Igen | Nem | Igen |
| Alkalmazásjelszók | Igen | Nem | Igen |

> [!NOTE]
> Az alkalmazás jelszavai csak az Multi-Factor Authentication számára kikényszerített felhasználók számára érhetők el. Az alkalmazás jelszavai nem érhetők el azokhoz a felhasználókhoz, akik számára engedélyezett a Multi-Factor Authentication egy feltételes hozzáférési szabályzaton keresztül.

A felhasználók a következő beállítások egyikét állíthatják be alapértelmezett Multi-Factor Authentication metódusként:

- Microsoft Authenticator – értesítés.
- Hitelesítő alkalmazás vagy hardver-jogkivonat – kód.
- Telefonhívás.
- Szöveges üzenet.

Mivel továbbra is további hitelesítési módszereket adunk az Azure AD-hez, ezek a módszerek a kombinált regisztrációban lesznek elérhetők.

## <a name="combined-registration-modes"></a>Kombinált regisztrációs módok

A kombinált regisztrációnak két módja van: megszakítás és felügyelet.

- A **megszakítási mód** egy varázsló-szerű élmény, amely a felhasználók számára jelenik meg, amikor regisztrálnak vagy frissítenek biztonsági adataikat a bejelentkezéskor.

- A **kezelési mód** a felhasználói profil része, és lehetővé teszi a felhasználók számára a biztonsági adatok kezelését.

Mindkét mód esetében a felhasználók, akik korábban regisztráltak a Multi-Factor Authenticationhoz használható metódust, Multi-Factor Authentication kell végrehajtaniuk, mielőtt hozzá tudnak férni a biztonsági adataihoz.

### <a name="interrupt-mode"></a>Megszakítási mód

A kombinált regisztráció a Multi-Factor Authentication és a SSPR szabályzatot egyaránt figyelembe veszi, ha mindkettő engedélyezve van a bérlő számára. Ezek a szabályzatok határozzák meg, hogy a rendszer megszakítja-e a felhasználók regisztrációját a bejelentkezés során, és hogy mely módszerek érhetők el a regisztrációhoz.

Az alábbiakban néhány olyan forgatókönyv látható, amelyben a felhasználók a biztonsági adatok regisztrálására vagy frissítésére kérhetnek:

- Multi-Factor Authentication regisztráció a személyazonosság védelme révén kényszerítve: a rendszer a felhasználókat a bejelentkezés során kéri. Regisztrálják Multi-Factor Authentication metódusokat és SSPR metódusokat (ha a felhasználó engedélyezve van a SSPR).
- Multi-Factor Authentication regisztráció a felhasználónkénti Multi-Factor Authentication használatával: a rendszer a felhasználókat a bejelentkezéskor kéri. Regisztrálják Multi-Factor Authentication metódusokat és SSPR metódusokat (ha a felhasználó engedélyezve van a SSPR).
- Multi-Factor Authentication a regisztrációt a feltételes hozzáférés vagy más szabályzatok alapján kényszerítjük: a felhasználóknak regisztrálniuk kell, amikor olyan erőforrást használnak, amelyhez Multi-Factor Authentication szükséges. Regisztrálják Multi-Factor Authentication metódusokat és SSPR metódusokat (ha a felhasználó engedélyezve van a SSPR).
- SSPR-regisztráció érvénybe léptetése: a rendszer a felhasználóknak a bejelentkezés során kéri a regisztrálást. Csak SSPR metódusokat regisztráljanak.
- SSPR-frissítés kényszerítve: a felhasználóknak a rendszergazda által beállított időközönként kell áttekinteniük biztonsági adataikat. A felhasználók megtekinthetik az adataikat, és szükség esetén ellenőrizhetik a jelenlegi adatokat, vagy módosíthatják azokat.

A regisztráció érvénybe léptetése esetén a felhasználók a lehető legkevesebb módszert mutatják be a Multi-Factor Authentication és a SSPR szabályzatoknak való megfeleléshez.

Példa:

- A SSPR engedélyezve van egy felhasználó. A SSPR házirendben két módszer szükséges az alaphelyzetbe állításhoz, és engedélyezte a Mobile App Code, az e-mail és a Phone használatát.
   - Ez a felhasználó két módszer regisztrálásához szükséges.
      - A felhasználó alapértelmezés szerint a hitelesítő alkalmazás és a telefon beállítást jeleníti meg.
      - A felhasználó megadhatja, hogy a hitelesítő alkalmazás vagy a telefon helyett e-maileket regisztráljon.

Ez a folyamatábra azt ismerteti, hogy mely metódusok jelennek meg a felhasználó számára a bejelentkezés során megszakított regisztráció során:

![Kombinált biztonsági adatok folyamatábrája](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Ha a Multi-Factor Authentication és a SSPR is engedélyezve van, javasoljuk, hogy érvényesítse Multi-Factor Authentication regisztrációját.

Ha a SSPR-szabályzat megköveteli, hogy a felhasználók rendszeres időközönként vizsgálják felül a biztonsági adataikat, a rendszer a bejelentkezés során megszakítja a felhasználókat, és megjeleníti az összes regisztrált módszerét. Megerősítik az aktuális információt, ha naprakészek, vagy ha szükséges, módosításokat végezhetnek.

### <a name="manage-mode"></a>Kezelés módja

A felhasználók a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) vagy a saját profil **biztonsági adatainak** kiválasztásával érhetik el a kezelési módot. A felhasználók hozzáadhatnak metódusokat, törölhetik vagy módosíthatják a meglévő metódusokat, módosíthatják az alapértelmezett metódust és egyebeket.

## <a name="key-usage-scenarios"></a>Kulcshasználat forgatókönyvek

### <a name="set-up-security-info-during-sign-in"></a>Biztonsági adatok beállítása a bejelentkezés során

A rendszergazda kényszerített regisztrációt.

Egy felhasználó nem állította be az összes szükséges biztonsági adatot, és a Azure Portalra kerül. A Felhasználónév és a jelszó megadása után a rendszer felszólítja a felhasználót a biztonsági adatok beállítására. A felhasználó ezután a varázslóban látható lépéseket követve beállíthatja a szükséges biztonsági adatokat. Ha a beállítások lehetővé teszik, a felhasználó dönthet úgy, hogy az alapértelmezés szerint nem látható módszereket is beállít. A varázsló befejezése után a felhasználók áttekintik az általuk beállított metódusokat és a Multi-Factor Authentication alapértelmezett módszerét. A telepítési folyamat befejezéséhez a felhasználó megerősíti az adatokat, és folytatja a Azure Portal.

### <a name="set-up-security-info-from-my-profile"></a>Biztonsági információk beállítása a saját profilból

Egy rendszergazda nem kényszerített regisztrációt.

Egy olyan felhasználó, aki még nem állította be az összes szükséges biztonsági adatot, [https://myprofile.microsoft.com](https://myprofile.microsoft.com). A felhasználó a bal oldali panelen kiválasztja a **biztonsági adatokat** . Onnan a felhasználó úgy dönt, hogy hozzáad egy metódust, kiválasztja az elérhető módszerek bármelyikét, és az adott metódus beállításának lépéseit követi. Ha elkészült, a felhasználó az imént beállított módszert látja el a biztonsági adatok lapon.

### <a name="delete-security-info-from-my-profile"></a>Biztonsági adatok törlése a saját profilból

Az a felhasználó, aki korábban beállított legalább egy metódust, navigál [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). A felhasználó úgy dönt, hogy törli a korábban regisztrált módszerek egyikét. Ha elkészült, a felhasználó már nem látja ezt a metódust a biztonsági adatok lapon.

### <a name="change-the-default-method-from-my-profile"></a>A saját profil alapértelmezett módszerének módosítása

Az a felhasználó, aki korábban beállított legalább egy olyan metódust, amelyet Multi-Factor Authentication használhat a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). A felhasználó az aktuális alapértelmezett metódust egy másik alapértelmezett metódusra módosítja. Ha elkészült, a felhasználó az új alapértelmezett metódust látja a biztonsági adatok lapon.

## <a name="next-steps"></a>Következő lépések

[A felhasználók számára a hitelesítési módszerek ismételt regisztrálásának kényszerítése](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Kombinált regisztráció engedélyezése a bérlőben](howto-registration-mfa-sspr-combined.md)

[SSPR és MFA-használati és-bejelentési jelentések](howto-authentication-methods-usage-insights.md)

[A Multi-Factor Authentication és a SSPR elérhető módszerei](concept-authentication-methods.md)

[Önkiszolgáló jelszó-visszaállítás konfigurálása](howto-sspr-deployment.md)

[Az Azure Multi-Factor Authentication konfigurálása](howto-mfa-getstarted.md)

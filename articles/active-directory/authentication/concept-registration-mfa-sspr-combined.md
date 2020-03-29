---
title: Kombinált regisztráció az SSPR-hez és az MFA-hoz – Azure Active Directory
description: Azure AD többtényezős hitelesítés és önkiszolgáló jelszó-visszaállításregisztráció (előzetes verzió)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cb5aca128679b21072a2a3daa503dc43a8e2885
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942897"
---
# <a name="combined-security-information-registration-preview"></a>Kombinált biztonsági adatok regisztrálása (előzetes verzió)

A kombinált regisztráció előtt a felhasználók külön-külön regisztrálták az Azure többtényezős hitelesítésés az önkiszolgáló jelszó-visszaállítás (SSPR) hitelesítési módszereit. Az emberek össze voltak zavarodva, hogy hasonló módszereket használtak a többtényezős hitelesítéshez és az SSPR-hez, de mindkét funkcióra regisztrálniuk kellett. Most, kombinált regisztráció, a felhasználók egyszer regisztrálhatnak, és kap az előnyeit mind a többtényezős hitelesítés és SSPR.

![Saját profil, amely regisztrált biztonsági adatokat jelenít meg egy felhasználó számára](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Az új felület engedélyezése előtt tekintse át ezt a rendszergazdaközpontú dokumentációt és a felhasználóközpontú dokumentációt, hogy biztosan tisztában legyen a szolgáltatás működésével és hatásával. A betanítást a [felhasználói dokumentációra](../user-help/user-help-security-info-overview.md) alapozva készítse fel a felhasználókat az új élményre, és segítsen biztosítani a sikeres bevezetést.

Az Azure AD kombinált biztonsági adatok regisztrációja jelenleg nem érhető el a nemzeti felhők, például az Azure US Government, az Azure Germany vagy az Azure China 21Vianet számára.

|     |
| --- |
| A többtényezős hitelesítés és az Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás kombinált biztonsági adatok regisztrálása az Azure AD nyilvános előzetes verziója. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Azok a felhasználók, akik engedélyezve vannak mind az eredeti előzetes verzióhoz, mind a továbbfejlesztett kombinált regisztrációs élményhez, az új viselkedést fogják látni. Azok a felhasználók, akik mindkét élményben engedélyezve vannak, csak az új Saját profil élményt látják. Az új Saját profil igazodik a kombinált regisztráció megjelenéséhez, és zökkenőmentes élményt nyújt a felhasználók számára. A felhasználók a profilomat [https://myprofile.microsoft.com](https://myprofile.microsoft.com)a segítségével láthatják.

> [!NOTE] 
> A Biztonsági adatok beállítás elérése közben hibaüzenet jelenhet meg. Például: "Sajnáljuk, nem tudjuk bejelentkezni". Ebben az esetben ellenőrizze, hogy nincs-e olyan konfigurációs vagy csoportházirend-objektum, amely letiltja a harmadik féltől származó cookie-kat a webböngészőben. 

A Saját profil lapok a lapot elérő számítógép nyelvi beállításai alapján honosodnak. A Microsoft a böngésző gyorsítótárában tárolja a legutóbb használt nyelvet, így a lapok elérésére tett későbbi kísérletek továbbra is az utoljára használt nyelven jelennek meg. Ha törli a gyorsítótárat, az oldalak újra renderelésre kerülnek. Ha egy adott nyelvet szeretne kényszeríteni, `?lng=<language>` hozzáadhatja az URL-cím végéhez, ahol `<language>` a megjeleníteni kívánt nyelv kódja.

![SSPR vagy más biztonsági ellenőrzési módszerek beállítása](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>A kombinált nyilvántartásba vétel során rendelkezésre álló módszerek

A kombinált regisztráció a következő hitelesítési módszereket és műveleteket támogatja:

|   | Regisztráljon | Módosítás | Törlés |
| --- | --- | --- | --- |
| Microsoft Authenticator | Igen (maximum 5) | Nem | Igen |
| Egyéb hitelesítő alkalmazás | Igen (maximum 5) | Nem | Igen |
| Hardvertoken | Nem | Nem | Igen |
| Telefon | Igen | Igen | Igen |
| Alternatív telefon | Igen | Igen | Igen |
| Munkahelyi telefon | Nem | Nem | Nem |
| E-mail | Igen | Igen | Igen |
| Biztonsági kérdések | Igen | Nem | Igen |
| Alkalmazásjelszavak. | Igen | Nem | Igen |
| FIDO2 biztonsági kulcsok<br />*Felügyelt mód csak a [Biztonsági adatok](https://mysignins.microsoft.com/security-info) lapon*| Igen | Igen | Igen |

> [!NOTE]
> Az alkalmazásjelszavak csak a többtényezős hitelesítéshez kényszerített felhasználók számára érhetők el. Az alkalmazásjelszavak nem érhetők el azon felhasználók számára, akik feltételes hozzáférési szabályzaton keresztül engedélyezve vannak a többtényezős hitelesítéshez.

A felhasználók az alábbi beállítások egyikét állíthatják be alapértelmezett többtényezős hitelesítési módszerként:

- Microsoft Hitelesítő – értesítés.
- Hitelesítő alkalmazás vagy hardvertoken – kód.
- Egy telefonhívás.
- Szöveges üzenet.

Ahogy továbbra is további hitelesítési módszereket adunk hozzá az Azure AD-hez, ezek a módszerek elérhetők lesznek a kombinált regisztrációban.

## <a name="combined-registration-modes"></a>Kombinált regisztrációs módok

A kombinált regisztrációnak két módja van: a megszakítás és a kezelés.

- **A megszakítási mód** varázslószerű élmény, amelyet a felhasználók akkor mutatnak be, amikor regisztrálják vagy frissítik biztonsági adataikat a bejelentkezéskor.

- **A Kezelési mód** a felhasználói profil része, és lehetővé teszi a felhasználók számára biztonsági adataik kezelését.

Mindkét mód esetében azoknak a felhasználóknak, akik korábban regisztráltak egy módszert, amely használható a többtényezős hitelesítéshez, többtényezős hitelesítést kell végrehajtaniuk, mielőtt hozzáférhetnének a biztonsági adataikhoz.

### <a name="interrupt-mode"></a>Megszakítási mód

A kombinált regisztráció mind a többtényezős hitelesítést, mind az SSPR-házirendeket tiszteletben tartja, ha mindkettő engedélyezve van a bérlő számára. Ezek a házirendek szabályozzák, hogy a felhasználó megszakítsa-e a regisztrációt a bejelentkezés során, és hogy mely módszerek érhetők el a regisztrációhoz.

Az alábbi esetekben a rendszer kérheti a felhasználókat a biztonsági adataik regisztrálására vagy frissítésére:

- Többtényezős hitelesítés regisztráció identitásvédelem: A felhasználók nak regisztrálnia kell a bejelentkezés során. Többtényezős hitelesítési módszereket és SSPR-metódusokat regisztrálnak (ha a felhasználó engedélyezve van az SSPR-hez).
- Többtényezős hitelesítés regisztráció kényszerítve felhasználónkénti többtényezős hitelesítés: A felhasználók nak regisztrálnia kell a bejelentkezés során. Többtényezős hitelesítési módszereket és SSPR-metódusokat regisztrálnak (ha a felhasználó engedélyezve van az SSPR-hez).
- Feltételes hozzáféréssel vagy más házirendekkel kényszerített többtényezős hitelesítésregisztráció: A felhasználóknak regisztrálniuk kell, ha többtényezős hitelesítést igénylő erőforrást használnak. Többtényezős hitelesítési módszereket és SSPR-metódusokat regisztrálnak (ha a felhasználó engedélyezve van az SSPR-hez).
- SSPR-regisztráció kényszerítve: A felhasználóknak regisztrálniuk kell a bejelentkezés során. Csak SSPR módszereket regisztrálnak.
- SSPR-frissítés kényszerítve: A felhasználóknak a rendszergazda által beállított időközönként át kell tekinteniük a biztonsági adataikat. A felhasználók megjelennek az adataik, és megerősíthetik az aktuális adatokat, vagy szükség esetén módosíthatják őket.

A regisztráció kényszerítésekén él a felhasználók a többtényezős hitelesítéshez és az SSPR-házirendeknek való megfeleléshez szükséges minimális számú módszer, a legtöbbtől a legkevésbé biztonságosig.

Példa:

- Egy felhasználó engedélyezve van az SSPR-hez. Az SSPR-házirend két módszert igényelt az alaphelyzetbe állításhoz, és engedélyezte a mobilalkalmazás-kódot, az e-mailt és a telefont.
   - Ennek a felhasználónak két módszert kell regisztrálnia.
      - A felhasználó alapértelmezés szerint a hitelesítő alkalmazás és a telefon jelenik meg.
      - A felhasználó választhat, hogy regisztrálja e-mail helyett hitelesítő app vagy telefon.

Ez a folyamatábra azt ismerteti, hogy a bejelentkezés során a regisztráció megszakításakor mely módszerek jelennek meg a felhasználó számára:

![Kombinált biztonsági adatok folyamatábrája](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Ha a többtényezős hitelesítés és az SSPR is engedélyezve van, javasoljuk, hogy kényszerítse a többtényezős hitelesítés regisztrációját.

Ha az SSPR-házirend megköveteli a felhasználóktól, hogy rendszeres időközönként áttekintsék a biztonsági adataikat, a felhasználók megszakadnak a bejelentkezés során, és minden regisztrált módszert megjelenítenek. Megerősíthetik az aktuális adatokat, ha azok naprakészek, vagy szükség esetén módosíthatják. A felhasználóknak többtényezős hitelesítést kell végrehajtaniuk a lap elérésekor.

### <a name="manage-mode"></a>Kezelési mód

A felhasználók a Profilom [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) lapon a **Biztonsági adatok** kiválasztásával érhetik el a kezelési módot. Innen a felhasználók metódusokat adhatnak hozzá, törölhetik vagy módosíthatják a meglévő metódusokat, módosíthatják az alapértelmezett módszert stb.

## <a name="key-usage-scenarios"></a>Kulcshasználati forgatókönyvek

### <a name="set-up-security-info-during-sign-in"></a>Biztonsági adatok beállítása bejelentkezés közben

Egy rendszergazda kényszerített regisztráció.

A felhasználó nem állította be az összes szükséges biztonsági adatokat, és megy az Azure Portalon. A felhasználónév és a jelszó megadása után a rendszer kéri a felhasználótól a biztonsági adatok beállítását. A felhasználó ezután a varázslóban látható lépéseket követve állítsa be a szükséges biztonsági adatokat. Ha a beállítások lehetővé teszik, a felhasználó az alapértelmezés szerint nem alkalmazott módszereket állíthat be. A varázsló befejezése után a felhasználók áttekintik a beállított módszereket és a többtényezős hitelesítés alapértelmezett módszerét. A beállítási folyamat befejezéséhez a felhasználó megerősíti az adatokat, és folytatja az Azure Portalon.

### <a name="set-up-security-info-from-my-profile"></a>Biztonsági adatok beállítása a Saját profil ból

A rendszergazda nem kényszerített regisztrációt.

Az a felhasználó, aki még nem állította [https://myprofile.microsoft.com](https://myprofile.microsoft.com)be az összes szükséges biztonsági adatot, a . A felhasználó a bal oldali ablaktáblában kiválasztja a **biztonsági adatokat.** Innen a felhasználó úgy dönt, hogy hozzáad egy metódust, kiválasztja a rendelkezésre álló módszerek bármelyikét, és követi a módszer beállításának lépéseit. Amikor elkészült, a felhasználó látja a biztonsági adatok lapon beállított metódust.

### <a name="delete-security-info-from-my-profile"></a>Biztonsági adatok törlése a Saját profilból

Az a felhasználó, aki korábban már [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)beállított legalább egy metódust, a rendszerre navigál. A felhasználó úgy dönt, hogy törli az egyik korábban regisztrált módszert. Ha végzett, a felhasználó már nem látja ezt a metódust a Biztonsági adatok lapon.

### <a name="change-the-default-method-from-my-profile"></a>Az alapértelmezett módszer módosítása a Saját profil ból

Az a felhasználó, aki korábban már beállított legalább egy módszert, [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)amely használható a többtényezős hitelesítéshez, a rendszerre navigál. A felhasználó az aktuális alapértelmezett módszert egy másik alapértelmezett módszerre módosítja. Ha végzett, a felhasználó látja az új alapértelmezett módszert a Biztonsági adatok lapon.

## <a name="next-steps"></a>További lépések

[A felhasználók kényszerítése a hitelesítési módszerek újbóli regisztrálására](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Kombinált regisztráció engedélyezése a bérlőben](howto-registration-mfa-sspr-combined.md)

[SSPR és MFA-használat és elemzési jelentések](howto-authentication-methods-usage-insights.md)

[A többtényezős hitelesítéshez és az SSPR-hez rendelkezésre álló módszerek](concept-authentication-methods.md)

[Új jelszó önkiszolgáló kérésének konfigurálása](howto-sspr-deployment.md)

[Az Azure Multi-Factor Authentication konfigurálása](howto-mfa-getstarted.md)

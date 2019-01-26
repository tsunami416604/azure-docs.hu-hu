---
title: Azure MFA-kiszolgáló hozzáférési és használati jelentések |} A Microsoft Docs
description: Ez ismerteti az Azure multi-factor Authentication szolgáltatás - jelentések használata.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: 92c319460d9412436eb1a474431c07766fa87176
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55078899"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication jelentései

Az Azure multi-factor Authentication számos jelentéseket biztosít, amelyek segítségével az Ön és szervezete az Azure Portalon keresztül érhető el. Az alábbi táblázatban az elérhető jelentéseket sorolja fel:

| Jelentés | Hely | Leírás |
|:--- |:--- |:--- |
| Blokkolt felhasználó – előzmények | Az Azure AD > MFA-kiszolgáló > felhasználó blokkolása/feloldása | A felhasználók blokkolása és feloldása kérelmek előzményeit jeleníti meg. |
| Használat és a csalási riasztás | Az Azure AD > bejelentkezések | Információt nyújt az általános használat – felhasználói összefoglalás és felhasználói adatait; csakúgy, mint a megadott dátumtartományban küldött visszaélési riasztások előzményeit. |
| A helyszíni összetevők használata | Az Azure AD > MFA-kiszolgáló > tevékenységgel kapcsolatos jelentés | Információt nyújt az általános használati a multi-factor Authentication az NPS bővítményével, ADFS, és az MFA-kiszolgáló. |
| Mellőzött felhasználó – előzmények | Az Azure AD > MFA-kiszolgáló > egyszeri Mellőzés | Egy felhasználó a multi-factor Authentication kihagyásához a kérelmek előzményeit biztosítja. |
| Kiszolgáló állapota | Az Azure AD > MFA-kiszolgáló > kiszolgáló állapota | A fiókjához társított multi-factor Authentication kiszolgálók állapotát jeleníti meg. |

## <a name="view-mfa-reports"></a>MFA-jelentések megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **MFA-kiszolgáló**.
3. Válassza ki a megtekinteni kívánt jelentést.

   <center>![Felhő](./media/howto-mfa-reporting/report.png)</center>

## <a name="azure-ad-sign-ins-report"></a>Az Azure AD bejelentkezési jelentések

Az a **bejelentkezési tevékenységjelentéshez** a a [az Azure portal](https://portal.azure.com), az információt meg kell határoznia, hogy a környezet működésébe.

A bejelentkezések jelentés biztosíthat kapcsolatos információk a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek, beleértve a többtényezős hitelesítés (MFA) használatával kapcsolatos információért. Az MFA-adatokból betekintést nyerhet, hogy a többtényezős hitelesítés hogyan működik a szervezetben. Ezáltal a következőkhöz hasonló kérdésekre kaphat választ:

- Felmerült a bejelentkezés során MFA?
- Hogyan végezte el a felhasználó az MFA-hitelesítést?
- Miért nem tudta a felhasználó elvégezni az MFA-hitelesítést?
- Hány felhasználó esetében merül fel az MFA?
- Hány felhasználó nem tudta elvégezni az MFA-hitelesítést?
- Melyek a gyakori MFA-problémák, amelyekkel a végfelhasználók találkoznak?

Ezek az adatok keresztül érhető el a [az Azure portal](https://portal.azure.com) és a [reporting API](../reports-monitoring/concept-reporting-api.md).

![Felhő](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Bejelentkezések jelentés szerkezete

Az MFA bejelentkezési tevékenységeinek jelentéseiben a következő adatokhoz fér hozzá:

**MFA szükséges:** E többtényezős hitelesítés szükség a bejelentkezéshez, vagy sem. Lehet, hogy az MFA felhasználónkénti MFA, feltételes hozzáférés vagy más okok miatt szükséges. Lehetséges értékek a következők **Igen** vagy **nem**.

**MFA eredménye:** További információ e MFA teljesítésével vagy megtagadásával:

- Az MFA teljesítése esetén az oszlop a teljesítés módjával kapcsolatos további információkat tartalmaz.
   - Azure Multi-Factor Authentication
      - elvégezve a felhőben
      - lejárt a bérlőn konfigurált szabályzatok miatt
      - regisztráció felkínálva
      - a tokenben lévő jogcím alapján teljesült
      - külső szolgáltató által biztosított jogcím alapján teljesült
      - erős hitelesítéssel teljesült
      - kihagyva, mivel a végrehajtott folyamat a Windows-közvetítő bejelentkezési folyamata volt
      - alkalmazásjelszó használata miatt kihagyva
      - a hely miatt kihagyva
      - regisztrált eszköz használata miatt kihagyva
      - megjegyzett eszköz használata miatt kihagyva
      - sikeresen teljesítve
   - Átirányítva külső szolgáltatóhoz többtényezős hitelesítésre

- Az MFA megtagadása esetén az oszlop a megtagadás okát tartalmazza.
   - Azure Multi-Factor Authentication megtagadva;
      - hitelesítés folyamatban
      - duplikált hitelesítési kísérlet
      - túl sokszor lett hibás kód megadva
      - érvénytelen hitelesítés
      - érvénytelen mobilalkalmazásbeli ellenőrző kód
      - hibás konfiguráció
      - a telefonhívás üzenetrögzítőre kapcsolt
      - a telefonszám formátuma érvénytelen
      - szolgáltatáshiba
      - a felhasználó telefonja nem érhető el
      - a mobilalkalmazás-értesítés nem küldhető el az eszközre
      - a mobilalkalmazás-értesítés nem küldhető el
      - a felhasználó visszautasította a hitelesítést
      - a felhasználó nem reagált a mobilalkalmazás-értesítésre
      - a felhasználónak nincsenek regisztrált ellenőrzési módszerei
      - a felhasználó hibás kódot adott meg
      - a felhasználó hibás PIN-kódot adott meg
      - a felhasználó sikeres hitelesítés nélkül megszakította a telefonhívást
      - a felhasználó blokkolva van
      - a felhasználó soha nem adta még meg az ellenőrzőkódot
      - a felhasználó nem található
      - az ellenőrzőkód már volt használva

**MFA hitelesítési módszer:** A hitelesítési módszer a többtényezős hitelesítés végrehajtásához használt felhasználói. A lehetséges értékek:

- Szöveges üzenet
- Mobilalkalmazás-értesítés
- Telefonhívás (hitelesítési telefon)
- Mobilalkalmazásbeli ellenőrző kód
- Telefonhívás (irodai telefon)
- Telefonhívás (alternatív hitelesítési telefon)

**MFA-hitelesítés részletei:** Kitakarva a telefonszámot, például: + X XXXXXXXX64.

**Feltételes hozzáférés** feltételes hozzáférési szabályzatok is érintett, a bejelentkezési kísérlet többek között további információt:

- Házirend neve
- Engedélyezési vezérlők
- Munkamenet-vezérlők
- Eredmény

## <a name="powershell-reporting"></a>PowerShell-jelentéskészítés

A multi-factor Authentication az alábbi PowerShell-lel regisztráló felhasználók azonosításához.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Azonosítsa azokat a felhasználókat, akik nem regisztrált multi-factor Authentication az alábbi PowerShell-lel.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="next-steps"></a>További lépések

* [A felhasználók számára](../user-help/multi-factor-authentication-end-user.md)
* [Az üzembe helyezés](concept-mfa-whichversion.md)

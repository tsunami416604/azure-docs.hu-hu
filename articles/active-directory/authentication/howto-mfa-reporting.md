---
title: Hozzáférés- és használati jelentések az Azure MFA-hoz – Azure Active Directory
description: Ez ismerteti, hogyan kell használni az Azure többtényezős hitelesítésfunkció - jelentések.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b47e6bac031a5f2cffc8734fee976fbf8dadc666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129089"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Jelentések az Azure többtényezős hitelesítésében

Az Azure többtényezős hitelesítése számos olyan jelentést biztosít, amelyeket Ön és szervezete az Azure Portalon keresztül is használhat. Az alábbi táblázat a rendelkezésre álló jelentéseket sorolja fel:

| Jelentés | Hely | Leírás |
|:--- |:--- |:--- |
| Blokkolt felhasználói előzmények | Az Azure AD > Security > Az MFA > felhasználók blokkolása/feloldása | A felhasználók letiltására vagy feloldására irányuló kérelmek előzményeit jeleníti meg. |
| Használati és csalási riasztások | Az Azure AD > bejelentkezések | Tájékoztatást nyújt az általános használatról, a felhasználói összegzésről és a felhasználó adatairól; valamint a megadott dátumtartományban benyújtott csalási riasztások előzményeit. |
| Helyszíni összetevők használata | Az Azure AD > security > MFA >-tevékenységjelentés | Az MFA általános használatáról a n-ps-bővítményen, az ADFS-en és az MFA-kiszolgálón keresztül. |
| Megkerült felhasználói előzmények | Az Azure AD > security > az MFA-> egyszeri megkerülés | A felhasználó többtényezős hitelesítésének megkerülésére irányuló kérelmek előzményeit tartalmazza. |
| Kiszolgáló állapota | Az Azure AD > Security > Az MFA > kiszolgáló állapota | A fiókhoz társított többtényezős hitelesítési kiszolgálók állapotát jeleníti meg. |

## <a name="view-mfa-reports"></a>Többfa-jelentések megtekintése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. A bal oldalon válassza az **Azure Active Directory** > **security** > **mfa**lehetőséget.
3. Válassza ki a megtekinteni kívánt jelentést.

   ![MFA-kiszolgáló állapotjelentése az Azure Portalon](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Azure AD bejelentkezések jelentés

A **bejelentkezési tevékenység jelentés** az [Azure Portalon,](https://portal.azure.com)beszerezheti a szükséges információkat, hogy meghatározza, hogyan a környezet teljesít.

A bejelentkezések jelentés tájékoztatást nyújt a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek, amely információkat tartalmaz a többtényezős hitelesítés (MFA) használat. Az MFA-adatokból betekintést nyerhet, hogy a többtényezős hitelesítés hogyan működik a szervezetben. Ezáltal a következőkhöz hasonló kérdésekre kaphat választ:

- Felmerült a bejelentkezés során MFA?
- Hogyan végezte el a felhasználó az MFA-hitelesítést?
- Miért nem tudta a felhasználó elvégezni az MFA-hitelesítést?
- Hány felhasználó esetében merül fel az MFA?
- Hány felhasználó nem tudta elvégezni az MFA-hitelesítést?
- Melyek a gyakori MFA-problémák, amelyekkel a végfelhasználók találkoznak?

Ezek az adatok az [Azure Portalon](https://portal.azure.com) és a [jelentéskészítési API-n](../reports-monitoring/concept-reporting-api.md)keresztül érhetők el.

![Az Azure AD-bejelentkezések jelentése az Azure Portalon](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Bejelentkezési jelentés szerkezete

Az MFA bejelentkezési tevékenységeinek jelentéseiben a következő adatokhoz fér hozzá:

**MFA szükséges:** Kötelező-e az MFA a bejelentkezéshez vagy sem. Többszintű be( MFA) felhasználónkénti többszintű többszintű hozzáférési, feltételes hozzáférés vagy egyéb okok miatt megkövetelhető. A lehetséges értékek: **Igen** vagy **Nem**.

**MFA eredménye:** További részletek az MFA teljesítésével vagy megtagadásával kapcsolatban:

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

**MFA hitelesítési módszer:** A módszer, amellyel a felhasználó végrehajtotta az MFA-hitelesítést. A lehetséges értékek a következők:

- Szöveges üzenet
- Mobilalkalmazás-értesítés
- Telefonhívás (hitelesítési telefon)
- Mobilalkalmazásbeli ellenőrző kód
- Telefonhívás (irodai telefon)
- Telefonhívás (alternatív hitelesítési telefon)

**MFA-hitelesítés részletei:** A telefonszám kitakarva, például: +X XXXXXXXX64.

**Feltételes hozzáférés** A bejelentkezési kísérletet érintett feltételes hozzáférési házirendekről többek között a következőket talál:

- Házirend neve
- Támogatási vezérlők
- Munkamenet-vezérlők
- Eredmény

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell-jelentés az MFA-ra regisztrált felhasználókról

Először győződjön meg arról, hogy az [MSOnline V1 PowerShell modul](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) telepítve van.

Azonosítsa azokat a felhasználókat, akik regisztráltak az MFA-ra a következő PowerShell használatával. Ez a parancskészlet kizárja a letiltott felhasználókat, mivel ezek a fiókok nem hitelesíthetők az Azure AD-vel szemben.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods -ne $null -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Azonosítsa azokat a felhasználókat, akik nem regisztráltak az MFA-ra az alábbi PowerShell használatával. Ez a parancskészlet kizárja a letiltott felhasználókat, mivel ezek a fiókok nem hitelesíthetők az Azure AD-vel szemben.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods.Count -eq 0 -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Azonosítsa a regisztrált felhasználókat és kimeneti módszereket. 

```PowerShell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Lehetséges eredmények a tevékenységjelentésekben

Az alábbi táblázat a többtényezős hitelesítési jelentés letöltött verziójával történő hibaelhárítására használható. Nem jelennek meg közvetlenül az Azure Portalon.

| Hívás eredménye | Leírás | Általános leírás |
| --- | --- | --- |
| SUCCESS_WITH_PIN | Beírt PIN-kód | A felhasználó megadott egy PIN-kódot. Ha a hitelesítés sikeres volt, akkor a megfelelő PIN-kódot adták meg. Ha a hitelesítés megtagadva, akkor helytelen PIN-kódot adtak meg, vagy a felhasználó Normál módra van állítva. |
| SUCCESS_NO_PIN | Csak # Entered | Ha a felhasználó PIN-kód módra van állítva, és a hitelesítés megtagadva, az azt jelenti, hogy a felhasználó nem adta meg a PIN-kódját, és csak a #.If the user is set to PIN mode and the authentication is denied, this means the user not enter their PIN and only enter #.  Ha a felhasználó normál módra van állítva, és a hitelesítés sikeres, ez azt jelenti, hogy a felhasználó csak a # -ot adta meg, ami a helyes dolog Normál módban. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Nem nyomva a belépés után | A felhasználó nem küldött DTMF számjegyeket, mivel a # nem lett megadva. A többi megadott számjegy csak akkor kerül elküldésre, ha a sor a tétel befejezését jelzi. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Nincs telefonbemenet - időalapú | A hívásra válaszoltak, de nem érkezett válasz. Ez általában azt jelzi, hogy a hívást hangposta vette fel. |
| SUCCESS_PIN_EXPIRED | Pin-kód lejárt és nem változott | A felhasználó PIN-kódja lejárt, és a rendszer kéri a módosításra, de a PIN-kód módosítása nem fejeződött be. |
| SUCCESS_USED_CACHE | Használt gyorsítótár | A hitelesítés többtényezős hitelesítési hívás nélkül sikerült, mivel a beállított gyorsítótár-időkereten belül történt egy korábbi sikeres hitelesítés ugyanahhoz a felhasználónévhez. |
| SUCCESS_BYPASSED_AUTH | Megkerülve auth | A hitelesítés a felhasználó számára kezdeményezett egyszeri megkerülés használatával sikerült. A bypass-ról további részleteket a megkerülő megkerülési előzményekről szóló jelentésben talál. |
| SUCCESS_USED_IP_BASED_CACHE | Használt IP-alapú gyorsítótár | A hitelesítés többtényezős hitelesítési hívás nélkül sikerült, mivel a beállított gyorsítótár időkeretén belül történt egy korábbi sikeres hitelesítés ugyanahhoz a felhasználónévhez, hitelesítéstípushoz, alkalmazásnévhez és IP-címhez. |
| SUCCESS_USED_APP_BASED_CACHE | Használt alkalmazásalapú gyorsítótár | A hitelesítés többtényezős hitelesítési hívás nélkül sikerült, mivel a konfigurált gyorsítótár időkeretén belül az azonos felhasználónév, a hitelesítési típus és az alkalmazásnév korábbi sikeres hitelesítése történt. |
| SUCCESS_INVALID_INPUT | Érvénytelen telefonbemenet | A telefonról küldött válasz érvénytelen. Ez lehet egy faxkészülék vagy modem, vagy a felhasználó is be * részeként a PIN-kódot. |
| SUCCESS_USER_BLOCKED | A felhasználó le van tiltva | A felhasználó telefonszáma le van tiltva. A letiltott számot kezdeményezheti a felhasználó egy hitelesítési hívás során, vagy egy rendszergazda az Azure Portalon. <br> Megjegyzés: A blokkolt szám is mellékterméke a csalás alert. |
| SUCCESS_SMS_AUTHENTICATED | Szöveges üzenet hitelesítve | Kétirányú tesztüzenet esetén a felhasználó helyesen válaszolt az egyszeri jelkóddal (OTP) vagy otp + PIN-kóddal. |
| SUCCESS_SMS_SENT | Elküldött szöveges üzenet | Szöveges üzenet esetén az egyszeri jelkódot (OTP) tartalmazó szöveges üzenet küldése sikeresen megtörtént. A felhasználó a hitelesítés befejezéséhez adja meg az OTP vagy OTP + PIN-kódot az alkalmazásban. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobilalkalmazás hitelesítése | A felhasználó sikeresen hitelesített a mobilalkalmazáson keresztül. |
| SUCCESS_OATH_CODE_PENDING | OATH-kód függőben | A rendszer kéri a felhasználótól az OATH-kódot, de nem válaszolt. |
| SUCCESS_OATH_CODE_VERIFIED | OATH-kód ellenőrizve | A felhasználó érvényes OATH-kódot adott meg, amikor a rendszer kéri. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Tartalék OATH-kód ellenőrizve | A felhasználó megtagadta a hitelesítést az elsődleges többtényezős hitelesítési módszerrel, majd érvényes OATH-kódot adott meg a tartalékhoz. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Tartalék biztonsági kérdések megválaszolva | A felhasználó megtagadta a hitelesítést az elsődleges többtényezős hitelesítési módszerrel, majd megfelelően válaszolt a biztonsági kérdésekre a tartalék. |
| FAILED_PHONE_BUSY | Auth már folyamatban | A többtényezős hitelesítés már feldolgoz egy hitelesítést ehhez a felhasználóhoz. Ezt gyakran azok a RADIUS-ügyfelek okozzák, amelyek ugyanazon bejelentkezés során több hitelesítési kérelmet küldenek. |
| CONFIG_ISSUE | A telefon nem érhető el | A hívás megkísérlése történt, de vagy nem lehetett elhelyezni, vagy nem érkezett meg a válasz. Ez magában foglalja a foglalt jelet, a gyors foglalt jelet (leválasztva), a háromhangokat (a szám már nem működik), a csengés közbeni időtúltöltést stb. |
| FAILED_INVALID_PHONENUMBER | Érvénytelen telefonszám-formátum | A telefonszám formátuma érvénytelen. A telefonszámnak numerikusnak kell lennie, és 10 számjegynek kell lennie a +1 országkódhoz (Egyesült Államok & Kanada). |
| FAILED_USER_HUNGUP_ON_US | A felhasználó letette a telefont | A felhasználó felvette a telefont, de aztán letette anélkül, hogy megnyomta volna a gombokat. |
| FAILED_INVALID_EXTENSION | Érvénytelen bővítmény | A bővítmény érvénytelen karaktereket tartalmaz. Csak a számjegyek, vesszők, *és # megengedettek. @előtag is használható. |
| FAILED_FRAUD_CODE_ENTERED | Beírt csalási kód | A felhasználó úgy döntött, hogy a hívás során csalást jelent, ami megtagadott hitelesítést és blokkolt telefonszámot eredményezett.| 
| FAILED_SERVER_ERROR | Nem lehet hívást kezdeményezni | A többtényezős hitelesítésszolgáltatás nem tudta elkezdeményezni a hívást. |
| FAILED_SMS_NOT_SENT | Nem lehet szöveges üzenetet küldeni | A szöveges üzenet nem küldhető el. A hitelesítés megtagadva. |
| FAILED_SMS_OTP_INCORRECT | Az OTP szöveges üzenet helytelen | A felhasználó helytelen egyszeri jelszót (OTP) adott meg a kapott szöveges üzenetből. A hitelesítés megtagadva. |
| FAILED_SMS_OTP_PIN_INCORRECT | Szöveges üzenet OTP + PIN helytelen | A felhasználó helytelen egyszeri jelszót (OTP) és/vagy helytelen felhasználói PIN-kódot adott meg. A hitelesítés megtagadva. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Túllépte a szöveges üzenet otp-kísérleteit | A felhasználó túllépte az egyszeri jelkód-kísérletek maximális számát. |
| FAILED_PHONE_APP_DENIED | Mobilalkalmazás megtagadva | A felhasználó a Megtagadás gomb megnyomásával megtagadta a hitelesítést a mobilalkalmazásban. |
| FAILED_PHONE_APP_INVALID_PIN | A mobilalkalmazás érvénytelen PIN-kódja | A felhasználó érvénytelen PIN-kódot adott meg a mobilalkalmazásban való hitelesítéskor. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | A mobilalkalmazás PIN-kódja nem változott | A felhasználó nem sikerült végrehajtani a szükséges PIN-kódmódosítást a mobilalkalmazásban. |
| FAILED_FRAUD_REPORTED | Jelentett csalás | A felhasználó csalást jelentett a mobilalkalmazásban. |
| FAILED_PHONE_APP_NO_RESPONSE | Mobilalkalmazás nincs válasz | A felhasználó nem válaszolt a mobilalkalmazás hitelesítési kérelmére. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Mobilalkalmazás minden eszköz blokkolva | A felhasználó mobilalkalmazás-eszközei már nem válaszolnak az értesítésekre, és le vannak tiltva. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Sikertelen a mobilalkalmazás-értesítés | Hiba történt, amikor értesítést próbált küldeni a mobilalkalmazásnak a felhasználó eszközén. |
| FAILED_PHONE_APP_INVALID_RESULT | A mobilalkalmazás érvénytelen eredménye | A mobilalkalmazás érvénytelen eredményt adott vissza. |
| FAILED_OATH_CODE_INCORRECT | AZ OATH-kód helytelen | A felhasználó helytelen OATH kódot adott meg.  A hitelesítés megtagadva. |
| FAILED_OATH_CODE_PIN_INCORRECT | OATH kód + PIN helytelen | A felhasználó helytelen OATH kódot és/vagy helytelen felhasználói PIN-kódot adott meg.  A hitelesítés megtagadva. |
| FAILED_OATH_CODE_DUPLICATE | Ismétlődő OATH-kód | A felhasználó olyan OATH kódot adott meg, amelyet korábban használt.  A hitelesítés megtagadva. |
| FAILED_OATH_CODE_OLD | Oath kód elavult | A felhasználó olyan OATH kódot adott meg, amely megelőzi a korábban használt OATH-kódot.  A hitelesítés megtagadva. |
| FAILED_OATH_TOKEN_TIMEOUT | OATH-kód eredményének időmeghosszabbítása | A felhasználónak túl sokáig tartott az OATH-kód megadása, és a többtényezős hitelesítési kísérlet már időtúllépést tett. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Biztonsági kérdések eredménye időmeghosszabbítás | A felhasználónak túl sokáig tartott a biztonsági kérdésekre adott válasz megadása, és a többtényezős hitelesítési kísérlet már túlsok időtúllépést tett. |
| FAILED_AUTH_RESULT_TIMEOUT | Auth eredmény időmeghosszabbítása | A felhasználó nak túl sokáig tartott a többtényezős hitelesítési kísérlet befejezése. |
| FAILED_AUTHENTICATION_THROTTLED | Hitelesítés szabályozása | A többtényezős hitelesítési kísérletet a szolgáltatás szabályozta. |

## <a name="next-steps"></a>További lépések

* [SSPR és MFA-használat és elemzési jelentések](howto-authentication-methods-usage-insights.md)
* [Felhasználóknak](../user-help/multi-factor-authentication-end-user.md)
* [A telepítés helye](concept-mfa-whichversion.md)

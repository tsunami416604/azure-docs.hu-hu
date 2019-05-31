---
title: Az Azure MFA - Azure Active Directory hozzáférési és használati jelentések
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 988e8982b6f06fb1210330c5cafdb696892794fe
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235515"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Az Azure multi-factor Authentication jelentései

Az Azure multi-factor Authentication számos jelentéseket biztosít, amelyek segítségével az Ön és szervezete az Azure Portalon keresztül érhető el. Az alábbi táblázatban az elérhető jelentéseket sorolja fel:

| Jelentés | Location egység | Leírás |
|:--- |:--- |:--- |
| Blokkolt felhasználó – előzmények | Az Azure AD > MFA-kiszolgáló > felhasználó blokkolása/feloldása | A felhasználók blokkolása és feloldása kérelmek előzményeit jeleníti meg. |
| Használat és a csalási riasztás | Az Azure AD > bejelentkezések | Információt nyújt az általános használat – felhasználói összefoglalás és felhasználói adatait; csakúgy, mint a megadott dátumtartományban küldött visszaélési riasztások előzményeit. |
| A helyszíni összetevők használata | Az Azure AD > MFA-kiszolgáló > tevékenységgel kapcsolatos jelentés | Információt nyújt az általános használati a multi-factor Authentication az NPS bővítményével, ADFS, és az MFA-kiszolgáló. |
| Kihagyott felhasználók előzményei | Az Azure AD > MFA-kiszolgáló > egyszeri Mellőzés | Egy felhasználó a multi-factor Authentication kihagyásához a kérelmek előzményeit biztosítja. |
| Kiszolgáló állapota | Az Azure AD > MFA-kiszolgáló > kiszolgáló állapota | A fiókjához társított multi-factor Authentication kiszolgálók állapotát jeleníti meg. |

## <a name="view-mfa-reports"></a>MFA-jelentések megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza ki a **Azure Active Directory** > **MFA-kiszolgáló**.
3. Válassza ki a megtekinteni kívánt jelentést.

   ![MFA-kiszolgáló server állapotjelentése az Azure Portalon](./media/howto-mfa-reporting/report.png)

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

![Az Azure AD bejelentkezési jelentések az Azure Portalon](./media/howto-mfa-reporting/sign-in-report.png)

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

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell-jelentések a multi-factor Authentication regisztrált felhasználók

Először is győződjön meg arról, hogy a [MSOnline V1 PowerShell-modul](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) telepítve.

A multi-factor Authentication az alábbi PowerShell-lel regisztráló felhasználók azonosításához.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Azonosítsa azokat a felhasználókat, akik nem regisztrált multi-factor Authentication az alábbi PowerShell-lel.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Tevékenységjelentések lehetséges eredményez

A következő táblázat használható hibaelhárítása a multi-factor Authentication hitelesítést a multi-factor authentication szolgáltatás tevékenységgel kapcsolatos jelentés letöltött verzióját használja. Ezek nem fog megjelenni közvetlenül az Azure Portalon.

| Hívás eredménye | Leírás | Széles körű leírása |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN Entered | A felhasználó által megadott PIN-kódot.  Ha a hitelesítés sikeres, akkor be azokat a megfelelő PIN-kódot.  Ha a rendszer megtagadja a hitelesítést, majd beírt helytelen PIN-kód, vagy a felhasználó normál mód használatára van beállítva. |
| SUCCESS_NO_PIN | Csak a megadott # | Ha a felhasználó PIN-kód módra van beállítva, és a hitelesítés megtagadása esetén, ez azt jelenti, hogy a felhasználó nem adta meg a PIN-kódját, és csak a megadott #.  Ha a felhasználó a normál mód használatára van beállítva, és a hitelesítés sikeres, ez azt jelenti, hogy a felhasználó csak megadott #, amely a megfelelő teendő a normál módban. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Nem volt lenyomva a bevitel után | A felhasználó nem küldött bármely DTMF-számjegyeknek óta # adtak meg.  Más beírt számjegyek nem kap, kivéve, ha # is meg kell adni a bejegyzés befejezését jelző. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Nincs telefonos bemenet – időtúllépés | A hívás, de nem érkezett válasz.  Ez általában azt jelzi, hogy a hívás volt dolgozza fel hangposta. |
| SUCCESS_PIN_EXPIRED | PIN-kód lejárt és nem változott | A felhasználó PIN-kód lejárt és azok jelenített meg módosítani, de a PIN-kód módosítása sikerült nem sikerült befejezni. |
| SUCCESS_USED_CACHE | Felhasznált gyorsítótár | A hitelesítés sikeres anélkül, hogy a multi-factor Authentication-hívás, mivel az előző sikeres hitelesítés azonos felhasználónév történt a gyorsítótár konfigurált időtartamon belül. |
| SUCCESS_BYPASSED_AUTH | A hitelesítés megkerülve | A hitelesítés sikeres, a felhasználó által kezdeményezett egyszeri Mellőzés használatával.  A telepítővarázslója kihagyja a felhasználó Teljesítményelőzményeinek jelentése. További részletekért lásd a Mellőzés. |
| SUCCESS_USED_IP_BASED_CACHE | IP-alapú gyorsítótár használatban | A hitelesítés sikeres, a multi-factor Authentication-hívás nélkül óta a korábbi sikeres hitelesítés, a felhasználónevet, hitelesítés típusa, alkalmazás neve és IP történt a gyorsítótár konfigurált időtartamon belül. |
| SUCCESS_USED_APP_BASED_CACHE | Alkalmazás-alapú gyorsítótár használatban | A hitelesítés sikeres anélkül, hogy a multi-factor Authentication-hívás a azonos felhasználónév, a hitelesítés típusát és az alkalmazás nevét, a gyorsítótár konfigurált időtartamon belül a korábbi sikeres hitelesítés óta. |
| SUCCESS_INVALID_INPUT | Érvénytelen bemenet a telefonról | Az a telefonjáról küldött válasz nem érvényes.  Ez lehet faxkészülék, vagy előfordulhat, hogy modem, illetve a megadott * részeként PIN-kódjukban. |
| SUCCESS_USER_BLOCKED | A felhasználó blokkolva van | A felhasználó telefonszámának le van tiltva.  Letiltott számnak kezdeményezhetők, a felhasználó-hitelesítési hívás során vagy egy rendszergazda az Azure portal használatával. <br> MEGJEGYZÉS:  Letiltott számnak is egy, a csalási riasztás byproduct. |
| SUCCESS_SMS_AUTHENTICATED | A szöveges üzenet hitelesítve | A kétirányú tesztüzenetet, a felhasználó megfelelően válaszolt az egyszer használatos jelszót (OTP) együtt vagy egyszeri jelszó + PIN-kódot. |
| SUCCESS_SMS_SENT | A szöveges üzenet elküldve | A szöveges üzenet az egyszer használatos jelszót (OTP) tartalmazó szöveges üzenet elküldése sikerült.  A felhasználó beírja az OTP vagy OTP + PIN-kódot az alkalmazás a hitelesítés végrehajtásához. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobilalkalmazás hitelesítve | A felhasználó sikeresen hitelesíteni a mobilalkalmazásban. |
| SUCCESS_OATH_CODE_PENDING | OATH-kód függőben | A felhasználó OATH-kódját a rendszer kéri, de nem válaszolt. |
| SUCCESS_OATH_CODE_VERIFIED | OATH-kód ellenőrizve | A felhasználó által megadott egy érvényes OATH kódot, amikor a rendszer kéri. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Tartalék OATH-kód ellenőrizve | A felhasználó lett megtagadva az elsődleges multi-factor Authentication módszer használatával hitelesítést, és megjelenik egy érvényes OATH-kódot tartalékként. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Tartalék biztonsági kérdés megválaszolva | A felhasználó az elsődleges multi-factor Authentication hitelesítési módszerrel hitelesítési meg lett tagadva, és majd megválaszolták biztonsági kérdéseiket megfelelően a tartalék. |
| FAILED_PHONE_BUSY | A hitelesítés már folyamatban van | A multi-factor Authentication szolgáltatás feldolgozása már folyamatban van a felhasználó hitelesítést.  Ezt gyakran okozza a RADIUS-ügyfelek, amelyek ugyanazt a bejelentkezés során több hitelesítési kéréseket küldeni. |
| CONFIG_ISSUE | Telefon nem érhető el | Hívás történt kísérlet, de akár nem lehet helyezni, vagy nem ad választ.  Ez magában foglalja a foglalt, gyors foglalt jel (választva), tri-hangjelzések (service-ben már nem szám), időkorlátot, miközben fogadalmunk, és így tovább. |
| FAILED_INVALID_PHONENUMBER | Érvénytelen telefonszám-formátum | A telefonszám formátuma érvénytelen.  Telefonszámok számokkal kell megadni, és 10 számjegy országkódhoz + 1 (Amerikai Egyesült Államok és Kanada) kell lennie. |
| FAILED_USER_HUNGUP_ON_US | A felhasználó letette a telefont | A felhasználó a telefon választ, de bármilyen gomb lenyomása nélkül bontotta. |
| FAILED_INVALID_EXTENSION | Érvénytelen mellék | A kiterjesztés érvénytelen karaktereket tartalmaz.  Csak számjegyek, vessző, *, és # jel szerepelhet.  Egy @ előtag használható. |
| FAILED_FRAUD_CODE_ENTERED | Fraud Code Entered | A felhasználó csalást választott-e a megtagadott hitelesítést és a egy letiltott telefonszám hívása során.| 
| FAILED_SERVER_ERROR | Nem sikerült hívást kezdeményezni | A multi-factor Authentication szolgáltatás nem tudta a hívás. |
| FAILED_SMS_NOT_SENT | Nem sikerült elküldeni a szöveges üzenet | Nem sikerült elküldeni a szöveges üzenet.  A hitelesítés megtagadva. |
| FAILED_SMS_OTP_INCORRECT | SMS-üzenet OTP helytelen | A felhasználó hibás egyszeri jelszó (OTP) lépett be a szöveges üzenetet kaptak.  A hitelesítés megtagadva. |
| FAILED_SMS_OTP_PIN_INCORRECT | SMS-üzenet OTP + PIN helytelen | A felhasználó által megadott hibás egyszeri jelszó (OTP) és/vagy egy PIN-kód helytelen felhasználói.  A hitelesítés megtagadva. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Túllépte a maximális szöveges üzenet egyszeri Jelszavas kísérletek | A felhasználó túllépte az egyszer használatos jelszót (OTP) kísérletek maximális számát. |
| FAILED_PHONE_APP_DENIED | A mobilalkalmazás elutasítva | A felhasználó az elutasítás gomb megnyomásával a mobilalkalmazásban a hitelesítés megtagadva. |
| FAILED_PHONE_APP_INVALID_PIN | Mobilalkalmazás érvénytelen PIN-kód | A felhasználó által megadott érvénytelen PIN-kódot a mobilalkalmazásban a hitelesítés során. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Mobilalkalmazás PIN kód nincs megváltoztatva | A felhasználó nem tudta befejezni a szükséges PIN-kód megváltoztatása a mobilalkalmazásban. |
| FAILED_FRAUD_REPORTED | Visszaélést jelentett | A felhasználó a mobilalkalmazásban csalást jelentett. |
| FAILED_PHONE_APP_NO_RESPONSE | Mobilalkalmazás nincs válasz. | A felhasználó nem reagált a mobilalkalmazás hitelesítési kéréshez. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Mobilalkalmazás – összes eszköz letiltva | A felhasználó számára a mobilalkalmazás-eszközök vannak nem válaszol az értesítésekre, és le lett tiltva. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Mobilalkalmazás értesítés sikertelen | Hiba történt, értesítést küld, a felhasználók a mobilalkalmazást. |
| FAILED_PHONE_APP_INVALID_RESULT | Mobilalkalmazás érvénytelen eredmény | A mobilalkalmazás érvénytelen eredményt adott vissza. |
| FAILED_OATH_CODE_INCORRECT | OATH-kód helytelen | A felhasználó adott meg helytelen OATH kódot.  A hitelesítés megtagadva. |
| FAILED_OATH_CODE_PIN_INCORRECT | Az OATH-kód + PIN-kód helytelen | A felhasználó által megadott érvénytelen OATH-kód és/vagy egy PIN-kód helytelen felhasználói.  A hitelesítés megtagadva. |
| FAILED_OATH_CODE_DUPLICATE | Duplikált OATH-kód | A felhasználó által megadott OATH-kódot, amely a korábban használt.  A hitelesítés megtagadva. |
| FAILED_OATH_CODE_OLD | OATH-kód lejárt | A felhasználó által megadott OATH-kódot, amely megelőzi az OATH-kódot, amely a korábban használt.  A hitelesítés megtagadva. |
| FAILED_OATH_TOKEN_TIMEOUT | OATH-kód eredményének időkorlátja | A felhasználó adja meg az OATH-kód túl sokáig tartott, és a multi-factor Authentication hitelesítési kísérlet már korábban túllépte az időkorlátot. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Biztonsági kérdések eredményének időkorlátja | A felhasználó adja meg a biztonsági kérdések kérdésre adott válasz túl sokáig tartott, és a multi-factor Authentication hitelesítési kísérlet már korábban túllépte az időkorlátot. |
| FAILED_AUTH_RESULT_TIMEOUT | Hitelesítés eredményének időkorlátja | A felhasználó túl sokáig befejezéséhez a multi-factor Authentication hitelesítési kísérlet után. |
| FAILED_AUTHENTICATION_THROTTLED | Hitelesítési szabályozva | A multi-factor Authentication hitelesítési kísérlet szabályozta a szolgáltatást. |

## <a name="next-steps"></a>További lépések

* [A felhasználók számára](../user-help/multi-factor-authentication-end-user.md)
* [Az üzembe helyezés](concept-mfa-whichversion.md)

---
title: Az Azure MFA hozzáférési és használati jelentései – Azure Active Directory
description: Ez az Azure Multi-Factor Authentication funkció – jelentések használatát ismerteti.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df562d65ad064efb1be337e0b68cb8638536981
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82112762"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Jelentések az Azure Multi-Factor Authentication

Az Azure Multi-Factor Authentication számos jelentést biztosít, amelyeket Ön és a szervezete a Azure Portal keresztül is elérhet. A következő táblázat felsorolja a rendelkezésre álló jelentéseket:

| Jelentés | Hely | Leírás |
|:--- |:--- |:--- |
| Letiltott felhasználói előzmények | Az Azure AD > biztonsági > MFA > a felhasználók blokkolására/feloldására | Megjeleníti a felhasználók blokkolására vagy feloldására irányuló kérelmek előzményeit. |
| Használati és csalási riasztások | Azure AD > bejelentkezések | Információt nyújt a teljes használatról, a felhasználói összesítésekről és a felhasználói adatokról; valamint a megadott dátumtartomány szerint elküldött csalási riasztások előzményei. |
| Helyszíni összetevők használata | Azure AD > Security > MFA > tevékenység jelentés | Információkat nyújt az MFA általános használatáról az NPS-bővítmény, az ADFS és az MFA-kiszolgáló használatával. |
| Megkerülő felhasználói előzmények | Azure AD > Security > MFA > egyszeri Mellőzés | A egy felhasználó megkerülésére vonatkozó kérelmek előzményeit jeleníti meg Multi-Factor Authentication. |
| Kiszolgáló állapota | Azure AD > Security > MFA > kiszolgáló állapota | A fiókjához társított Multi-Factor Authentication kiszolgálók állapotát jeleníti meg. |

## <a name="view-mfa-reports"></a>MFA-jelentések megtekintése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldalon válassza a **Azure Active Directory** > **biztonsági** > **MFA**elemet.
3. Válassza ki a megtekinteni kívánt jelentést.

   ![MFA-kiszolgáló kiszolgálójának állapota jelentés a Azure Portal](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Azure AD-beli bejelentkezési jelentés

A [Azure Portal](https://portal.azure.com)a **bejelentkezési tevékenységek jelentésével** megtekintheti a környezete működésének meghatározásához szükséges információkat.

A bejelentkezések jelentés a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról nyújt információkat, beleértve a többtényezős hitelesítés (MFA) használatáról szóló információkat is. Az MFA-adatokból betekintést nyerhet, hogy a többtényezős hitelesítés hogyan működik a szervezetben. Ezáltal a következőkhöz hasonló kérdésekre kaphat választ:

- Felmerült a bejelentkezés során MFA?
- Hogyan végezte el a felhasználó az MFA-hitelesítést?
- Miért nem tudta a felhasználó elvégezni az MFA-hitelesítést?
- Hány felhasználó esetében merül fel az MFA?
- Hány felhasználó nem tudta elvégezni az MFA-hitelesítést?
- Melyek a gyakori MFA-problémák, amelyekkel a végfelhasználók találkoznak?

Ezek az adatszolgáltatások a [Azure Portal](https://portal.azure.com) és a [jelentéskészítő API](../reports-monitoring/concept-reporting-api.md)-n keresztül érhetők el.

![Azure AD-beli bejelentkezési jelentés a Azure Portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Bejelentkezések jelentési struktúrája

Az MFA bejelentkezési tevékenységeinek jelentéseiben a következő adatokhoz fér hozzá:

**MFA szükséges:** Kötelező-e az MFA a bejelentkezéshez vagy sem. A többtényezős hitelesítés a felhasználónkénti MFA, a feltételes hozzáférés vagy más okok miatt lehet szükséges. A lehetséges értékek: **Igen** vagy **nem**.

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
      - a felhasználó telefonszáma nem érhető el
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

**Feltételes hozzáférés** Információk a bejelentkezési kísérletet érintő feltételes hozzáférési szabályzatokról, beleértve a következőket:

- Házirend neve
- Vezérlők megadása
- Munkamenet-vezérlőelemek
- Eredmény

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>PowerShell-jelentéskészítés az MFA-hoz regisztrált felhasználókkal

Először győződjön meg arról, hogy telepítve van a [MSOnline v1 PowerShell-modulja](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) .

Azonosítsa az MFA-val regisztrált felhasználókat az alábbi PowerShell-lel. Ezek a parancsok kizárják a letiltott felhasználókat, mivel ezek a fiókok nem tudják hitelesíteni az Azure AD-t.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Azonosítsa azokat a felhasználókat, akik nem regisztráltak az MFA használatára az alábbi PowerShell-lel. Ezek a parancsok kizárják a letiltott felhasználókat, mivel ezek a fiókok nem tudják hitelesíteni az Azure AD-t.

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

A regisztrált felhasználók és kimeneti metódusok azonosítása. 

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>A tevékenységgel kapcsolatos jelentések lehetséges eredményei

Az alábbi táblázat a multi-Factor Authentication tevékenység jelentés letöltött verziójával végezhető el a többtényezős hitelesítés hibakereséséhez. Nem jelennek meg közvetlenül a Azure Portal.

| Hívás eredménye | Leírás | Széles Leírás |
| --- | --- | --- |
| SUCCESS_WITH_PIN | PIN-kód megadva | A felhasználó PIN-kódot adott meg. Ha a hitelesítés sikeres volt, akkor a megfelelő PIN-kódot adtak meg. Ha a hitelesítés megtagadva, akkor helytelen PIN-kódot adtak meg, vagy a felhasználó normál módra van beállítva. |
| SUCCESS_NO_PIN | Csak # megadott | Ha a felhasználó PIN módra van beállítva, és a hitelesítés megtagadva, ez azt jelenti, hogy a felhasználó nem adta meg a PIN-kódját, és csak a # értéket adta meg.  Ha a felhasználó szabványos módra van beállítva, és a hitelesítés sikeres, akkor a felhasználó csak a # paramétert adta meg, ami a helyes dolog a standard módban. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | A beléptetés után nem kell lenyomni | A felhasználó nem küldött DTMF-számjegyeket, mivel a # nem lett megadva. A beírt egyéb számjegyek nem lesznek elküldve, kivéve, ha a # meg van adva a bejegyzés befejezését jelző karakter. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Nincs telefonos bemenet – időtúllépés | A hívást megválaszolták, de nem volt válasz. Ez általában azt jelzi, hogy a hívást a hangposta vette át. |
| SUCCESS_PIN_EXPIRED | A PIN-kód lejárt és nem változott | A felhasználó PIN-kódja lejárt, és a rendszer megkéri, hogy módosítsa, de a PIN-kód módosítása nem fejeződött be sikeresen. |
| SUCCESS_USED_CACHE | Használt gyorsítótár | A hitelesítés Multi-Factor Authentication hívás nélkül sikerült, mert egy korábbi sikeres hitelesítés ugyanahhoz a felhasználónévhez a beállított gyorsítótár időkeretén belül történt. |
| SUCCESS_BYPASSED_AUTH | Kihagyott hitelesítés | A hitelesítés sikeres volt a felhasználó számára indított egyszeri Mellőzés használatával. A mellőzéssel kapcsolatos további részletekért tekintse meg a felhasználói előzmények megkerülő jelentést. |
| SUCCESS_USED_IP_BASED_CACHE | Használt IP-alapú gyorsítótár | A hitelesítés Multi-Factor Authentication hívás nélkül sikeres volt, mert az előző sikeres hitelesítés ugyanarra a felhasználónévre, hitelesítési típusra, alkalmazás nevére és IP-re történt a beállított gyorsítótári időkereten belül. |
| SUCCESS_USED_APP_BASED_CACHE | Alkalmazás-alapú gyorsítótár használatban | A hitelesítés Multi-Factor Authentication hívás nélkül sikeres volt, mert egy korábbi sikeres hitelesítés történt ugyanazzal a felhasználónévvel, hitelesítési típussal és alkalmazás nevével a beállított gyorsítótár időkeretén belül. |
| SUCCESS_INVALID_INPUT | Érvénytelen telefonos bevitel | A telefonról küldött válasz érvénytelen. Ez lehet egy faxkészülék vagy modem, vagy előfordulhat, hogy a felhasználó a PIN-kód részeként megadta a t. |
| SUCCESS_USER_BLOCKED | A felhasználó le van tiltva | A felhasználó telefonszáma le van tiltva. A felhasználó egy hitelesítési hívás vagy egy, a Azure Portal használó rendszergazda által kezdeményezett letiltott számot is kezdeményezhet. <br> Megjegyzés: A letiltott szám a csalási riasztások mellékterméke is. |
| SUCCESS_SMS_AUTHENTICATED | Szöveges üzenet hitelesítve | Kétirányú tesztelési üzenet esetén a felhasználó az egyszeri jelszóval (OTP) vagy az OTP + PIN-kóddal helyesen válaszol. |
| SUCCESS_SMS_SENT | Szöveges üzenet elküldve | Szöveges üzenet esetén az egyszer használatos jelszót (OTP) tartalmazó szöveges üzenet sikeresen elküldve. A felhasználó a hitelesítés elvégzéséhez megadja az OTP vagy az OTP + PIN kódot az alkalmazásban. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobile App hitelesítve | A felhasználó sikeresen hitelesítve lett a Mobile App használatával. |
| SUCCESS_OATH_CODE_PENDING | ESKÜ kód függőben | A felhasználót a rendszer az eskü kód megadására kéri, de nem válaszolt. |
| SUCCESS_OATH_CODE_VERIFIED | ESKÜ kód ellenőrizve | A felhasználó egy érvényes eskü-kódot adott meg, amikor a rendszer kéri. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Tartalék eskü kód ellenőrizve | A felhasználó az elsődleges Multi-Factor Authentication metódus használatával megtagadta a hitelesítést, majd érvényes eskü-kódot adott meg a tartalékhoz. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Tartalék biztonsági kérdések megválaszolva | A felhasználó az elsődleges Multi-Factor Authentication metódussal megtagadta a hitelesítést, majd a tartalékként való megfelelő biztonsági kérdéseket válaszol. |
| FAILED_PHONE_BUSY | A hitelesítés már folyamatban van | Multi-Factor Authentication már a felhasználó hitelesítését dolgozza fel. Ezt gyakran a RADIUS-ügyfelek okozzák, amelyek több hitelesítési kérelmet küldenek ugyanazon bejelentkezés során. |
| CONFIG_ISSUE | A telefon nem érhető el | A rendszer megkísérelte a hívást, de nem helyezhető el vagy nem válaszolt. Ez magában foglalja a foglalt jelet, a gyors foglalt jelet (leválasztva), a Tri-Tone (már nem a szolgáltatásban), az időkorlátot és a csengetést. |
| FAILED_INVALID_PHONENUMBER | Érvénytelen telefonszám-formátum | A telefonszám formátuma érvénytelen. A telefonszámoknak számnak kell lennie, és 10 számjegyből kell állnia az országkód + 1 (Egyesült Államok & Canada) számára. |
| FAILED_USER_HUNGUP_ON_US | A felhasználó letette a telefont | A felhasználó megválaszolta a telefont, de a gombok megnyomása nélkül lefagyott. |
| FAILED_INVALID_EXTENSION | Érvénytelen bővítmény | A bővítmény érvénytelen karaktereket tartalmaz. Csak számjegyek, vessző, * és # karakter megengedett. A @ előtagot is lehet használni. |
| FAILED_FRAUD_CODE_ENTERED | Csalási kód megadva | A felhasználó megtagadta a csalás jelentését a hívás során, ami egy elutasított hitelesítést és egy letiltott telefonszámot eredményezett.| 
| FAILED_SERVER_ERROR | A hívás nem helyezhető el | A Multi-Factor Authentication szolgáltatás nem tudta elhelyezni a hívást. |
| FAILED_SMS_NOT_SENT | Nem lehet elküldeni a szöveges üzenetet | Nem lehet elküldeni a szöveges üzenetet. A hitelesítés megtagadva. |
| FAILED_SMS_OTP_INCORRECT | Szöveges üzenet, OTP helytelen | A felhasználó helytelen egyszeri jelszót (OTP) adott meg a kapott szöveges üzenetből. A hitelesítés megtagadva. |
| FAILED_SMS_OTP_PIN_INCORRECT | Szöveges üzenet – OTP + PIN-kód helytelen | A felhasználó helytelen egyszeri jelszót (OTP) és/vagy helytelen felhasználói PIN-kódot adott meg. A hitelesítés megtagadva. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Túllépte a maximális szöveges üzenet egyszeri jelszavas kísérleteit | A felhasználó túllépte az egyszeri jelszavas (OTP) kísérletek maximális számát. |
| FAILED_PHONE_APP_DENIED | A Mobile App megtagadva | A felhasználó megtagadta a hitelesítést a Mobile alkalmazásban a Megtagadás gomb megnyomásával. |
| FAILED_PHONE_APP_INVALID_PIN | Érvénytelen a Mobile App PIN-kód | A felhasználó érvénytelen PIN-kódot adott meg a Mobile alkalmazásban végzett hitelesítés során. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | A Mobile App PIN-kódja nem módosult | A felhasználó nem tudta sikeresen befejezni a PIN-kód megváltoztatását a Mobile alkalmazásban. |
| FAILED_FRAUD_REPORTED | Csalás jelentett | A felhasználó csalást jelentett a Mobile alkalmazásban. |
| FAILED_PHONE_APP_NO_RESPONSE | Nincs válasz a mobil alkalmazásra | A felhasználó nem válaszolt a Mobile App Authentication kérelemre. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Mobile App minden eszköz letiltva | A felhasználóhoz tartozó mobileszköz-eszközök már nem válaszolnak az értesítésekre, és le vannak tiltva. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | A Mobile apps-értesítés sikertelen | Hiba történt, amikor a rendszer megpróbált elküldeni egy értesítést a mobil alkalmazásnak a felhasználó eszközén. |
| FAILED_PHONE_APP_INVALID_RESULT | A Mobile App érvénytelen eredménye | A Mobile alkalmazás érvénytelen eredményt adott vissza. |
| FAILED_OATH_CODE_INCORRECT | Helytelen az eskü kódja | A felhasználó helytelen eskü-kódot adott meg.  A hitelesítés megtagadva. |
| FAILED_OATH_CODE_PIN_INCORRECT | Az eskü kódja és a PIN-kód helytelen | A felhasználó helytelen eskü kód és/vagy helytelen felhasználói PIN-kódot adott meg.  A hitelesítés megtagadva. |
| FAILED_OATH_CODE_DUPLICATE | Ismétlődő eskü kódja | A felhasználó a korábban használt eskü kódot adta meg.  A hitelesítés megtagadva. |
| FAILED_OATH_CODE_OLD | Az eskü kódja elavult | A felhasználó olyan eskü-kódot adott meg, amely megelőzi a korábban használt eskü-kódot.  A hitelesítés megtagadva. |
| FAILED_OATH_TOKEN_TIMEOUT | ESKÜ kód eredményének időtúllépése | A felhasználó túl sokáig tartott az eskü kódjának megadásához, és a Multi-Factor Authentication kísérlet már időtúllépéssel járt. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Biztonsági kérdések eredményének időtúllépése | A felhasználó túl sokáig tartott a biztonsági kérdésekre adott válasz megadására, és a Multi-Factor Authentication kísérlet már időtúllépéssel járt. |
| FAILED_AUTH_RESULT_TIMEOUT | Hitelesítés eredményének időtúllépése | A felhasználó túl sokáig tartott a Multi-Factor Authentication kísérlet befejezéséhez. |
| FAILED_AUTHENTICATION_THROTTLED | Hitelesítés szabályozva | Az Multi-Factor Authentication kísérletet a szolgáltatás szabályozta. |

## <a name="next-steps"></a>További lépések

* [SSPR és MFA-használati és-bejelentési jelentések](howto-authentication-methods-usage-insights.md)
* [Felhasználók számára](../user-help/multi-factor-authentication-end-user.md)
* [Az üzembe helyezés helye](concept-mfa-whichversion.md)

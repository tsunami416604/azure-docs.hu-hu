---
title: 'Az Azure AD Connect: Az átmenő hitelesítés hibaelhárítása |} A Microsoft Docs'
description: Ez a cikk ismerteti, hogyan háríthatók el az Azure Active Directory (Azure AD) átmenő hitelesítés.
services: active-directory
keywords: Hibaelhárítás az Azure AD Connect az átmenő hitelesítés, Active Directory, Azure AD egyszeri bejelentkezés, a szükséges összetevők telepítése egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 6cd6b139699b38a06a8e3f9fce5eb6e24fe24654
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214175"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Az Azure Active Directory átmenő hitelesítés hibaelhárítása

Ez a cikk hibaelhárítási információkat az Azure AD átmenő hitelesítés kapcsolatos gyakori problémákkal kapcsolatban.

>[!IMPORTANT]
>Ha az átmenő hitelesítés felhasználói bejelentkezési problémák, nem a funkció letiltásához vagy anélkül, hogy egy csak felhőalapú globális rendszergazdai fiókkal tud visszatérni átmenő hitelesítés ügynökök eltávolítása. Ismerje meg [hozzáadása egy csak felhőalapú globális rendszergazdai fiókkal](../active-directory-users-create-azure-portal.md). Ehhez a lépéshez kritikus fontosságú, és biztosítja, hogy ne zárja ki a bérlő.

## <a name="general-issues"></a>Általános problémák

### <a name="check-status-of-the-feature-and-authentication-agents"></a>A szolgáltatás és a hitelesítési ügynökök állapotának ellenőrzése

Győződjön meg arról, hogy az átmenő hitelesítés funkció továbbra is **engedélyezve** a bérlő és a hitelesítési ügynökök állapotát jeleníti meg **aktív**, és nem **inaktív**. Címen ellenőrizheti a **az Azure AD Connect** paneljén a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/).

![Az Azure Active Directory felügyeleti központ – az Azure AD Connect panel](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Az Azure Active Directory felügyeleti központ – átmenő hitelesítés panel](./media/active-directory-aadconnect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Felhasználói bejelentkezési hibaüzenetek

Ha a felhasználó nem tud bejelentkezni az átmenő hitelesítéssel, megjelenhet számukra felhasználói hibák a következők egyikét a bejelentkezési képernyő Azure ad-ben: 

|Hiba|Leírás|Feloldás
| --- | --- | ---
|AADSTS80001|Nem lehet csatlakozni az Active Directory|Győződjön meg arról, hogy az ügynök kiszolgálók tagjai a felhasználókat, amelyeknek a jelszava kell érvényesíteni eltérő AD-erdőben, és azok tud csatlakozni az Active Directory.  
|AADSTS8002|Időtúllépés történt az Active Directoryhoz csatlakozó|Ellenőrizze, hogy ellenőrizze, hogy az Active Directory elérhető-e, és az ügynököktől származó válaszol a kérelmekre.
|AADSTS80004|Az ügynök átadott felhasználónév nem érvényes|Győződjön meg arról, a felhasználó megpróbál bejelentkezni a megfelelő felhasználónév.
|AADSTS80005|Érvényesítési kiszámíthatatlan WebException észlelt|Átmeneti hiba. Ismételje meg a kérelmet. Ha továbbra sem sikerül, forduljon a Microsoft ügyfélszolgálatához.
|AADSTS80007|Hiba történt az Active Directory kommunikál|További információ az ügynök naplókat, és győződjön meg arról, hogy a várt módon működik-e az Active Directory.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Bejelentkezési hibák okai a az Azure Active Directory felügyeleti központban (prémium szintű licencre van szüksége)

Ha a bérlő az Azure AD Premium licenccel rendelkezik, akkor is megjeleníthető a [bejelentkezési tevékenységek jelentésének](../active-directory-reporting-activity-sign-ins.md) a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/).

![Az Azure Active Directory felügyeleti központ – bejelentkezési jelentések](./media/active-directory-aadconnect-pass-through-authentication/pta4.png)

Navigáljon a **Azure Active Directory** -> **bejelentkezések** a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/) kattintson egy adott felhasználó bejelentkezési tevékenységét. Keresse meg a **bejelentkezési hiba kódja** mező. A mező értékét leképezése a hiba oka és a megoldás a következő táblázat használatával:

|Bejelentkezési hiba kódja|Jelentkezzen be a hiba oka|Feloldás
| --- | --- | ---
| 50144 | A felhasználó Active Directory jelszava lejárt. | A helyszíni Active Directoryban a felhasználó jelszavának alaphelyzetbe állítása.
| 80001 | Nem érhető el hitelesítési ügynök. | Telepíti és regisztrálja a hitelesítési ügynök.
| 80002 | A hitelesítési ügynök jelszó-érvényesítési kérése túllépte az időkorlátot. | Ellenőrizze, hogy elérhető legyen a hitelesítési ügynök-e az Active Directoryban.
| 80003 | A hitelesítési ügynök érvénytelen választ kapott. | Ha a probléma rendszeresen reprodukálható, több olyan felhasználóra, ellenőrizze az Active Directory konfigurációját.
| 80004 | A bejelentkezési kérésben helytelen egyszerű felhasználónevet (UPN-t) használtak. | Kérje meg a felhasználót, hogy jelentkezzen be a helyes felhasználónevet.
| 80005 | Hitelesítési ügynök: hiba történt. | Átmeneti hiba. Próbálkozzon újra később.
| 80007 | A hitelesítési ügynök nem tudott csatlakozni az Active Directory-hoz. | Ellenőrizze, hogy elérhető legyen a hitelesítési ügynök-e az Active Directoryban.
| 80010 | A hitelesítési ügynök nem tudta visszafejteni a jelszót. | Ha a probléma rendszeresen reprodukálható, telepítse, és regisztráljon egy új hitelesítési ügynök. És távolítsa el a jelenlegivel. 
| 80011 | A hitelesítési ügynök nem tudta lekérni a visszafejtési kulcsot. | Ha a probléma rendszeresen reprodukálható, telepítse, és regisztráljon egy új hitelesítési ügynök. És távolítsa el a jelenlegivel.

## <a name="authentication-agent-installation-issues"></a>Hitelesítési ügynök telepítésével kapcsolatos problémák

### <a name="an-unexpected-error-occurred"></a>Váratlan hiba történt

[Ügynök naplóinak gyűjtése](#collecting-pass-through-authentication-agent-logs) a kiszolgáló és a probléma megoldásához forduljon a Microsoft Support.

## <a name="authentication-agent-registration-issues"></a>Hitelesítési ügynök regisztrációs problémák

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>A hitelesítési ügynök regisztrációja nem sikerült, mert a blokkolt port

Győződjön meg arról, hogy a kiszolgáló, amelyre telepítve van a hitelesítési ügynök kommunikálhatnak-e a szolgáltatás URL-címek és portok listáját [Itt](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>A hitelesítési ügynök regisztrációs jogkivonatot, vagy a fiók hitelesítési hibák miatt nem sikerült

Győződjön meg arról, hogy egy csak felhőalapú globális rendszergazdai fiókkal minden Azure AD Connect vagy különálló hitelesítési ügynök telepítése és regisztrálása az operations. Van egy ismert probléma az MFA-kompatibilis globális rendszergazdai fiókok; Áthidaló megoldásként kapcsolja ki az MFA ideiglenesen (csak a műveletek végrehajtásához).

### <a name="an-unexpected-error-occurred"></a>Váratlan hiba történt

[Ügynök naplóinak gyűjtése](#collecting-pass-through-authentication-agent-logs) a kiszolgáló és a probléma megoldásához forduljon a Microsoft Support.

## <a name="authentication-agent-uninstallation-issues"></a>Hitelesítési ügynök eltávolításának kapcsolatos problémák

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Figyelmeztetés jelenik meg, amikor az Azure AD Connect eltávolítása

Ha az átmenő hitelesítés engedélyezve van a bérlő rendelkezik, és megpróbálja eltávolítani az Azure AD Connect, megjeleníti a következő hibaüzenet: "felhasználók nem fognak tudni jelentkezzen be az Azure AD, ha nincsenek más telepítve átmenő hitelesítési ügynökök más kiszolgálókra."

Győződjön meg arról, hogy a telepítő [magas rendelkezésre álló](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-4-ensure-high-availability) felhasználói bejelentkezés használhatatlanná tévő elkerülése érdekében az Azure AD Connect eltávolítása előtt.

## <a name="issues-with-enabling-the-feature"></a>A szolgáltatás engedélyezési problémái

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>Nem sikerült, mert nincs hitelesítési ügynökök volt elérhető a funkció engedélyezése

Szüksége lesz az átmenő hitelesítés engedélyezése a bérlő legalább egy aktív hitelesítési ügynököt. Hitelesítési ügynök telepítése az Azure AD Connect vagy egy különálló hitelesítési ügynök telepítése.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>A funkció engedélyezése nem sikerült, mert a blokkolt port

Győződjön meg arról, hogy a kiszolgáló, amelyen telepítve van az Azure AD Connect kommunikálhatnak-e a szolgáltatás URL-címek és portok listáját [Itt](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>A funkció engedélyezése a jogkivonatot, vagy a fiók hitelesítési hibák miatt nem sikerült

Ha a funkció engedélyezése egy csak felhőalapú globális rendszergazdai fiók használata érdekében. Egy ismert probléma, és a többtényezős hitelesítés (MFA)-engedélyezve van a globális rendszergazdai fiókok; Áthidaló megoldásként kapcsolja ki az MFA ideiglenesen (csak a a művelet végrehajtásához).

## <a name="collecting-pass-through-authentication-agent-logs"></a>Átmenő hitelesítési ügynök-naplók gyűjtését.

Függően szükség lehet a probléma szükség van a átmenő hitelesítési ügynök naplóinak különböző helyeken.

### <a name="azure-ad-connect-logs"></a>Az Azure AD Connect-naplók

A telepítés kapcsolódó hibák ellenőrizze az Azure AD Connect-naplókat, **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Hitelesítési ügynök eseménynaplók

A hitelesítési ügynök kapcsolatos hibákat, nyissa meg az Eseménynapló alkalmazást a kiszolgálón, és ellenőrizze a **alkalmazás és szolgáltatás Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Részletes elemzés céljából a "Munkamenet" napló engedélyezése. Ez a napló engedélyezve van a normál működés; során az a hitelesítési ügynök nem futnak csak a hibaelhárításhoz használható. A napló tartalmát csak láthatók, miután a naplót újra le van tiltva.

### <a name="detailed-trace-logs"></a>Részletes nyomkövetési naplók

A felhasználói bejelentkezési hibák elhárításához keresse meg a nyomkövetési naplókat, **%ProgramData%\Microsoft\Azure AD Connect hitelesítési Agent\Trace\\**. Ezek a naplók például miért egy adott felhasználó bejelentkezési okokból nem sikerült az átmenő hitelesítés funkciójával. Ezek a hibák is vannak leképezve a bejelentkezési hibák okainak látható az előző [tábla](#sign-in-failure-reasons-on-the-Azure-portal). Következő egy példa naplóbejegyzés:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Részletekért leíró (az előző példában "1328" jelöli) hiba megnyitása a parancssor használatával, és futtassa a következő parancsot (Megjegyzés: "1328" cserélje le a tényleges hiba száma a naplókban megjelenő):

`Net helpmsg 1328`

![Átmenő hitelesítés](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Tartományvezérlő-naplók

Ha a naplózás engedélyezve van, további információ a biztonsági naplók a tartományvezérlők található. Bejelentkezési kérelmek átmenő hitelesítési ügynökök által küldött lekérdezési egy egyszerű módja a következőképpen történik:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Teljesítményfigyelő-számlálók

Hitelesítési ügynökök figyeléséhez egy másik lehetőség, hogy jellemző teljesítményszámlálók nyomon minden kiszolgálón, ahol a hitelesítési ügynök telepítve van. Használja a következő globális számlálók (**# ESP hitelesítések**, **#PTA sikertelen hitelesítések** és **#PTA sikeres hitelesítések**) és a hiba-számlálók (**# ESP hitelesítési hibák**):

![Átmenő hitelesítés Teljesítményfigyelő-számlálók](./media/active-directory-aadconnect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Az átmenő hitelesítés használatával több hitelesítési ügynök, magas rendelkezésre állást biztosít, és _nem_ terheléselosztás. A konfigurációtól függően _nem_ nagyjából kapni minden hitelesítési ügynök _egyenlő_ kérelmek száma. Akkor lehet, hogy egy adott hitelesítési ügynök nem forgalmat fogadó egyáltalán.

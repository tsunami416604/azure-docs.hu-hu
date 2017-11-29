---
title: "Az Azure AD Connect: Áteresztő hitelesítés hibaelhárítása |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure Active Directory (Azure AD) áteresztő hitelesítés hibaelhárítása."
services: active-directory
keywords: "Az Azure AD Connect áteresztő hitelesítés elhárításával kapcsolatos tudnivalókat Active Directory, az Azure AD, SSO, szükséges összetevők telepítése egyszeri bejelentkezést."
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2017
ms.author: billmath
ms.openlocfilehash: 730dfcb8925a1a7edf8796ddaf86d2038fd9df44
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/28/2017
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Az Azure Active Directory áteresztő hitelesítés hibáinak elhárítása

Ez a cikk segít hibaelhárítási Azure AD áteresztő hitelesítés kapcsolatos gyakori problémákat kapcsolatos információkat.

>[!IMPORTANT]
>Felhasználói bejelentkezési problémák átmenő hitelesítéssel küzdenek, ha nem a funkció letiltásához vagy anélkül, hogy egy kizárólag felhőalapú globális rendszergazdai fiók tud visszatérni áteresztő hitelesítés ügynökök eltávolítása. További tudnivalók [csak felhőalapú globális rendszergazdai fiókot hozzá](../active-directory-users-create-azure-portal.md). Ez a lépés elvégzése alapvető fontosságú, és biztosítja, hogy Ön nem ártatlan tévedéssel a bérlő.

## <a name="general-issues"></a>Általános problémák

### <a name="check-status-of-the-feature-and-authentication-agents"></a>A szolgáltatás és a hitelesítési ügynökök állapotának ellenőrzése

Győződjön meg arról, hogy a csatlakoztatott hitelesítési funkció még **engedélyezve** a bérlő és a hitelesítési ügynökök állapotát jeleníti meg **aktív**, és nem **inaktív**. Állapot megnyitásával ellenőrizheti a **az Azure AD Connect** paneljét a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/).

![Az Azure Active Directory felügyeleti központ bemutatása – az Azure AD Connect panel](./media/active-directory-aadconnect-pass-through-authentication/pta7.png)

![Az Azure Active Directory felügyeleti központ – áteresztő hitelesítés panel](./media/active-directory-aadconnect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Felhasználók számára is elérhető bejelentkezési hibaüzenetek

Ha a felhasználó nem tud bejelentkezni átmenő hitelesítést használ, akkor előfordulhat, hogy látni felhasználók számára is elérhető hibák a következők egyikét a az Azure AD bejelentkezési képernyő: 

|Hiba|Leírás|Megoldás:
| --- | --- | ---
|AADSTS80001|Nem sikerült kapcsolódni az Active Directory|Győződjön meg arról, hogy az ügynök kiszolgálók tagjai a felhasználókat, amelyeknek a jelszavánál kell érvényesíteni eltérő AD-erdőben, és Active Directory rendszerhez kapcsolódni tudjanak.  
|AADSTS8002|Időtúllépés történt az Active Directory való kapcsolódás során|Ellenőrizze, hogy biztosítsa, hogy az Active Directory elérhető-e, és az ügynököktől származó válaszol a kérelmekre.
|AADSTS80004|Az ügynök átadott felhasználónév érvénytelen.|Győződjön meg arról, a felhasználó megpróbál bejelentkezni a jobb oldali felhasználónév.
|AADSTS80005|Érvényesítési előre nem látható WebException észlelt|Egy átmeneti hiba. Próbálkozzon újra a kéréssel. Ha azt továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.
|AADSTS80007|Hiba történt az Active Directory kommunikál|Ellenőrizze az ügynök további információt, és győződjön meg arról, hogy a várt módon működik-e az Active Directory.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Bejelentkezési hiba okai a az Azure Active Directory felügyeleti központot (Premium licenc szükséges)

Ha a bérlő az Azure AD Premium licenchez társítva, is megtalálhatja a [bejelentkezési tevékenységei jelentése](../active-directory-reporting-activity-sign-ins.md) a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/).

![Az Azure Active Directory felügyeleti központ – bejelentkezések jelentés](./media/active-directory-aadconnect-pass-through-authentication/pta4.png)

Navigáljon a **Azure Active Directory** -> **bejelentkezések** a a [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/) kattintson egy adott felhasználó bejelentkezési tevékenységet. Keresse meg a **SIGN-IN hibakód** mező. A mező értékének leképezése egy hiba okát is megadva és az alábbi táblázat alapján:

|Bejelentkezési hiba kódja|Bejelentkezési hiba okát is megadva|Megoldás:
| --- | --- | ---
| 50144 | A felhasználó Active Directory jelszava lejárt. | A felhasználó jelszavát a helyszíni Active Directoryban.
| 80001 | Nem érhető el hitelesítési ügynök. | Telepíti és regisztrálja egy hitelesítési ügynök.
| 80002 | A hitelesítési ügynök jelszó-érvényesítési kérése túllépte az időkorlátot. | Annak ellenőrzése, hogy az Active Directory elérhető-e a hitelesítés ügynöktől.
| 80003 | A hitelesítési ügynök érvénytelen választ kapott. | Ha több felhasználó között a probléma rendszeresen reprodukálható, ellenőrizze az Active Directory konfigurációját.
| 80004 | A bejelentkezési kérésben helytelen egyszerű felhasználónevet (UPN-t) használtak. | Kérje meg, hogy jelentkezzen be a felhasználónevet.
| 80005 | Hitelesítési ügynök: hiba történt. | Átmeneti hiba. Próbálkozzon újra később.
| 80007 | A hitelesítési ügynök nem tudott csatlakozni az Active Directory-hoz. | Annak ellenőrzése, hogy az Active Directory elérhető-e a hitelesítés ügynöktől.
| 80010 | A hitelesítési ügynök nem tudta visszafejteni a jelszót. | Ha a probléma rendszeresen reprodukálható, telepíteni és regisztrálni egy új hitelesítési ügynök. És távolítsa el a jelenlegivel. 
| 80011 | A hitelesítési ügynök nem tudta lekérni a visszafejtési kulcsot. | Ha a probléma rendszeresen reprodukálható, telepíteni és regisztrálni egy új hitelesítési ügynök. És távolítsa el a jelenlegivel.

## <a name="authentication-agent-installation-issues"></a>Hitelesítési ügynök telepítésével kapcsolatos problémák

### <a name="an-unexpected-error-occurred"></a>Váratlan hiba történt

[Ügynök naplógyűjtéshez](#collecting-pass-through-authentication-agent-logs) a kiszolgáló és a probléma megoldásához forduljon a Microsoft Support.

## <a name="authentication-agent-registration-issues"></a>Hitelesítési ügynök regisztrációs problémák

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>Letiltott portok miatt nem sikerült a hitelesítési ügynök regisztrálása

Győződjön meg arról, hogy a kiszolgáló, amelyen a hitelesítési ügynök telepítve van a szolgáltatás URL-címek és portok felsorolt kommunikálhatnak [Itt](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>Token vagy fiók hitelesítési hiba miatt nem sikerült a hitelesítési ügynök regisztrálása

Győződjön meg arról, hogy egy csak felhőalapú globális rendszergazdai fiókot használja-e az összes Azure AD Connect vagy önálló hitelesítési ügynök telepítése és regisztrálása az operations. Egy ismert probléma az MFA-kompatibilis globális rendszergazdai fiókok; Kapcsolja ki a multi-factor Authentication ideiglenesen (csak a a művelet elvégzéséhez) megoldás.

### <a name="an-unexpected-error-occurred"></a>Váratlan hiba történt

[Ügynök naplógyűjtéshez](#collecting-pass-through-authentication-agent-logs) a kiszolgáló és a probléma megoldásához forduljon a Microsoft Support.

## <a name="authentication-agent-uninstallation-issues"></a>Hitelesítési ügynök eltávolítása problémák

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Figyelmeztetés jelenik meg, amikor az Azure AD Connect eltávolítása

Ha megpróbálja eltávolítani az Azure AD Connect áteresztő hitelesítés engedélyezve van a tenant, ez azt mutatja meg a következő figyelmeztető üzenet: "felhasználók nem fognak tudni bejelentkezni az Azure AD használni, ha nincs más áteresztő hitelesítés ügynökök telepítve egyéb kiszolgálók".

Győződjön meg arról, hogy a telepítő [magas rendelkezésre álló](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability) felhasználói bejelentkezés továbbra is működni fognak az Azure AD Connect eltávolítása előtt.

## <a name="issues-with-enabling-the-feature"></a>A funkció engedélyezése problémákat

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>A funkció nem sikerült, mert nem volt elérhető az egyetlen hitelesítési ügynökök engedélyezése

Legalább egy aktív hitelesítési ügynök a bérlő átmenő hitelesítést kell. Egy hitelesítési ügynök telepítése az Azure AD Connect, vagy egy önálló hitelesítési ügynök telepítése.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>A funkció engedélyezése a blokkolt portok miatt nem sikerült

Győződjön meg arról, hogy a kiszolgáló, amelyen telepítve van az Azure AD Connect kommunikálhatnak-e a szolgáltatás URL-címek és portok felsorolt [Itt](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-1-check-the-prerequisites).

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>A funkció engedélyezése token vagy fiók hitelesítési hiba miatt nem sikerült

Győződjön meg arról, hogy egy kizárólag felhőalapú globális rendszergazdai fiók, ha engedélyezi a szolgáltatást. Egy ismert probléma, és a többtényezős hitelesítés (MFA)-engedélyezve van a globális rendszergazdai fiókok; Kapcsolja ki a multi-factor Authentication ideiglenesen (csak a a művelet befejezéséhez) megoldás.

## <a name="exchange-activesync-configuration-issues"></a>Exchange ActiveSync konfigurációs problémák

Ezek a gyakori problémák az Exchange ActiveSync támogatja a csatlakoztatott hitelesítés konfigurálásakor.

### <a name="exchange-powershell-issue"></a>Exchange PowerShell-problémát

Ha megjelenik a "**paraméter nem található egyező"PerTenantSwitchToESTSEnabled"paraméternév\.**" Hiba, ha futtatja a `Set-OrganizationConfig` Exchange PowerShell paranccsal, forduljon a Microsoft Support.

### <a name="exchange-activesync-not-working"></a>Exchange ActiveSync nem működik

A konfigurációs bizonyos idő érvénybe - környezettől függ az adott időszakban. Ha hosszú ideig a helyzet továbbra is fennáll, forduljon a Microsoft Support.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Naplózza az áteresztő hitelesítés gyűjtését ügynök

Előfordulhat, hogy probléma típusától függően szükség van az áteresztő hitelesítés ügynök naplók különböző helyeken.

### <a name="azure-ad-connect-logs"></a>Az Azure AD Connect naplói

A telepítés hibáinak ellenőrizze az Azure AD Connect naplózásra kerül **%ProgramData%\AADConnect\trace-\*.log**.

### <a name="authentication-agent-event-logs"></a>Hitelesítési ügynök eseménynaplók

A hitelesítési ügynök kapcsolatos hibákat, nyissa meg az Eseménynapló alkalmazást, a kiszolgálón, és ellenőrizze a **alkalmazás és szolgáltatás Logs\Microsoft\AzureAdConnect\AuthenticationAgent\Admin**.

Részletes elemzéséhez engedélyezze a "munkamenetnapló". Ne futtassa a hitelesítési ügynök a napló engedélyezve; a normál műveletek során a csak a hibaelhárításhoz használja. A napló tartalmát csak láthatók, miután a napló újra le van tiltva.

### <a name="detailed-trace-logs"></a>Részletes nyomkövetési naplók

Felhasználói bejelentkezési hibák elhárításához tekintse meg a következő nyomkövetési naplók **%ProgramData%\Microsoft\Azure AD Connect hitelesítési Agent\Trace\\**. Ezek a naplók tartalmaznak okok miért egy adott felhasználói bejelentkezés nem sikerült az áteresztő hitelesítés funkció használata. Ezek a hibák is van leképezve a bejelentkezési hiba oka az előző látható [tábla](#sign-in-failure-reasons-on-the-Azure-portal). Az alábbiakban látható egy példa naplóbejegyzés:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

Nyissa meg a parancssort és futtassa a következő parancsot is ki lehet tartalmazza a hiba (az előző példában a "1328" jelöli) leíró adatait (Megjegyzés: "1328" cserélje le a tényleges hiba száma, melyek megjelennek a naplókban):

`Net helpmsg 1328`

![Átmenő hitelesítés](./media/active-directory-aadconnect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Tartományvezérlő naplói

Ha a naplózás engedélyezve van, további információk találhatók a biztonsági naplók a tartományvezérlők. Bejelentkezési kérelmek áteresztő hitelesítés-ügynökök által küldött lekérdezési egyszerű módját a következőképpen történik:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

### <a name="performance-monitor-counters"></a>Teljesítményszámlálók

Egy másik hitelesítési ügynökök figyelése módja specifikus teljesítményszámlálók nyomon minden kiszolgálón, amelyen a hitelesítési ügynök telepítve van. A következő globális számlálókat (**# ESP hitelesítések**, **#PTA sikertelen hitelesítés** és **#PTA sikeres hitelesítések**) és a hiba számlálók (**# ESP hitelesítési hibák**):

![Áteresztő hitelesítés Teljesítményfigyelő-számlálókból](./media/active-directory-aadconnect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Áteresztő hitelesítés több hitelesítési ügynököt használ, magas rendelkezésre állást biztosít, és _nem_ terheléselosztás. A konfigurációtól függően _nem_ a hitelesítési ügynökök körülbelül kap _egyenlő_ kérelmek száma. Akkor lehet, hogy egy adott hitelesítési ügynök minden megkapja-e sincs forgalom.

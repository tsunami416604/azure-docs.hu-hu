---
title: 'Azure AD Connect: Áthaladási hitelesítés hibaelhárítása | Microsoft dokumentumok'
description: Ez a cikk ismerteti, hogyan hárítsa el az Azure Active Directory (Azure AD) átmenő hitelesítés.
services: active-directory
keywords: Az Azure AD Connect áthaladási hitelesítésének hibaelhárítása, az Active Directory telepítése, az Azure AD, egyszeri bejelentkezés, egyszeri bejelentkezés szükséges összetevőinek telepítése
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae83cea866367fa6a6596caa683d0287bea96c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60456174"
---
# <a name="troubleshoot-azure-active-directory-pass-through-authentication"></a>Az Azure Active Directory átmenő hitelesítésének hibaelhárítása

Ez a cikk segítséget nyújt az Azure AD áthaladási hitelesítéssel kapcsolatos gyakori problémákkal kapcsolatos hibaelhárítási információk kereséséhez.

>[!IMPORTANT]
>Ha felhasználói bejelentkezési problémákkal szembesül az átmenő hitelesítéssel kapcsolatban, ne tiltsa le a szolgáltatást, és ne távolítsa el az átmenő hitelesítési ügynököket anélkül, hogy csak felhőalapú globális rendszergazdai fiókkal rendelkezne. További információ [a csak felhőalapú globális rendszergazdai fiók hozzáadásáról.](../active-directory-users-create-azure-portal.md) Ez a lépés kritikus fontosságú, és biztosítja, hogy nem kap kizárva a bérlő.

## <a name="general-issues"></a>Általános problémák

### <a name="check-status-of-the-feature-and-authentication-agents"></a>A szolgáltatás és a hitelesítési megbízottak állapotának ellenőrzése

Győződjön meg arról, hogy az átadó hitelesítés funkció továbbra is **engedélyezve** van a bérlőn, és a hitelesítési ügynökök állapota **aktív**és nem **inaktív állapotot**mutat. Az állapotát az [Azure Active Directory felügyeleti központ](https://aad.portal.azure.com/)Azure **AD Connect** paneljének megnyitásával ellenőrizheti.

![Azure Active Directory felügyeleti központ – Az Azure AD Connect panel](./media/tshoot-connect-pass-through-authentication/pta7.png)

![Azure Active Directory felügyeleti központ – Áthaladási hitelesítés panel](./media/tshoot-connect-pass-through-authentication/pta11.png)

### <a name="user-facing-sign-in-error-messages"></a>Felhasználó felé néző bejelentkezési hibaüzenetek

Ha a felhasználó nem tud bejelentkezni az átmenő hitelesítés használatával, az alábbi felhasználói hibák egyikét láthatja az Azure AD bejelentkezési képernyőjén: 

|Hiba|Leírás|Megoldás:
| --- | --- | ---
|AADSTS80001|Nem lehet csatlakozni az Active Directoryhoz|Győződjön meg arról, hogy az ügynökkiszolgálók ugyanannak az AD-erdőnek a tagjai, mint azoknak a felhasználóknak, akiknek a jelszavát ellenőrizni kell, és csatlakozni tudnak az Active Directoryhoz.  
|AADSTS8002|Időtúltöltés történt az Active Directoryhoz való csatlakozáskor|Ellenőrizze, hogy az Active Directory elérhető-e, és válaszol-e az ügynökök kéréseire.
|AADSTS80004|Az ügynöknek átadott felhasználónév érvénytelen.|Győződjön meg arról, hogy a felhasználó a megfelelő felhasználónévvel próbál bejelentkezni.
|AADSTS80005|Előre nem látható webkivétel ellenőrzése|Átmeneti hiba. Próbálkozzon újra a kéréssel. Ha továbbra is sikertelen, forduljon a Microsoft támogatási szolgálatához.
|AADSTS80007|Hiba történt az Active Directoryval való kommunikáció során|Ellenőrizze az ügynök naplók további információt, és ellenőrizze, hogy az Active Directory működik a várt módon.

### <a name="sign-in-failure-reasons-on-the-azure-active-directory-admin-center-needs-premium-license"></a>Bejelentkezési hibák okai az Azure Active Directory felügyeleti központban (prémium szintű licencre van szüksége)

Ha a bérlő rendelkezik egy Azure AD [Premium-licenc](../reports-monitoring/concept-sign-ins.md) társítva, akkor is tekintse meg a bejelentkezési tevékenység jelentést az [Azure Active Directory felügyeleti központ.](https://aad.portal.azure.com/)

![Azure Active Directory felügyeleti központ – Bejelentkezések jelentés](./media/tshoot-connect-pass-through-authentication/pta4.png)

Keresse meg az **Azure Active Directory** -> **bejelentkezések** az [Azure Active Directory felügyeleti központban,](https://aad.portal.azure.com/) és kattintson egy adott felhasználó bejelentkezési tevékenység. Keresse meg a **SIGN-IN ERROR CODE mezőt.** A mező értékét az alábbi táblázat segítségével rendelje hozzá a hiba okához és megoldásához:

|Bejelentkezési hibakód|Bejelentkezési hiba oka|Megoldás:
| --- | --- | ---
| 50144 | A felhasználó Active Directory jelszava lejárt. | Állítsa alaphelyzetbe a felhasználó jelszavát a helyszíni Active Directoryban.
| 80001 | Nem érhető el hitelesítési ügynök. | Hitelesítési ügynök telepítése és regisztrálása.
| 80002 | A hitelesítési ügynök jelszó-érvényesítési kérése túllépte az időkorlátot. | Ellenőrizze, hogy az Active Directory elérhető-e a hitelesítési ügynökből.
| 80003 | A hitelesítési ügynök érvénytelen választ kapott. | Ha a probléma következetesen több felhasználó számára is megismételhető, ellenőrizze az Active Directory konfigurációját.
| 80004 | A bejelentkezési kérésben helytelen egyszerű felhasználónevet (UPN-t) használtak. | Kérje meg a felhasználót, hogy a megfelelő felhasználónévvel jelentkezzen be.
| 80005 | Hitelesítési ügynök: hiba történt. | Átmeneti hiba. Próbálkozzon újra később.
| 80007 | A hitelesítési ügynök nem tudott csatlakozni az Active Directory-hoz. | Ellenőrizze, hogy az Active Directory elérhető-e a hitelesítési ügynökből.
| 80010 | A hitelesítési ügynök nem tudta visszafejteni a jelszót. | Ha a probléma következetesen reprodukálható, telepítsen és regisztráljon egy új hitelesítési ügynököt. És távolítsa el a jelenlegit. 
| 80011 | A hitelesítési ügynök nem tudta lekérni a visszafejtési kulcsot. | Ha a probléma következetesen reprodukálható, telepítsen és regisztráljon egy új hitelesítési ügynököt. És távolítsa el a jelenlegit.

>[!IMPORTANT]
>Az átadó hitelesítési ügynökök úgy hitelesítik az Azure AD-felhasználókat, hogy a [Win32 LogonUser API-t](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx)meghívja az Active Directoryhoz. Ennek eredményeképpen, ha az Active Directory "Bejelentkezési" beállítását állította be a munkaállomás-bejelentkezési hozzáférés korlátozására, akkor az átmenő hitelesítési ügynököket tartalmazó kiszolgálókat is hozzá kell adnia a "Bejelentkezési" kiszolgálók listájához. Ennek elmulasztása megakadályozza, hogy a felhasználók bejelentkeznek az Azure AD-be.

## <a name="authentication-agent-installation-issues"></a>Hitelesítési ügynök telepítési problémái

### <a name="an-unexpected-error-occurred"></a>Váratlan hiba történt

Gyűjtse össze az [ügyintézői naplókat](#collecting-pass-through-authentication-agent-logs) a kiszolgálóról, és lépjen kapcsolatba a Microsoft támogatási szolgálatával a problémával kapcsolatban.

## <a name="authentication-agent-registration-issues"></a>Hitelesítési ügynök regisztrációs problémái

### <a name="registration-of-the-authentication-agent-failed-due-to-blocked-ports"></a>A hitelesítési ügynök regisztrációja blokkolt portok miatt nem sikerült

Győződjön meg arról, hogy az a kiszolgáló, amelyre a hitelesítési ügynök telepítve van, képes kommunikálni az [itt](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)felsorolt szolgáltatási URL-címekkel és portokkal.

### <a name="registration-of-the-authentication-agent-failed-due-to-token-or-account-authorization-errors"></a>A hitelesítési ügynök regisztrációja token- vagy fiókengedélyezési hibák miatt nem sikerült

Győződjön meg arról, hogy csak felhőalapú globális rendszergazdai fiókot használ az összes Azure AD Connect vagy önálló hitelesítési ügynök telepítési és regisztrációs művelethez. Ismert probléma van az MFA-kompatibilis globális rendszergazdai fiókokkal; ideiglenesen kikapcsolja az MFA-t (csak a műveletek befejezéséhez) kerülő megoldásként.

### <a name="an-unexpected-error-occurred"></a>Váratlan hiba történt

Gyűjtse össze az [ügyintézői naplókat](#collecting-pass-through-authentication-agent-logs) a kiszolgálóról, és lépjen kapcsolatba a Microsoft támogatási szolgálatával a problémával kapcsolatban.

## <a name="authentication-agent-uninstallation-issues"></a>A hitelesítési ügynök eltávolítási problémái

### <a name="warning-message-when-uninstalling-azure-ad-connect"></a>Figyelmeztető üzenet az Azure AD Connect eltávolításakor

Ha engedélyezve van az átmenő hitelesítés a bérlőn, és megpróbálja eltávolítani az Azure AD Connectet, az a következő figyelmeztető üzenetet jeleníti meg: "A felhasználók csak akkor tudnak bejelentkezni az Azure AD-be, ha más átmenő hitelesítési ügynökök vannak telepítve más szervereken."

Győződjön meg arról, hogy a beállítás [magas rendelkezésre állású,](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability) mielőtt eltávolítané az Azure AD Connectet, hogy elkerülje a felhasználói bejelentkezés t.

## <a name="issues-with-enabling-the-feature"></a>A szolgáltatás engedélyezésével kapcsolatos problémák

### <a name="enabling-the-feature-failed-because-there-were-no-authentication-agents-available"></a>A szolgáltatás engedélyezése nem sikerült, mert nem volt elérhető hitelesítési ügynök

Legalább egy aktív hitelesítési ügynökkel kell rendelkeznie ahhoz, hogy a bérlőn engedélyezze az átmenő hitelesítést. A hitelesítési ügynök telepítése az Azure AD Connect vagy egy önálló hitelesítési ügynök telepítésével telepíthető.

### <a name="enabling-the-feature-failed-due-to-blocked-ports"></a>A szolgáltatás engedélyezése blokkolt portok miatt nem sikerült

Győződjön meg arról, hogy az a kiszolgáló, amelyre az Azure AD Connect telepítve van, képes kommunikálni az [itt](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)felsorolt szolgáltatási URL-címekkel és portokkal.

### <a name="enabling-the-feature-failed-due-to-token-or-account-authorization-errors"></a>A szolgáltatás engedélyezése token- vagy fiókengedélyezési hibák miatt nem sikerült

Győződjön meg arról, hogy csak felhőalapú globális rendszergazdai fiókot használ a szolgáltatás engedélyezésekor. Ismert probléma van a többtényezős hitelesítést (MFA) engedélyező globális rendszergazdai fiókokkal; ideiglenesen kikapcsolja az MFA-t (csak a művelet befejezéséhez) kerülő megoldásként.

## <a name="collecting-pass-through-authentication-agent-logs"></a>Áthaladási hitelesítési ügynök naplóinak gyűjtése

Attól függően, hogy milyen típusú probléma lehet, meg kell keresni a különböző helyeken átmenő hitelesítési ügynök naplók.

### <a name="azure-ad-connect-logs"></a>Az Azure AD Connect naplói

A telepítéssel kapcsolatos hibákat az Azure AD Connect naplóiban a **%ProgramData%\AADConnect\trace-\*.log mappában.**

### <a name="authentication-agent-event-logs"></a>Hitelesítési ügynök eseménynaplói

A hitelesítési ügynökkel kapcsolatos hibákért nyissa meg az Eseménynapló alkalmazást a kiszolgálón, és ellenőrizze az **Alkalmazás- és szolgáltatásnaplók\Microsoft\AzureAdConnect\AuthenticationAgent\Admin mappában.**

A részletes elemzéshez engedélyezze a "Munkamenet" naplót (kattintson a jobb gombbal az Eseménynapló alkalmazáson belül, hogy megtalálja ezt a lehetőséget). Ne futtassa a hitelesítési ügynököt úgy, hogy ez a napló engedélyezve van a normál műveletek során; csak hibaelhárításhoz használható. A napló tartalma csak akkor látható, ha a napló újra le van tiltva.

### <a name="detailed-trace-logs"></a>Részletes nyomkövetési naplók

A felhasználói bejelentkezési hibák elhárításához keresse meg a nyomkövetési naplókat a **következő helyen:\\%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace**. Ezek a naplók tartalmazzák az okokat, amelyek miatt egy adott felhasználói bejelentkezés sikertelen volt az átmenő hitelesítés i. Ezek a hibák is le vannak képezve az előző bejelentkezési hiba okai táblázatban látható bejelentkezési hibák okai. A következő egy példa naplóbejegyzés:

```
    AzureADConnectAuthenticationAgentService.exe Error: 0 : Passthrough Authentication request failed. RequestId: 'df63f4a4-68b9-44ae-8d81-6ad2d844d84e'. Reason: '1328'.
        ThreadId=5
        DateTime=xxxx-xx-xxTxx:xx:xx.xxxxxxZ
```

A hiba leíró részleteit (az előző példában"1328) a parancssor megnyitásával és a következő parancs futtatásával kaphatja meg (Megjegyzés: Cserélje le az "1328" értéket a naplókban látható tényleges hibaszámra):

`Net helpmsg 1328`

![Átmenő hitelesítés](./media/tshoot-connect-pass-through-authentication/pta3.png)

### <a name="domain-controller-logs"></a>Tartományvezérlő naplói

Ha a naplózás engedélyezve van, további információk találhatók a tartományvezérlők biztonsági naplóiban. Az átmenő hitelesítési ügynökök által küldött bejelentkezési kérelmek lekérdezésének egyszerű módja a következő:

```
    <QueryList>
    <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>
    </Query>
    </QueryList>
```

## <a name="performance-monitor-counters"></a>Teljesítményfigyelő számlálói

A hitelesítési ügynökök figyelésének másik módja, ha nyomon követi a teljesítményfigyelő egyes számlálóit minden olyan kiszolgálón, ahol a hitelesítési ügynök telepítve van. Használja a következő globális számlálókat (**# PTA-hitelesítések**, **#PTA sikertelen hitelesítések** és **#PTA sikeres hitelesítések)** és hibaszámlálókat **(# PTA hitelesítési hibák):**

![Átmenő hitelesítési teljesítményfigyelő számlálók](./media/tshoot-connect-pass-through-authentication/pta12.png)

>[!IMPORTANT]
>Az átmenő hitelesítés magas rendelkezésre állást biztosít több hitelesítési ügynök használatával, _és nem_ terheléselosztást. A konfigurációtól függően _nem_ minden hitelesítési ügynök kap nagyjából _azonos_ számú kérelmet. Lehetséges, hogy egy adott hitelesítési ügynök egyáltalán nem kap forgalmat.

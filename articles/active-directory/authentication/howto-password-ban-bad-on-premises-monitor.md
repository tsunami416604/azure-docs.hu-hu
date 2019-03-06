---
title: Figyelés és naplózás az Azure AD jelszóvédelem előzetes verzióban érhető el
description: Megismerheti az Azure AD jelszó-védelmi figyelés és naplózás
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1f946eceab68b0fe49ea67dc33a7094458cd14c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451486"
---
# <a name="preview-azure-ad-password-protection-monitoring-and-logging"></a>Előzetes verzió: Az Azure AD jelszóvédelem figyelés és naplózás

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Az Azure AD jelszóvédelem központi telepítését, miután a figyelési és jelentéskészítési elvégzendő alapvető feladatok áll. Ez a cikk részletezik segítségével megismerkedett a különböző figyelési technikák, többek között, ahol minden szolgáltatás információkat naplózza, és az Azure AD jelszóvédelem használatát jelentése.

# <a name="dc-agent-event-logging"></a>Tartományvezérlő ügynök események naplózása

Minden tartományvezérlőn a tartományvezérlő szolgáltatás ügynökszoftver minden egyes jelszó-ellenőrzési művelet (és más állapota) eredményét ír egy helyi Eseménynapló:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

A tartományvezérlő az ügynök felügyeleti napló az információkat a szoftver működése hogyan elsődleges forrása.

Vegye figyelembe, hogy a nyomkövetési napló alapértelmezés szerint ki van kapcsolva.

A következő tartományokra tartoznak a különböző DC agent-összetevők által naplózott eseményeket:

|Összetevő |Esemény tartomány|
| --- | --- |
|Tartományvezérlő ügynök jelszó szűrő dll| 10000-19999|
|Tartományvezérlő ügynök szolgáltatás üzemeltetési folyamata| 20000-29999|
|Tartományvezérlő ügynök szolgáltatás házirend ellenőrzési logika| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Tartományvezérlő az ügynök felügyeleti Eseménynapló

### <a name="password-validation-outcome-events"></a>Jelszó ellenőrzés eredménye események

Minden tartományvezérlőn a tartományvezérlő szolgáltatás ügynökszoftver ír minden egyes jelszó érvényesítése eredményeit a tartományvezérlő az ügynök felügyeleti Eseménynapló.

Sikeres jelszó-ellenőrzési művelet esetén nincs általában egy eseményt naplózza a tartományvezérlő ügynök jelszó szűrő DLL-ben. A sikertelen jelszó-ellenőrzési művelet, nincsenek általában két naplózott eseményeket, egy a DC-ügynök szolgáltatás, és egy, a tartományvezérlő ügynök jelszó szűrő dll.

Ezekben a helyzetekben rögzítéséhez diszkrét eseményeket naplózza, az alábbi tényezők alapján:

* E folyamatban van, a megadott jelszó megadása vagy módosítása.
* E egy adott a jelszó érvényesítése sikeres és sikertelen.
* E érvényesítése nem sikerült, mert a Microsoft globális szabályzatot, a szervezeti házirend, vagy kombinálja.
* E csak naplózási mód jelenleg be- vagy kikapcsolása az aktuális jelszó házirend.

A kulcs jelszó-érvényesítési-események a következők:

|   |Jelszó módosítása |Jelszó beállítása|
| --- | :---: | :---: |
|Sikeres |10014 |10015|
|(A customer jelszóházirend) miatt sikertelen| 10016, 30002| 10017, 30003|
|(A Microsoft jelszóházirend) miatt sikertelen| 10016, 30004| 10017, 30005|
|(A kombinált a Microsoft és a felhasználói jelszó-szabályzatokat) miatt sikertelen| 10016, 30026| 10017, 30027|
|Csak naplózási Pass (végrehajtása nem sikerül ügyfél jelszóházirend)| 10024, 30008| 10025, 30007|
|Csak naplózási Pass (végrehajtása nem sikerül a Microsoft jelszóházirend)| 10024, 30010| 10025, 30009|
|Csak naplózási Pass (végrehajtása nem sikerül kombinált a Microsoft és az ügyfél jelszóházirendek)| 10024, 30028| 10025, 30029|

A fenti táblázatban szereplő esetekben hivatkozó "kombinált szabályzatok" olyan helyzetekben, ahol a felhasználó jelszavát is le van tiltva a Microsoft jelszó listájából, és a le van tiltva a felhasználói jelszó listában legalább egy jogkivonatot tartalmaz található hivatkozunk.

Naplózásakor események párjai együtt a mindkét események explicit módon a azonos CorrelationId sablonkonfigurációkat vannak társítva.

### <a name="password-validation-summary-reporting-via-powershell"></a>PowerShell-lel jelentéskészítési jelszó érvényesítés összegzése

A `Get-AzureADPasswordProtectionSummaryReport` parancsmag is használható előállításához a jelszó érvényesítése tevékenység összegzését. Egy példa a parancsmag kimenete a következőképpen történik:

```PowerShell
Get-AzureADPasswordProtectionSummaryReport -DomainController bplrootdc2
DomainController                : bplrootdc2
PasswordChangesValidated        : 6677
PasswordSetsValidated           : 9
PasswordChangesRejected         : 10868
PasswordSetsRejected            : 34
PasswordChangeAuditOnlyFailures : 213
PasswordSetAuditOnlyFailures    : 3
PasswordChangeErrors            : 0
PasswordSetErrors               : 1
```

A parancsmag reporting hatóköre egy befolyásolhatja a – erdő, - tartomány vagy -DomainController paraméterek. Nem a paraméterek megadása azt jelenti, – erdőben.

A `Get-AzureADPasswordProtectionSummaryReport` parancsmag működik, a tartományvezérlő az ügynök felügyeleti Eseménynapló lekérdezéséhez és majd számbavételi megjelenített eredmény kategóriákhoz megfelelő események teljes száma. Az alábbi táblázat tartalmazza az egyes serkenti az eredményt és a megfelelő esemény azonosítója közötti leképezéseket:

|Get-AzureADPasswordProtectionSummaryReport property |Megfelelő esemény azonosítója|
| :---: | :---: |
|PasswordChangesValidated |10014|
|PasswordSetsValidated |10015|
|PasswordChangesRejected |10016|
|PasswordSetsRejected |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors |10012|
|PasswordSetErrors |10013|

Vegye figyelembe, hogy a `Get-AzureADPasswordProtectionSummaryReport` parancsmag PowerShell parancsfájl formátumban tartalmazza a szükséges és május szükség lehet rá hivatkozni, közvetlenül a következő helyen:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Ez a parancsmag minden olyan tartományvezérlőre, amely egy PowerShell-munkamenet megnyitásával működik. Annak érdekében, hogy sikeres legyen, PowerShell távoli munkamenet-támogatás minden tartományvezérlőn engedélyezni kell, és az ügyfél megfelelő jogosultságokkal kell rendelkeznie. PowerShell távoli munkamenet követelményeiről további információkért futtassa a "Get-Help about_Remote_Troubleshooting" PowerShell-ablakban.

> [!NOTE]
> Ez a parancsmag minden tartományvezérlő ügynökszolgáltatás rendszergazdai Eseménynapló távolról lekérdezésével működik. Ha az eseménynaplókban események nagy számú, a parancsmag végrehajtásához hosszú időt vehet igénybe. Emellett tömeges hálózati lekérdezések célja nagyméretű adathalmazok hatással lehet a tartományvezérlő teljesítményét. Ezért ez a parancsmag használandó gondosan az éles környezetben.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Minta eseménynapló-üzenet az eseményazonosító 10014 (sikeres jelszó módosítása)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Minta eseménynapló-üzenet az eseményazonosító 10017 és 30003 (sikertelen jelszómegadási set)

10017:

```text
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```text
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Minta eseménynapló-üzenet az eseményazonosító 30001 (jelszó miatt nincs elérhető házirend elfogadja)

```text
The password for the specified user was accepted because an Azure password policy is not available yet

UserName: SomeUser
FullName: Some User

This condition may be caused by one or more of the following reasons:%n

1. The forest has not yet been registered with Azure.

   Resolution steps: an administrator must register the forest using the Register-AzureADPasswordProtectionForest cmdlet.

2. An Azure AD password protection Proxy is not yet available on at least one machine in the current forest.

   Resolution steps: an administrator must install and register a proxy using the Register-AzureADPasswordProtectionProxy cmdlet.

3. This DC does not have network connectivity to any Azure AD password protection Proxy instances.

   Resolution steps: ensure network connectivity exists to at least one Azure AD password protection Proxy instance.

4. This DC does not have connectivity to other domain controllers in the domain.

   Resolution steps: ensure network connectivity exists to the domain.
```

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Minta eseménynapló-üzenet az eseményazonosító 30006 (új szabályzat van érvényben)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>Minta eseménynapló-üzenet az eseményazonosító 30019 (a Azure AD jelszó-védelem le van tiltva)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>Tartományvezérlő az ügynök műveleti napló

A tartományvezérlő-ügynökszolgáltatás is naplózza az eseményeket üzemeltetési vonatkozású, a következő log:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Tartományvezérlő ügynök nyomkövetési napló

A tartományvezérlő-ügynökszolgáltatás is jelentkezhetnek hibakeresési szintűre részletes nyomkövetési események a következő naplófájl:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Nyomkövetési adatainak naplózása alapértelmezés szerint le van tiltva.

> [!WARNING]
> Ha engedélyezve van, a nyomkövetési napló nagy mennyiségű esemény kap, és hatással lehet a tartományvezérlő teljesítményét. Ezért a továbbfejlesztett napló kell csak akkor engedélyezhető, ha probléma mélyebb vizsgálatra van szükség, és akkor is csak egy minimális időtartamot.

## <a name="dc-agent-text-logging"></a>Tartományvezérlő ügynök szöveges naplózás

A tartományvezérlő-ügynök szolgáltatás beállítható úgy, hogy egy szöveges napló írni, állítsa a következő beállításazonosítót:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

Szöveges naplózás alapértelmezés szerint le van tiltva. A tartományvezérlő-ügynök szolgáltatás újraindításra szükség a módosítások érvénybe léptetéséhez ezt az értéket. Ha engedélyezve van a tartományvezérlő ügynökszolgáltatás fog kiírni, egy naplófájlba alatt található:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> A szöveges napló kap a nyomkövetési naplónak naplózható hibakeresési szintűre bejegyzések, de általában egyszerűbb formátumban áttekintéséhez és elemzéséhez.

> [!WARNING]
> Ha engedélyezve van, ez a napló nagy mennyiségű esemény fogadása, és hatással lehet a tartományvezérlő teljesítményét. Ezért a továbbfejlesztett napló kell csak akkor engedélyezhető, ha probléma mélyebb vizsgálatra van szükség, és akkor is csak egy minimális időtartamot.

## <a name="dc-agent-performance-monitoring"></a>Tartományvezérlő ügynök alkalmazásteljesítmény-figyelés

A tartományvezérlő ügynök szolgáltatás szoftver telepítése a teljesítményszámláló objektum neve **Azure AD jelszóvédelem**. Az alábbi teljesítményszámlálók jelenleg érhetők el:

|Teljesítményoptimalizált számláló neve | Leírás|
| --- | --- |
|Feldolgozott jelszavak |Ez a számláló utolsó újraindítás óta (elfogad vagy elutasít) feldolgozott jelszavak teljes számát jeleníti meg.|
|Jelszavak elfogadva |Ez a számláló a teljes száma, hogy a rendszer elfogadta az utolsó újraindítás óta jeleníti meg.|
|Elutasított jelszavak |Ez a számláló a teljes száma, hogy a rendszer elutasította az utolsó újraindítás óta jeleníti meg.|
|Jelszó kérelmek szűrése folyamatban |Ez a számláló jeleníti meg a jelszó-kérelmek szűrése jelenleg folyamatban van.|
|Csúcsidőszak jelszó kérelmek szűrése |Ez a számláló egyidejű jelszó szűrő kérelmek maximális száma az utolsó újraindítás óta jeleníti meg.|
|Jelszó szűrő kérelem hibák |Ez a számláló a jelszó kérelmek szűrése, amely egy hiba miatt sikertelen volt az utolsó újraindítás óta teljes számát jeleníti meg. Hibák akkor fordulhatnak elő, ha az Azure AD-jelszó Protection tartományvezérlő ügynökszolgáltatás nem fut.|
|Jelszó szűrő vonatkozó kérelmek/másodperc |Ez a számláló a sebességet, milyen jelszavak feldolgozott jeleníti meg.|
|Jelszó szűrő kérelem feldolgozási ideje |Ez a számláló a jelszó szűrő kérelmek feldolgozásához szükséges átlagos időt jeleníti meg.|
|Csúcsidőszak jelszó szűrő kérelem feldolgozási ideje |Ez a számláló a csúcsidőszak jelszó szűrő kérelem feldolgozása az utolsó újraindítás óta eltelt idő jeleníti meg.|
|Jelszavak elfogadott miatt mód naplózása |Ez a számláló a teljes száma, hogy lenne általában elutasított, de a rendszer elfogadta, mert a jelszóházirend úgy lett konfigurálva, hogy rendszervizsgálati módban (az utolsó újraindítás) óta jeleníti meg.|

## <a name="dc-agent-discovery"></a>DC-ügynök felderítése

A `Get-AzureADPasswordProtectionDCAgent` parancsmag is használható a különböző DC ügynökök futtatása a tartomány vagy erdő alapvető információit jeleníti meg. Ez az információ veszi át a serviceConnectionPoint objektumok a futó tartományvezérlő ügynök szolgáltatás(ok) által regisztrált.

Egy példa a parancsmag kimenete a következőképpen történik:

```PowerShell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

Minden egyes tartományvezérlő ügynökszolgáltatás hozzávetőleges számítónk különböző tulajdonságait frissíti. Az adatok a továbbra is az Active Directory replikáció késése.

A parancsmag lekérdezés hatókörét használatával befolyásolhatja a – erdőben vagy -Domain paraméterek.

Ha a HeartbeatUTC érték elavult, ez lehet az Azure AD-jelszó Protection tartományvezérlő ügynök tartományvezérlő nem fut, vagy el lett távolítva, vagy a gép le lett fokozva, és már nem tartományvezérlő.

Ha a PasswordPolicyDateUTC érték beolvasása, elavult, ez lehet, amely rendelkezik az Azure AD-jelszó DC védelmi ügynököt, hogy a gép nem működik megfelelően.

## <a name="proxy-service-event-logging"></a>Proxy szolgáltatás események naplózása

A Proxy szolgáltatást az alábbi eseménynaplókból események minimális számú bocsát ki:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Vegye figyelembe, hogy a nyomkövetési napló alapértelmezés szerint ki van kapcsolva.

> [!WARNING]
> Ha engedélyezve van, a nyomkövetési napló fogadja-e nagy mennyiségű esemény, és hatással lehet a proxyállomás teljesítményét. Ezért ez a napló kell csak akkor engedélyezhető, ha a probléma mélyebb vizsgálatra van szükség, és akkor is csak egy minimális időtartamot.

A különböző Proxy-összetevők használatával a következő tartományokra által naplózott eseményeket:

|Összetevő |Esemény tartomány|
| --- | --- |
|Proxy szolgáltatás üzemeltetési folyamata| 10000-19999|
|Proxy szolgáltatás alapvető üzleti logika| 20000-29999|
|PowerShell-parancsmagok| 30000-39999|

## <a name="proxy-service-text-logging"></a>Proxy szolgáltatás szöveges naplózás

A Proxy szolgáltatás beállítható úgy, hogy egy szöveges napló írni, állítsa a következő beállításazonosítót:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (REG_DWORD érték)

Szöveges naplózás alapértelmezés szerint le van tiltva. A Proxy szolgáltatás újraindításra szükség a módosítások érvénybe léptetéséhez ezt az értéket. Ha a szolgáltatás alatt naplófájlt fogja írni a Proxy engedélyezve:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> A szöveges napló kap a nyomkövetési naplónak naplózható hibakeresési szintűre bejegyzések, de általában egyszerűbb formátumban áttekintéséhez és elemzéséhez.

> [!WARNING]
> Ha engedélyezve van, ez a napló nagy mennyiségű esemény fogadása, és hatással lehetnek a számítógép teljesítményére. Ezért a továbbfejlesztett napló kell csak akkor engedélyezhető, ha probléma mélyebb vizsgálatra van szükség, és akkor is csak egy minimális időtartamot.

## <a name="powershell-cmdlet-logging"></a>PowerShell-parancsmag naplózása

PowerShell-parancsmagok (például Register-AzureADPasswordProtectionProxy) állapotváltozás eredményező általában fog naplózhat egy eseményt, serkenti az eredményt, a műveleti napló.

Emellett az Azure AD-jelszó Protection PowerShell-parancsmagok a legtöbb fog írni egy szöveges napló alatt található:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Ha egy parancsmag hiba lép fel, és a OK kijelöljön megoldás nem jól látható, a szöveges naplók is megtekinthetők.

## <a name="proxy-discovery"></a>Proxy-felderítés

A `Get-AzureADPasswordProtectionProxy` parancsmag is használható a különböző Azure AD-jelszó védelmi alkalmazásproxy-szolgáltatásokat futtató tartomány vagy erdő alapvető információit jeleníti meg. Ez az információ veszi át a serviceConnectionPoint objektumok a futó Proxy szolgáltatás(ok) által regisztrált.

Egy példa a parancsmag kimenete a következőképpen történik:

```PowerShell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

Minden egyes proxyszolgáltatás hozzávetőleges számítónk különböző tulajdonságait frissíti. Az adatok a továbbra is az Active Directory replikáció késése.

A parancsmag lekérdezés hatókörét használatával befolyásolhatja a – erdőben vagy -Domain paraméterek.

Ha a HeartbeatUTC érték elavult, ez lehet, hogy a Azure AD jelszó-védelem Proxy, hogy a gép nem fut, vagy el lett távolítva.

## <a name="next-steps"></a>További lépések

[Hibaelhárítás az Azure AD jelszóvédelem](howto-password-ban-bad-on-premises-troubleshoot.md)

A globális és egyéni letiltott jelszavak listáit további információkért tekintse meg a cikket [rossz jelszavak letiltása](concept-password-ban-bad.md)

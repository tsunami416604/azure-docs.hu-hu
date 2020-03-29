---
title: Helyszíni Azure AD-jelszóvédelem figyelése
description: Megtudhatja, hogy miként figyelheti és tekintheti át az Azure AD-jelszóvédelem naplóit egy helyszíni Active Directory tartományi szolgáltatások környezetéhez
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbb533d5565009fb22d686e4082c9b4bfaae6dc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671657"
---
# <a name="monitor-and-review-logs-for-on-premises-azure-ad-password-protection-environments"></a>Helyszíni Azure AD password protection környezetek naplóinak figyelése és áttekintése

Az Azure AD password protection üzembe helyezése után a figyelés és a jelentéskészítés alapvető feladatok. Ez a cikk részletesen ismerteti a különböző figyelési technikák, beleértve, ahol az egyes szolgáltatások naplózza az információkat, és hogyan kell jelenteni az Azure AD password protection használatával.

A figyelés és a jelentéskészítés eseménynapló-üzenetek vagy PowerShell-parancsmagok futtatásával történik. A tartományvezérlő-ügynök és a proxyszolgáltatások egyaránt naplózzák az eseménynapló-üzeneteket. Az alábbiakban ismertetett összes PowerShell-parancsmag csak a proxykiszolgálón érhető el (lásd az AzureADPasswordProtection PowerShell modult). A tartományvezérlő-ügynök szoftvere nem telepít PowerShell-modult.

## <a name="dc-agent-event-logging"></a>Tartományvezérlő-ügynök eseménynaplózása

A tartományvezérlő-ügynök szolgáltatásszoftverminden tartományvezérlőn egy helyi eseménynaplóba írja az egyes jelszó-érvényesítési műveletek (és egyéb állapotok) eredményeit:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

A tartományvezérlő-ügynök admin naplója a szoftver működésének elsődleges információforrása.

Vegye figyelembe, hogy a Nyomkövetési napló alapértelmezés szerint ki van kapcsolva.

A különböző egyenáramú ügynök-összetevők által naplózott események a következő tartományokba esnek:

|Összetevő |Eseményazonosító tartománya|
| --- | --- |
|Tartományvezérlő-ügynök jelszószűrődlle| 10000-19999|
|TARTOMÁNYVEZÉRLŐ-ügynök szolgáltatásüzemeltetési folyamata| 20000-29999|
|Tartományvezérlő-ügynök szolgáltatásházirend-ellenőrzési logikája| 30000-39999|

## <a name="dc-agent-admin-event-log"></a>Tartományvezérlő-ügynök felügyeleti eseménynaplója

### <a name="password-validation-outcome-events"></a>Jelszó-érvényesítési eredményesemények

A tartományvezérlő-ügynök szolgáltatásszoftverminden tartományvezérlőn minden egyes jelszó-érvényesítés eredményét a tartományvezérlő-ügynök felügyeleti eseménynaplójába írja.

A sikeres jelszó-érvényesítési művelethez általában egy esemény van bejelentkezve a tartományvezérlő-ügynök jelszószűrődlési dll-jéből. A sikertelen jelszó-ellenőrzési művelet hez általában két esemény van naplózva, egy a tartományvezérlő-ügynök szolgáltatásból, a másik pedig a tartományvezérlő-ügynök jelszószűrő dll-jéből.

Az ilyen helyzetek rögzítésére szolgáló különálló eseményeket a rendszer a következő tényezők alapján naplózza:

* Azt jelzi, hogy egy adott jelszó be van-e állítva vagy módosult-e.
* Azt jelzi, hogy egy adott jelszó érvényesítése átment-e vagy sem.
* Azt jelzi, hogy az érvényesítés a Microsoft globális házirendje, a szervezeti házirend vagy egy kombináció miatt nem sikerült.Whether validation failed due to the Microsoft global policy, the organization policy, or a combination.
* Azt jelzi, hogy a csak naplózási mód be van-e kapcsolva vagy ki van-e kapcsolva az aktuális jelszóházirendhez.

A kulcsjelszó-ellenőrzéssel kapcsolatos események a következők:

|   |Jelszó módosítása |Jelszó beállítása|
| --- | :---: | :---: |
|Át |10014 |10015|
|Sikertelen (ügyféljelszó-házirend miatt)| 10016, 30002| 10017, 30003|
|Sikertelen (a Microsoft jelszóházirendje miatt)| 10016, 30004| 10017, 30005|
|Sikertelen (a Microsoft és az ügyfél jelszóra vonatkozó kombinált házirendek miatt)| 10016, 30026| 10017, 30027|
|Csak naplózási pass (meghiúsult volna az ügyféljelszó-házirend)| 10024, 30008| 10025, 30007|
|Csak naplózási pass (meghiúsult volna a Microsoft jelszóházirendje)| 10024, 30010| 10025, 30009|
|Csak naplózási pass (nem sikerült volna a Microsoft és az ügyféljelszó-házirendek kombinálása)| 10024, 30028| 10025, 30029|

A fenti táblázatban szereplő, "kombinált szabályzatokra" hivatkozó esetek olyan helyzetekre vonatkoznak, amikor a felhasználó jelszaváról megállapították, hogy legalább egy tokent tartalmaz mind a Microsoft tiltott jelszavak listájából, mind az ügyfél által tiltott jelszólistából.

Ha egy pár esemény van bejelentkezve együtt, mindkét esemény explicit módon társítva ugyanazt a Korrelációs azonosítót.

### <a name="password-validation-summary-reporting-via-powershell"></a>Jelszóérvényesítési összefoglaló jelentés készítés a PowerShellen keresztül

A `Get-AzureADPasswordProtectionSummaryReport` parancsmag a jelszó-érvényesítési tevékenység összefoglaló nézetének létrehozásához használható. A parancsmag kimenetének egyik példája a következő:

```powershell
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

A parancsmag jelentésének hatóköre a –Erdő, -Tartomány vagy –DomainController paraméterek egyikével befolyásolható. Nem adja meg a paraméter t -Erdő.

A `Get-AzureADPasswordProtectionSummaryReport` parancsmag úgy működik, hogy lekérdezi a tartományvezérlő-ügynök felügyeleti eseménynaplóját, majd megszámolja az egyes megjelenített eredménykategóriáknak megfelelő események teljes számát. Az alábbi táblázat az egyes eredmények és a hozzájuk tartozó eseményazonosító közötti leképezéseket tartalmazza:

|Get-AzureADPasswordProtectionSummaryReport tulajdonság |Megfelelő eseményazonosító|
| :---: | :---: |
|PasswordChangesValidates |10014|
|PasswordSetsÉrvényesítve |10015|
|PasswordChangesElutasított |10016|
|PasswordSetsElutasított |10017|
|PasswordChangeAuditOnlyFailures |10024|
|PasswordSetAuditOnlyFailures |10025|
|PasswordChangeErrors (Jelszómódosítási hibák) |10012|
|PasswordSetErrors (PasswordSetErrors) |10013|

Vegye figyelembe, hogy a `Get-AzureADPasswordProtectionSummaryReport` parancsmag a PowerShell parancsfájl formájában kerül kiszállításra, és szükség esetén közvetlenül a következő helyen hivatkozhat:

`%ProgramFiles%\WindowsPowerShell\Modules\AzureADPasswordProtection\Get-AzureADPasswordProtectionSummaryReport.ps1`

> [!NOTE]
> Ez a parancsmag úgy működik, hogy minden tartományvezérlőszámára megnyit egy PowerShell-munkamenetet. A siker érdekében a PowerShell távoli munkamenet-támogatását minden tartományvezérlőn engedélyezni kell, és az ügyfélnek megfelelő jogosultságokkal kell rendelkeznie. A PowerShell távoli munkamenet-követelményeiről további információt a "Get-Help about_Remote_Troubleshooting" futtatásához egy PowerShell-ablakban.

> [!NOTE]
> Ez a parancsmag úgy működik, hogy távolról lekérdezi az egyes tartományvezérlő-ügynökszolgáltatás felügyeleti eseménynaplóját. Ha az eseménynaplók nagy számú eseményt tartalmaznak, a parancsmag befejezése hosszú időt vehet igénybe. Ezenkívül a nagy adatkészletek tömeges hálózati lekérdezései hatással lehetnek a tartományvezérlő teljesítményére. Ezért ezt a parancsmagát éles környezetben gondosan kell használni.

### <a name="sample-event-log-message-for-event-id-10014-successful-password-change"></a>Mintaeseménynapló-üzenet az 10014-es azonosítójú eseményhez (sikeres jelszómódosítás)

```text
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Mintaeseménynapló-üzenet az 10017-es és 30003-as eseményazonosítóhoz (sikertelen jelszóbeállítás)

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

### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Mintaeseménynapló-üzenet a 30001-es azonosítójú eseményhez (jelszó elfogadva, mert nincs elérhető házirend)

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

### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>A 30006-os eseményazonosítójú esemény naplójának mintanaplója (az új házirend kényszerítése)

```text
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

### <a name="sample-event-log-message-for-event-id-30019-azure-ad-password-protection-is-disabled"></a>A 30019-es eseményazonosítójú esemény naplójának mintaüzenete (az Azure AD jelszavas védelem le van tiltva)

```text
The most recently obtained Azure password policy was configured to be disabled. All passwords submitted for validation from this point on will automatically be considered compliant with no processing performed.

No further events will be logged until the policy is changed.%n

```

## <a name="dc-agent-operational-log"></a>TARTOMÁNYVEZÉRLŐ-ügynök működési naplója

A tartományvezérlő-ügynök szolgáltatás a működéssel kapcsolatos eseményeket is naplózza a következő naplóba:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

## <a name="dc-agent-trace-log"></a>Tartományvezérlő-ügynök nyomkövetési naplója

A tartományvezérlő-ügynök szolgáltatás részletes hibakeresési szintű nyomkövetési eseményeket is naplózhat a következő naplóba:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

A nyomkövetési naplózás alapértelmezés szerint le van tiltva.

> [!WARNING]
> Ha engedélyezve van, a nyomkövetési napló nagy mennyiségű eseményt kap, és hatással lehet a tartományvezérlő teljesítményére. Ezért ezt a továbbfejlesztett naplót csak akkor kell engedélyezni, ha egy probléma mélyebb vizsgálatot igényel, és csak minimális ideig.

## <a name="dc-agent-text-logging"></a>Tartományvezérlő-ügynök szövegnaplózása

A tartományvezérlő-ügynök szolgáltatás beállítható úgy, hogy a következő beállítási értékkel írjon a szöveges naplóba:

```text
HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters!EnableTextLogging = 1 (REG_DWORD value)
```

A szövegnaplózás alapértelmezés szerint le van tiltva. A tartományvezérlő-ügynök szolgáltatás újraindítása szükséges az érték módosításához. Ha engedélyezve van, a tartományvezérlő-ügynök szolgáltatás a következő helyen található naplófájlba fog írni:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> A szöveges napló ugyanazokat a hibakeresési szintű bejegyzéseket kapja, amelyek naplózhatók a nyomkövetési naplóba, de általában egyszerűbb formátumban tekinthetők át és elemezhetők.

> [!WARNING]
> Ha engedélyezve van, ez a napló nagy mennyiségű eseményt kap, és hatással lehet a tartományvezérlő teljesítményére. Ezért ezt a továbbfejlesztett naplót csak akkor kell engedélyezni, ha egy probléma mélyebb vizsgálatot igényel, és csak minimális ideig.

## <a name="dc-agent-performance-monitoring"></a>Tartományvezérlő-ügynök teljesítményének figyelése

A TARTOMÁNYVEZÉRLŐ-ügynök szolgáltatásszoftver egy **Azure AD Password Protection**nevű teljesítményszámláló-objektumot telepít. Jelenleg a következő perf számlálók érhetők el:

|Perf számláló neve | Leírás|
| --- | --- |
|Feldolgozott jelszavak |Ez a számláló a legutóbbi újraindítás óta feldolgozott (elfogadott vagy elutasított) jelszavak teljes számát jeleníti meg.|
|Elfogadott jelszavak |Ez a számláló a legutóbbi újraindítás óta elfogadott jelszavak teljes számát jeleníti meg.|
|Elutasított jelszavak |Ez a számláló a legutóbbi újraindítás óta elutasított jelszavak teljes számát jeleníti meg.|
|Folyamatban lévő jelszószűrő-kérelmek |Ez a számláló a folyamatban lévő jelszószűrő-kérelmek számát jeleníti meg.|
|Jelszószűrő-kérelmek maximális száma |Ez a számláló az egyidejű jelszószűrő-kérelmek maximális számát jeleníti meg a legutóbbi újraindítás óta.|
|Jelszószűrő-kérelem hibái |Ez a számláló azoknak a jelszószűrő-kérelmeknek a teljes számát jeleníti meg, amelyek a legutóbbi újraindítás óta hiba miatt sikertelenek voltak. Hibák fordulhatnak elő, ha az Azure AD Password Protection DC ügynök szolgáltatás nem fut.|
|Jelszószűrő-kérelmek/mp |Ez a számláló a jelszavak feldolgozásának sebességét jeleníti meg.|
|Jelszószűrő-kérelmek feldolgozási ideje |Ez a számláló a jelszószűrő-kérelmek feldolgozásához szükséges átlagos időt jeleníti meg.|
|A jelszószűrő-kérelmek feldolgozásának csúcsideje |Ez a számláló a legutóbbi újraindítás óta eltelt maximális jelszószűrő-kérelem feldolgozási idejét jeleníti meg.|
|A rendszervizsgálati mód miatt elfogadott jelszavak |Ez a számláló azoknak a jelszavaknak a teljes számát jeleníti meg, amelyeket egyébként elutasított volna, de a rendszer azért lett elfogadva, mert a jelszóházirend naplózási módban volt konfigurálva (az utolsó újraindítás óta).|

## <a name="dc-agent-discovery"></a>Tartományvezérlő-ügynök felderítése

A `Get-AzureADPasswordProtectionDCAgent` parancsmag a tartományban vagy erdőben futó különböző tartományvezérlő-ügynökök alapvető információinak megjelenítésére használható. Ezt az információt a futó tartományvezérlő-ügynökszolgáltatás(ok) által regisztrált ConnectionPoint-objektum(ok) a rendszer olvassa be.

A parancsmag kimenetének egyik példája a következő:

```powershell
Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
PasswordPolicyDateUTC : 2/16/2018 8:35:01 AM
HeartbeatUTC          : 2/16/2018 8:35:02 AM
```

A különböző tulajdonságokat az egyes tartományvezérlő-ügynök-szolgáltatások körülbelül óránként frissítik. Az adatok továbbra is az Active Directory replikációs késésének vannak kitéve.

A parancsmag lekérdezésének hatóköre a –Erdő vagy –Tartomány paraméterekkel is befolyásolható.

Ha a HeartbeatUTC érték elavult, ez annak a tünete lehet, hogy az Azure AD password protection dc-ügynök az adott tartományvezérlőn nem fut, vagy eltávolították, vagy a gép lelett fokozva, és már nem tartományvezérlő.

Ha a PasswordPolicyDateUTC érték elavultlesz, ez lehet a tünete, hogy az Azure AD password protection DC-ügynök a számítógépen nem működik megfelelően.

## <a name="dc-agent-newer-version-available"></a>Dc-ügynök újabb verziója érhető el

A tartományvezérlő-ügynök szolgáltatás naplóz egy 30034-es figyelmeztető eseményt a működési naplóba, amikor észleli, hogy a tartományvezérlő-ügynök szoftverének egy újabb verziója érhető el, például:

```text
An update for Azure AD Password Protection DC Agent is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
```

A fenti esemény nem határozza meg az újabb szoftver verzióját. Meg kell menni a linkre az esemény üzenetben az információt.

> [!NOTE]
> Annak ellenére, hogy a fenti eseményüzenetben az "automatikus frissítésre" hivatkoznak, a DC-ügynök szoftver jelenleg nem támogatja ezt a funkciót.

## <a name="proxy-service-event-logging"></a>Proxyszolgáltatás eseménynaplózása

A proxyszolgáltatás a következő eseménynaplókban az események minimális készletét bocsátja ki:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Vegye figyelembe, hogy a Nyomkövetési napló alapértelmezés szerint ki van kapcsolva.

> [!WARNING]
> Ha engedélyezve van, a nyomkövetési napló nagy mennyiségű eseményt kap, és ez hatással lehet a proxyállomás teljesítményére. Ezért ezt a naplót csak akkor kell engedélyezni, ha egy probléma mélyebb vizsgálatot igényel, és csak minimális ideig.

Az eseményeket a különböző proxyösszetevők naplózzák a következő tartományok használatával:

|Összetevő |Eseményazonosító tartománya|
| --- | --- |
|Proxy szolgáltatás üzemeltetési folyamata| 10000-19999|
|Proxyszolgáltatás alapvető üzleti logikája| 20000-29999|
|PowerShell-parancsmagok| 30000-39999|

## <a name="proxy-service-text-logging"></a>Proxyszolgáltatás szövegének naplózása

A proxyszolgáltatás beállítható úgy, hogy a következő beállítási értékkel írjon a szöveges naplóba:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (REG_DWORD érték)

A szövegnaplózás alapértelmezés szerint le van tiltva. Az érték módosításához újra kell indítani a proxyszolgáltatást. Ha engedélyezve van, a proxyszolgáltatás a következő helyen található naplófájlba ír:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> A szöveges napló ugyanazokat a hibakeresési szintű bejegyzéseket kapja, amelyek naplózhatók a nyomkövetési naplóba, de általában egyszerűbb formátumban tekinthetők át és elemezhetők.

> [!WARNING]
> Ha engedélyezve van, ez a napló nagy mennyiségű eseményt fogad, és hatással lehet a gép teljesítményére. Ezért ezt a továbbfejlesztett naplót csak akkor kell engedélyezni, ha egy probléma mélyebb vizsgálatot igényel, és csak minimális ideig.

## <a name="powershell-cmdlet-logging"></a>PowerShell-parancsmag naplózása

Az állapotváltozást eredményező PowerShell-parancsmagok (például Register-AzureADPasswordProtectionProxy) általában egy eredményeseményt naplóznak a működési naplóba.

Emellett az Azure AD Password Protection PowerShell-parancsmagok többsége a következő helyen található szöveges naplóba fog írni:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Ha parancsmaghiba lép fel, és az ok és a megoldás nem egyértelmű, akkor ezek a szövegnaplók is megtekinthetők.

## <a name="proxy-discovery"></a>Proxyfelderítés

A `Get-AzureADPasswordProtectionProxy` parancsmag segítségével alapvető információkat jeleníthet meg a tartományban vagy erdőben futó különböző Azure AD jelszóvédelmi proxy szolgáltatások. Ezt az információt a futó proxyszolgáltatás(ok) által regisztrált ConnectionPoint-objektum(ok) a rendszer olvassa be.

A parancsmag kimenetének egyik példája a következő:

```powershell
Get-AzureADPasswordProtectionProxy
ServerFQDN            : bplProxy.bplchild2.bplRootDomain.com
Domain                : bplchild2.bplRootDomain.com
Forest                : bplRootDomain.com
HeartbeatUTC          : 12/25/2018 6:35:02 AM
```

A különböző tulajdonságokat az egyes proxyszolgáltatások körülbelül óránként frissítik. Az adatok továbbra is az Active Directory replikációs késésének vannak kitéve.

A parancsmag lekérdezésének hatóköre a –Erdő vagy –Tartomány paraméterekkel is befolyásolható.

Ha a HeartbeatUTC érték elavult, ez lehet a tünete, hogy az Azure AD password protection proxy az adott gépen nem fut, vagy eltávolították.

## <a name="proxy-agent-newer-version-available"></a>Proxyügynök újabb verziója érhető el

A proxyszolgáltatás 20002-es figyelmeztető eseményt naplóz az operatív naplóba, amikor észleli, hogy a proxyszoftver újabb verziója érhető el, például:

```text
An update for Azure AD Password Protection Proxy is available.

If autoupgrade is enabled, this message may be ignored.

If autoupgrade is disabled, refer to the following link for the latest version available:

https://aka.ms/AzureADPasswordProtectionAgentSoftwareVersions

Current version: 1.2.116.0
.
```

A fenti esemény nem határozza meg az újabb szoftver verzióját. Meg kell menni a linkre az esemény üzenetben az információt.

Ez az esemény akkor is megjelenik, ha a proxyügynök automatikus frissítés engedélyezve van.

## <a name="next-steps"></a>További lépések

[Az Azure AD jelszóvédelem hibaelhárítása](howto-password-ban-bad-on-premises-troubleshoot.md)

A globális és egyéni tiltott jelszólistákról a Rossz jelszavak tiltása című cikkben olvashat [bővebben.](concept-password-ban-bad.md)

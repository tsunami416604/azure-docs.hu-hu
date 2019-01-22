---
title: Hibaelhárítás és a bejelentkezés az Azure AD jelszó protection előzetes verziója
description: Megismerheti az Azure AD jelszóvédelem naplózás és a gyakori hibaelhárítási előzetes verzió
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 008fba2fd6052ac9043de6ec217cb71d17f3ecce
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427100"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Előzetes verzió: Az Azure AD jelszó-védelem figyelési, jelentéseket és hibaelhárítás

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Az Azure AD jelszóvédelem üzembe helyezés után a figyelés és jelentéskészítés elvégzendő alapvető feladatok. Ez a cikk részletezik segítségével megismeri, ahol minden szolgáltatás információkat naplózza, és az Azure AD jelszóvédelem használatakor jelentése.

## <a name="on-premises-logs-and-events"></a>A helyszíni naplók és események

### <a name="dc-agent-admin-log"></a>Tartományvezérlő az ügynök felügyeleti napló

Minden tartományvezérlőn a tartományvezérlő szolgáltatás ügynökszoftver a naplófájlba írja az eredményeket a jelszó-ellenőrzések (és más állapota) egy helyi esemény:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin`

A különböző DC agent-összetevők használatával a következő tartományokra által naplózott eseményeket:

|Összetevő |Esemény tartomány|
| --- | --- |
|Tartományvezérlő ügynök jelszó szűrő dll| 10000-19999|
|Tartományvezérlő ügynök szolgáltatás üzemeltetési folyamata| 20000-29999|
|Tartományvezérlő ügynök szolgáltatás házirend ellenőrzési logika| 30000-39999|

Sikeres jelszó-ellenőrzési művelet esetén nincs általában egy eseményt naplózza a tartományvezérlő ügynök jelszó szűrő DLL-ben. A sikertelen jelszó-ellenőrzési művelet, nincsenek általában két naplózott eseményeket, egy a DC-ügynök szolgáltatás, és egy, a tartományvezérlő ügynök jelszó szűrő dll.

Ezekben a helyzetekben rögzítéséhez diszkrét eseményeket naplózza, az alábbi tényezők alapján:

* E folyamatban van, a megadott jelszó megadása vagy módosítása.
* E egy adott a jelszó érvényesítése sikeres és sikertelen.
* E érvényesítési miatt nem sikerült a Microsoft globális szabályzat vagy a szervezeti házirend.
* E csak naplózási mód jelenleg be- vagy kikapcsolása az aktuális jelszó házirend.

A kulcs jelszó-érvényesítési-események a következők:

|   |Jelszó módosítása |Jelszó beállítása|
| --- | :---: | :---: |
|Sikeres |10014 |10015|
|Sikertelen (nem felelt meg az ügyfél jelszóházirend)| 10016, 30002| 10017, 30003|
|Sikertelen (nem felelt meg a Microsoft jelszóházirend)| 10016, 30004| 10017, 30005|
|Csak naplózási Pass (végrehajtása nem sikerül ügyfél jelszóházirend)| 10024, 30008| 10025, 30007|
|Csak naplózási Pass (végrehajtása nem sikerül a Microsoft jelszóházirend)| 10024, 30010| 10025, 30009|

> [!TIP]
> Bejövő jelszavak ellenőrizni hasonlítja össze a Microsoft globális jelszót először; Ha ez nem sikerül, nincs további feldolgozás céljából történik. Ez a lehetőség viselkedést végrehajtott jelszómódosítások az Azure-ban.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Minta eseménynapló-üzenet az eseményazonosító 10014 (sikeres jelszavának beállítása)

```
The changed password for the specified user was validated as compliant with the current Azure password policy.

 UserName: BPL_02885102771
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Minta eseménynapló-üzenet az eseményazonosító 10017 és 30003 (sikertelen jelszómegadási set)

10017:

```
The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.

 UserName: BPL_03283841185
 FullName:
```

30003:

```
The reset password for the specified user was rejected because it matched at least one of the tokens present in the per-tenant banned password list of the current Azure password policy.

 UserName: BPL_03283841185
 FullName:
```

#### <a name="sample-event-log-message-for-event-id-30001-password-accepted-due-to-no-policy-available"></a>Minta eseménynapló-üzenet az eseményazonosító 30001 (jelszó miatt nincs elérhető házirend elfogadja)

```
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

#### <a name="sample-event-log-message-for-event-id-30006-new-policy-being-enforced"></a>Minta eseménynapló-üzenet az eseményazonosító 30006 (új szabályzat van érvényben)

```
The service is now enforcing the following Azure password policy.

 Enabled: 1
 AuditOnly: 1
 Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
 Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
 Enforce tenant policy: 1
```

#### <a name="dc-agent-operational-log"></a>Tartományvezérlő az ügynök műveleti napló

A tartományvezérlő-ügynökszolgáltatás is naplózza az eseményeket üzemeltetési vonatkozású, a következő log:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational`

#### <a name="dc-agent-trace-log"></a>Tartományvezérlő ügynök nyomkövetési napló

A tartományvezérlő-ügynökszolgáltatás is jelentkezhetnek hibakeresési szintűre részletes nyomkövetési események a következő naplófájl:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace`

Nyomkövetési adatainak naplózása alapértelmezés szerint le van tiltva.

> [!WARNING]
>  Ha engedélyezve van, a nyomkövetési napló nagy mennyiségű esemény kap, és hatással lehet a tartományvezérlő teljesítményét. Ezért a továbbfejlesztett napló kell csak akkor engedélyezhető, ha probléma mélyebb vizsgálatra van szükség, és akkor is csak egy minimális időtartamot.

#### <a name="dc-agent-text-logging"></a>Tartományvezérlő ügynök szöveges naplózás

A tartományvezérlő-ügynök szolgáltatás beállítható úgy, hogy egy szöveges napló írni, állítsa a következő beállításazonosítót:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionDCAgent\Parameters! EnableTextLogging = 1 (REG_DWORD érték)

Szöveges naplózás alapértelmezés szerint le van tiltva. A tartományvezérlő-ügynök szolgáltatás újraindításra szükség a módosítások érvénybe léptetéséhez ezt az értéket. Ha engedélyezve van a tartományvezérlő ügynökszolgáltatás fog kiírni, egy naplófájlba alatt található:

`%ProgramFiles%\Azure AD Password Protection DC Agent\Logs`

> [!TIP]
> A szöveges napló kap a nyomkövetési naplónak naplózható hibakeresési szintűre bejegyzések, de általában egyszerűbb formátumban áttekintéséhez és elemzéséhez.

> [!WARNING]
> Ha engedélyezve van, ez a napló nagy mennyiségű esemény fogadása, és hatással lehet a tartományvezérlő teljesítményét. Ezért a továbbfejlesztett napló kell csak akkor engedélyezhető, ha probléma mélyebb vizsgálatra van szükség, és akkor is csak egy minimális időtartamot.

### <a name="azure-ad-password-protection-proxy-service"></a>Az Azure AD jelszó protection proxy szolgáltatás

#### <a name="proxy-service-event-logs"></a>Proxy szolgáltatás eseménynaplók

A Proxy szolgáltatást az alábbi eseménynaplókból események minimális számú bocsát ki:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Admin`

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational`

A Proxy szolgáltatást is jelentkezhetnek hibakeresési szintűre részletes nyomkövetési események a következő naplófájl:

`\Applications and Services Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace`

Nyomkövetési adatainak naplózása alapértelmezés szerint le van tiltva.

> [!WARNING]
> Ha engedélyezve van, a nyomkövetési napló fogadja-e nagy mennyiségű esemény, és hatással lehet a proxyállomás teljesítményét. Ezért ez a napló kell csak akkor engedélyezhető, ha a probléma mélyebb vizsgálatra van szükség, és akkor is csak egy minimális időtartamot.

#### <a name="proxy-service-text-logging"></a>Proxy szolgáltatás szöveges naplózás

A Proxy szolgáltatás beállítható úgy, hogy egy szöveges napló írni, állítsa a következő beállításazonosítót:

HKLM\System\CurrentControlSet\Services\AzureADPasswordProtectionProxy\Parameters! EnableTextLogging = 1 (REG_DWORD érték)

Szöveges naplózás alapértelmezés szerint le van tiltva. A Proxy szolgáltatás újraindításra szükség a módosítások érvénybe léptetéséhez ezt az értéket. Ha a szolgáltatás alatt naplófájlt fogja írni a Proxy engedélyezve:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

> [!TIP]
> A szöveges napló kap a nyomkövetési naplónak naplózható hibakeresési szintűre bejegyzések, de általában egyszerűbb formátumban áttekintéséhez és elemzéséhez.

> [!WARNING]
> Ha engedélyezve van, ez a napló nagy mennyiségű esemény fogadása, és hatással lehet a tartományvezérlő teljesítményét. Ezért a továbbfejlesztett napló kell csak akkor engedélyezhető, ha probléma mélyebb vizsgálatra van szükség, és akkor is csak egy minimális időtartamot.

#### <a name="powershell-cmdlet-logging"></a>PowerShell-parancsmag naplózása

Az Azure AD jelszóvédelem Powershell-parancsmagok a legtöbb fog írni egy szöveges napló alatt található:

`%ProgramFiles%\Azure AD Password Protection Proxy\Logs`

Ha egy parancsmag hiba lép fel, és a OK kijelöljön megoldás nem jól látható, a szöveges naplók is megtekinthetők.

### <a name="emergency-remediation"></a>Sürgős szervizelés

Ha olyan helyzet akkor fordul elő, ha a tartományvezérlő-ügynökszolgáltatás problémákat okoz, a tartományvezérlő-ügynökszolgáltatás előfordulhat, hogy azonnal leáll. A tartományvezérlő ügynök jelszó szűrő dll továbbra is megpróbálja meghívni a nem futó szolgáltatás, és naplózza az figyelmeztetési eseményeket (10012, 10013), de ebben az időszakban minden bejövő jelszót fogad. A tartományvezérlő-ügynökszolgáltatás majd is konfigurálhatók keresztül a Windows szolgáltatásvezérlő indítási típusaként válassza a "Letiltott" igény szerint.

### <a name="performance-monitoring"></a>Teljesítményfigyelés

A tartományvezérlő ügynök szolgáltatás szoftver telepítése a teljesítményszámláló objektum neve **Azure AD jelszóvédelem**. Az alábbi teljesítményszámlálók jelenleg érhetők el:

|Teljesítményoptimalizált számláló neve | Leírás|
| --- | --- |
|Feldolgozott jelszavak |Ez a számláló utolsó újraindítás óta (elfogad vagy elutasít) feldolgozott jelszavak teljes számát jeleníti meg.|
|Jelszavak elfogadva |Ez a számláló a teljes száma, hogy a rendszer elfogadta az utolsó újraindítás óta jeleníti meg.|
|Elutasított jelszavak |Ez a számláló a teljes száma, hogy a rendszer elutasította az utolsó újraindítás óta jeleníti meg.|
|Jelszó kérelmek szűrése folyamatban |Ez a számláló jeleníti meg a jelszó-kérelmek szűrése jelenleg folyamatban van.|
|Csúcsidőszak jelszó kérelmek szűrése |Ez a számláló egyidejű jelszó szűrő kérelmek maximális száma az utolsó újraindítás óta jeleníti meg.|
|Jelszó szűrő kérelem hibák |Ez a számláló a jelszó kérelmek szűrése, amely egy hiba miatt sikertelen volt az utolsó újraindítás óta teljes számát jeleníti meg. Hibák akkor fordulhatnak elő, ha az Azure AD jelszó DC védelmi ügynök szolgáltatás nem fut.|
|Jelszó szűrő vonatkozó kérelmek/másodperc |Ez a számláló a sebességet, milyen jelszavak feldolgozott jeleníti meg.|
|Jelszó szűrő kérelem feldolgozási ideje |Ez a számláló a jelszó szűrő kérelmek feldolgozásához szükséges átlagos időt jeleníti meg.|
|Csúcsidőszak jelszó szűrő kérelem feldolgozási ideje |Ez a számláló a csúcsidőszak jelszó szűrő kérelem feldolgozása az utolsó újraindítás óta eltelt idő jeleníti meg.|
|Jelszavak elfogadott miatt mód naplózása |Ez a számláló a teljes száma, hogy lenne általában elutasított, de a rendszer elfogadta, mert a jelszóházirend úgy lett konfigurálva, hogy rendszervizsgálati módban (az utolsó újraindítás) óta jeleníti meg.|

## <a name="directory-services-repair-mode"></a>Címtárszolgáltatások helyreállító módjában

A tartományvezérlő a Címtárszolgáltatások helyreállító módjában rendszer indult, ha a tartományvezérlő-ügynökszolgáltatás észlel, az összes jelszó érvényesítése vagy le kell tiltani, függetlenül a jelenleg aktív szabályzatkonfiguráció végrehajtó tevékenységek okozó.

## <a name="domain-controller-demotion"></a>Tartományvezérlő lefokozása

Továbbra is fut a tartományvezérlő ügynökszoftver tartományvezérlő lefokozása támogatott. A rendszergazdák figyelembe kell azonban, hogy a tartományvezérlő ügynök szoftver továbbra is működik, és továbbra is a jelenlegi jelszóházirend kényszerítése a lefokozási folyamat során. Az új helyi rendszergazdai fiók jelszava (a lefokozás művelet részeként megadott) érvényesítve lesz, mint bármely más jelszót. A Microsoft azt javasolja, hogy biztonságos jelszavakkal a helyi rendszergazdai fiókok választani egy tartományvezérlő lefokozási eljárást; részeként Új helyi rendszergazdai fiók jelszavának a tartományvezérlő ügynök szoftver érvényesítése azonban előre a lefokozás eljárások meglévő zavaró lehet.

A lefokozás sikeres volt, és a tartományvezérlő újraindítását, és újra normál kiszolgáló fut, a tartományvezérlő ügynökszoftver visszatér a passzív üzemmódban futó. Előfordulhat, hogy ezután távolítható bármikor.

## <a name="removal"></a>Eltávolítás

Ha úgy döntenek, hogy távolítsa el a nyilvános előzetes verziójú szoftver és karbantartása minden kapcsolódó állapota a tartomány és erdő, ez a feladat végezhető el az alábbi lépéseket követve:

> [!IMPORTANT]
> Fontos sorrendben hajtsa végre ezeket a lépéseket. Ha a Proxy szolgáltatás minden példányát futtató, rendszeresen újra létrehozza a serviceConnectionPoint objektum. Ha a tartományvezérlő-ügynökszolgáltatás bármelyik példányát futtató, rendszeresen újra létrehozza a serviceConnectionPoint objektum és a sysvol-állapot.

1. Távolítsa el a jelszavas védelem Proxy szoftver összes gép. Ebben a lépésben does **nem** újraindítása szükséges.
2. Távolítsa el a tartományvezérlő ügynökszoftver az összes tartományvezérlőről. Ebben a lépésben **igényel** újraindítás.
3. Manuálisan távolítsa el a Proxy összes szolgáltatáskapcsolati pontját, az egyes tartományi névhasználati környezeten. Ezek az objektumok helyét is felderítése a következő Active Directory Powershell-paranccsal:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nem hagyja ki a csillag ("*") a $keywords változó értékét a végén.

   Az eredményül kapott objektumok található keresztül a `Get-ADObject` parancs majd átadható olyan parancsoknak `Remove-ADObject`, vagy manuálisan törölték. 

4. Manuálisan távolítsa el az összes tartományvezérlő ügynök csatlakozási pontok minden tartományi névhasználati környezetében. Lehet, hogy egy ezeket az objektumokat az erdő, attól függően, hogyan széles körben a nyilvános előzetes verziójú szoftver telepítve lett a tartományvezérlőnkénti. Előfordulhat, hogy felderítése a helyét annak az objektumnak a következő Active Directory Powershell-paranccsal:

   ```Powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Az eredményül kapott objektumok található keresztül a `Get-ADObject` parancs majd átadható olyan parancsoknak `Remove-ADObject`, vagy manuálisan törölték.

5. Manuálisan távolítsa el az erdő szintű konfigurációs állapotát. Az erdő konfigurációs állapotát egy tárolót az Active Directory konfigurációelnevezési környezetében a változatlan marad. Ez felderíthető és törölni az alábbiak szerint:

   ```Powershell
   $passwordProtectonConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectonConfigContainer
   ```

6. Manuálisan távolítsa el az összes sysvol-állapot manuálisan kapcsolódó törlése a következő mappához és annak teljes tartalmát:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ha szükséges, ezt az elérési utat is is elérhető, helyileg egy adott tartományvezérlőn; az alapértelmezett hely a következő elérési út hasonló lenne:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ez az elérési út eltérő, ha a sysvol-megosztás egy nem alapértelmezett helyen lett konfigurálva.

## <a name="next-steps"></a>További lépések

A globális és egyéni letiltott jelszavak listáit további információkért tekintse meg a cikket [rossz jelszavak letiltása](concept-password-ban-bad.md)

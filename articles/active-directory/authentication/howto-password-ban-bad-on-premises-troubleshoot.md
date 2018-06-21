---
title: Hibaelhárítás és az Azure AD-jelszó protection előzetes kiadásának-naplózás
description: Az Azure AD jelszóvédelem megértéséhez naplózás és a gyakori hibaelhárítási megtekintése
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 06/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: e5b3dc1bfa7c7890be83529e863907ec056f188f
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295658"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Előzetes verzió: Az Azure AD jelszó védelem figyelési, jelentéskészítési és hibaelhárítás

|     |
| --- |
| Az Azure AD-jelszó protection és a tiltott egyénijelszó listájának az Azure Active Directory nyilvános előzetes verziójú funkciók. Az előzetes verziójú funkciók kapcsolatos további információkért lásd: [kiegészítő használati feltételek Microsoft Azure előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Az Azure AD jelszóvédelem központi telepítése után a figyelés és jelentéskészítés elvégzendő alapvető feladatok. Ez a cikk részletek segítségével állapotba kerül, hogy tudomásul veszi, ahol minden szolgáltatás naplózza az adatokat és a jelentés az Azure AD jelszavas védelem használatát.

## <a name="on-premises-logs-and-events"></a>A helyszíni naplók és események

### <a name="dc-agent-service"></a>DC ügynökszolgáltatás

Az egyes tartományvezérlőkön, a tartományvezérlő szolgáltatás ügynökszoftver eredményeit a jelszó-érvényesítést (és más állapot) ír a helyi Eseménynapló: \Applications és a szolgáltatások Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

A különböző DC agent-összetevők a következő tartomány használata által naplózott eseményeket:

|Összetevő |Esemény Azonosítótartományának kezdete|
| --- | --- |
|Jelszó-szűrő dll DC ügynök| 10000-19999|
|DC ügynök szolgáltatás gazdafolyamatának| 20000-29999|
|DC ügynök szolgáltatás házirend ellenőrzési logika| 30000-39999|

A sikeres jelszó-ellenőrzési művelet van általában egy esemény naplózása a DC ügynök jelszó szűrő DLL-ben. Sikertelen a jelszó érvényesítése művelet esetén általában két naplózott eseményeket, a DC-ügynökszolgáltatás közül, és a tartományvezérlő ügynök jelszó-szűrő dll közül.

Ezekben a helyzetekben rögzítéséhez diszkrét eseményeket naplózza, alapul, az alábbi tényezőket:

* Hogy folyamatban van a megadott jelszó beállítása vagy módosítása.
* A megadott jelszó érvényesítése e átadva, vagy sikertelen volt.
* E érvényesítési miatt nem sikerült a Microsoft globális házirend és a szervezeti házirend.
* E csak naplózási mód jelenleg be- és kikapcsolása az aktuális jelszóházirend.

A kulcs jelszó-érvényességi-események a következők:

|   |Jelszó módosítása |Jelszó beállítása|
| --- | :---: | :---: |
|Sikeres |10014 |10015|
|Sikertelen (nem felelt meg az ügyfél jelszóházirend)| 10016, 30002| 10017, 30003|
|Sikertelen (nem felelt meg a Microsoft jelszóházirend)| 10016, 30004| 10017, 30005|
|Csak naplózási fázis (végrehajtása nem sikerül felhasználói jelszó-házirend)| 10024, 30008| 10025, 30007|
|Csak naplózási fázis (végrehajtása nem sikerül Microsoft jelszóházirend)| 10024, 30010| 10025, 30009|

> [!TIP]
> Bejövő jelszavak ellenőrizni a Microsoft globális jelszó listája alapján először; Ha nem sikerül, nincs további feldolgozás történik. Ez történik, azonos módon végrehajtott jelszómódosítások az Azure-ban.

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>A minta eseménynapló-üzenet eseményazonosító 10014 sikeres jelszavának beállítása

A megadott felhasználó a módosított jelszó lett érvényesítve, a jelenlegi Azure-jelszó házirenddel.

 Felhasználónév: BPL_02885102771 FullName:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>A minta eseménynapló-üzenet eseményazonosító 10017 és nem sikerült 30003 jelszavának beállítása

10017:

A jelszó-átállítási a megadott felhasználó el lett utasítva, mert nem felelt meg a jelenlegi Azure jelszóházirend. A korrelált az eseménynaplóba további részletekért lásd:.

 Felhasználónév: BPL_03283841185 FullName:

30003:

A jelszó-átállítási a megadott felhasználó el lett utasítva, mert legalább egy megtalálható az aktuális Azure jelszóházirendben / bérlői tiltott jelszó listáján jogkivonatok egyik megfelelő.

 Felhasználónév: BPL_03283841185 FullName:

Néhány más kulcs Eseménynapló-üzenetek tisztában lenni a rendszer:

#### <a name="sample-event-log-message-for-event-id-30001"></a>Az eseményazonosító 30001 minta eseménynapló-üzenet

A megadott felhasználó jelszavát fogadták el, mivel az Azure-jelszó házirend még nem érhető el

Felhasználónév: <user> FullName: <user>

A probléma oka lehet egy vagy több, a következő okok miatt: % n

1. Az erdő még nincs regisztrálva az Azure-ral.

   Megoldási lépései: a rendszergazda regisztrálnia kell az erdő a Register-AzureADPasswordProtectionForest parancsmaggal.

2. Egy Azure AD-jelszó védelmi Proxy még nem érhető el az aktuális erdőben legalább egy számítógépen.

   Megoldási lépései: a rendszergazda kell telepíteni, és regisztrálja a Register-AzureADPasswordProtectionProxy parancsmaggal proxy.

3. Ezen a Tartományvezérlőn nem rendelkezik hálózati kapcsolattal bármely Azure AD-jelszó védelmi Proxy-példányban.

   Megoldási lépések: Győződjön meg arról, legalább egy Azure AD jelszó védelmi Proxy példányra van hálózati kapcsolat.

4. A tartományvezérlő a tartomány nincs kapcsolat a többi tartományvezérlőn.

   Megoldási lépések: Győződjön meg arról, a tartományhoz van hálózati kapcsolat.

#### <a name="sample-event-log-message-for-event-id-30006"></a>Az eseményazonosító 30006 minta eseménynapló-üzenet

A szolgáltatás a következő Azure jelszóházirend most kényszerít.

 AuditOnly: 1

 Általános házirend dátuma: 2018-05-15T00:00:00.000000000Z

 A bérlői házirend dátuma: 2018-06-10T20:15:24.432457600Z

 Bérlői házirend: 1

#### <a name="dc-agent-log-locations"></a>DC ügynök naplók helye

A DC-ügynökszolgáltatás is naplózza működési események a következő naplóban: \Applications és a szolgáltatások Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

A DC-ügynökszolgáltatás is bejelentkezhetnek részletes hibakeresési szintű nyomkövetési események a következő naplóban: \Applications és a szolgáltatások Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

> [!WARNING]
> A nyomkövetési napló alapértelmezés szerint le van tiltva. Ha engedélyezve van, ez a napló nagy mennyiségű esemény kap, és hatással lehet, hogy a tartományvezérlők teljesítményére. Ezért ez a bővített napló kell csak engedélyezhető, ha probléma mélyebb vizsgálatra van szükség, és akkor is csak a lehető legrövidebb idő alatt.

### <a name="azure-ad-password-protection-proxy-service"></a>Az Azure AD jelszó védelmi proxy szolgáltatás

A jelszavas védelem proxyszolgáltatás bocsát legszükségesebb eseményeket, a következő naplófájl: \Applications és a szolgáltatások Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational

A jelszavas védelem proxyszolgáltatás is bejelentkezhetnek részletes hibakeresési szintű nyomkövetési események a következő naplóban: \Applications és a szolgáltatások Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace

> [!WARNING]
> A nyomkövetési napló alapértelmezés szerint le van tiltva. Ha engedélyezve van, ez a napló megkapja-e nagy mennyiségű esemény, és ez hatással lehet a teljesítményre a proxy-állomás. Ezért ezt a naplót kell csak akkor engedélyezhető, ha a probléma mélyebb vizsgálatra van szükség, és akkor is csak a lehető legrövidebb idő alatt.

### <a name="dc-agent-discovery"></a>DC-ügynök felderítése

A `Get-AzureADPasswordProtectionDCAgent` parancsmag is használható a különböző DC ügynökök, a tartomány vagy erdő futtató alapvető információkat jelenítenek meg. Ez az információ veszi át a serviceConnectionPoint objektumok a futó tartományvezérlő ügynök Services-szolgáltatás(ok) regisztrálja. Egy példa parancsmag kimenete a következőképpen történik:

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Minden egyes tartományvezérlő ügynök szolgáltatás hozzávetőleges óránként frissíti a tulajdonságait. Az adatok továbbra is az Active Directory replikációs késleltetése.

A parancsmag-lekérdezés hatókörén használatával befolyásolhatja az – erdő vagy a – tartományi paraméterek.

### <a name="emergency-remediation"></a>Sürgős szervizelés

Ha egy kellemetlen helyzet akkor fordul elő, ha a tartományvezérlő-ügynökszolgáltatás okozza a hibát, a DC-ügynökszolgáltatás is azonnal leáll. A tartományvezérlő ügynök jelszó-szűrő dll megkísérli a hívó nem fut, és figyelmeztető eseményeket (10012, 10013), de ebben az időszakban az összes bejövő jelszavak elfogadottak. A DC-ügynökszolgáltatás majd is konfigurálhatók keresztül a Windows szolgáltatásvezérlő indítási típusaként válassza az "Letiltott" igény szerint.

### <a name="performance-monitoring"></a>Teljesítményfigyelés

A tartományvezérlő szolgáltatás ügynökszoftver telepíti a teljesítményszámláló objektum neve **az Azure AD jelszóvédelem**. A következő teljesítményszámlálói jelenleg érhetők el:

|A Teljesítményfigyelő számláló neve | Leírás|
| --- | --- |
|A jelszavak feldolgozása |Ez a számláló utolsó újraindítás óta (vagy sem) feldolgozott jelszavak teljes számát jeleníti meg.|
|A jelszavak elfogadva |Ez a számláló, hogy az utolsó újraindítás óta fogadott teljes számát jeleníti meg.|
|A jelszavak elutasítva |Ez a számláló, hogy az utolsó újraindítás óta elutasított teljes számát jeleníti meg.|
|Jelszó kérelmek szűrése folyamatban |Ez a számláló a jelszó szűrő kérések számát jelenleg folyamatban jeleníti meg.|
|Csúcsidőszak jelszó kérelmek szűrése |Ez a számláló az utolsó újraindítás óta egyidejű jelszó kérelmek szűrése legmagasabb számát mutatja.|
|Jelszó szűrő kérelem hibák |Ez a számláló a jelszó kérelmek szűrése, amely az utolsó újraindítás óta hiba miatt sikertelen teljes számát jeleníti meg. Hiba akkor fordulhat elő, ha az Azure AD-jelszó DC védelmi ügynök szolgáltatás nem fut.|
|Jelszó szűrő kérelmek/másodperc |Ez a számláló a sebességet, amellyel jelszavak feldolgozott jeleníti meg.|
|Jelszó szűrő kérés feldolgozási ideje |Ez a számláló a jelszó szűrő kérelmek feldolgozásához szükséges átlagos időt mutatja.|
|Csúcsidőszak jelszó szűrő kérés feldolgozási ideje |Ez a számláló jeleníti meg a maximális jelszó szűrő kérés feldolgozási ideje az utolsó újraindítás óta.|
|Jelszavak elfogadott miatt rendszervizsgálati módban |Ez a számláló, hogy volna általában elutasították, de a rendszer fogadta, mert a jelszó házirend úgy van konfigurálva, hogy rendszervizsgálati módban (mert az utolsó újraindítás) teljes számát jeleníti meg.|

## <a name="directory-services-repair-mode"></a>A címtárszolgáltatás-javító módbeli

Ha a tartományvezérlő a Címtárszolgáltatások helyreállító módjában indult, a DC-ügynökszolgáltatás észlel, az összes jelszó érvényesítése vagy letiltását, függetlenül a jelenleg aktív házirend konfigurációs végrehajtó tevékenységek okozó.

## <a name="domain-controller-demotion"></a>Tartományvezérlő lefokozása

Fokozza a tartományvezérlőt, hogy továbbra is fut a tartományvezérlő ügynökszoftver esetén támogatott. A rendszergazdák érdemes figyelembe azonban, hogy a tartományvezérlő ügynökszoftver folyamatosan működik, és továbbra is fennáll, az aktuális jelszó házirend kényszerítése a lefokozás során. Az új helyi rendszergazdai fiók jelszavának (a lefokozási művelet részeként megadott) mint bármely más jelszó van hitelesítve. A Microsoft azt javasolja, hogy biztonságos jelszavak választják ki a helyi rendszergazdai fiókok a tartományvezérlő lefokozási eljárás; részeként azonban új helyi rendszergazdai fiók jelszavának a DC-ügynök szoftver érvényesítése a már meglévő lefokozás műveleti eljárásokká zavaró lehet.
A lefokozás sikeresen befejeződött, és a tartományvezérlő újraindítása után, és normál tagkiszolgálóként újra fut, a tartományvezérlő ügynökszoftver visszatér passzív módban. Előfordulhat, hogy majd távolítható bármikor.

## <a name="removal"></a>Eltávolítás

Ha úgy döntenek, hogy távolítsa el a nyilvános előzetes szoftverek és karbantartása minden kapcsolódó állapota a tartomány és erdő, ez a feladat valósítható meg az alábbi lépéseket követve:

> [!IMPORTANT]
> Fontos sorrendben hajtsa végre ezeket a lépéseket. Ha bármelyik példánya a jelszavas védelem Proxy szolgáltatás is fut fog rendszeres időközönként hozza létre újból a serviceConnectionPoint objektum, valamint rendszeres időközönként hozza létre újból a sysvol állapotát.

1. Távolítsa el a jelszavas védelem Proxy szoftver gépeire. Ez a lépés does **nem** a számítógép újraindítása szükséges.
2. Távolítsa el a tartományvezérlő ügynökszoftver minden tartományvezérlőről. Ez a lépés **szükséges** újraindítás.
3. Manuálisan távolítsa el az összes proxy szolgáltatás kapcsolódási pontja minden tartomány névhasználati környezetében. Ezek az objektumok helyét is észlelhetők a következő Active Directory Powershell-paranccsal:
   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{EBEFB703-6113-413D-9167-9F8DD4D24468}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   Nem hagyja ki ezt a csillag ("*") a $keywords változó értékét végén.

   Az eredményül kapott objektum keresztül található a `Get-ADObject` parancs majd átirányítható való `Remove-ADObject`, vagy manuálisan törölték. 

4. Manuálisan távolítsa el az összes tartományvezérlő ügynök kapcsolódási pontja minden tartomány névhasználati környezetében. Lehet, hogy egy ezek az objektumok egy olyan tartományvezérlőre, az erdőben, attól függően, hogy a nyilvános előzetes szoftver telepítve lett. Az objektum helyét előfordulhat, hogy történni, hogy a következő Active Directory Powershell-parancsot:

   ```
   $scp = “serviceConnectionPoint”
   $keywords = “{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*”
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -eq $keywords }
   ```

   Az eredményül kapott objektum keresztül található a `Get-ADObject` parancs majd átirányítható való `Remove-ADObject`, vagy manuálisan törölték.

5. Távolítsa el manuálisan az erdő szintű konfigurációs állapotát. Az erdő konfigurációs állapotát egy tárolót az Active Directory konfigurációs névhasználati környezetében megőrződik. Az felderíthető és törölni az alábbiak szerint:

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Az összes sysvol kapcsolódó állapot szerint manuálisan távolítsa el manuálisan a következő mappát és annak teljes tartalmát törlése:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ha szükséges, ezt az elérési utat is érhető helyileg egy adott tartományvezérlőn; az alapértelmezett hely a következő elérési út hasonlót néz ki:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ez az elérési út eltérő, ha a sysvol-megosztás egy nem alapértelmezett helyen lett konfigurálva.

## <a name="next-steps"></a>További lépések

További információ a globális és egyéni tiltott jelszó használatát, olvassa el a [rossz jelszavak bA](concept-password-ban-bad.md)

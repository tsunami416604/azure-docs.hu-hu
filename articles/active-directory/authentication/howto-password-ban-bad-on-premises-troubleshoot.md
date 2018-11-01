---
title: Hibaelhárítás és a bejelentkezés az Azure AD jelszó protection előzetes verziója
description: Megismerheti az Azure AD jelszóvédelem naplózás és a gyakori hibaelhárítási előzetes verzió
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: jsimmons
ms.openlocfilehash: 6832f6f9d09cbbfea6ccaa69160ad93209c7ac8c
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741181"
---
# <a name="preview-azure-ad-password-protection-monitoring-reporting-and-troubleshooting"></a>Előzetes verzió: Azure AD jelszó-védelem figyelési, jelentéseket és hibaelhárítás

|     |
| --- |
| Az Azure AD jelszóvédelem az Azure Active Directory nyilvános előzetes verziójú funkció. Előzetes verziók kapcsolatos további információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Az Azure AD jelszóvédelem telepítése után a figyelés és jelentéskészítés elvégzendő alapvető feladatok. Ez a cikk részletezik segítségével megismeri, ahol minden szolgáltatás információkat naplózza, és az Azure AD jelszóvédelem használatakor jelentése.

## <a name="on-premises-logs-and-events"></a>A helyszíni naplók és események

### <a name="dc-agent-service"></a>DC-ügynökszolgáltatás

Minden tartományvezérlőn, a tartományvezérlő szolgáltatás ügynökszoftver az eredményeket a jelszó-ellenőrzések (és más állapota) ír egy helyi Eseménynapló: \Applications és a szolgáltatások Logs\Microsoft\AzureADPasswordProtection\DCAgent\Admin

A különböző DC agent-összetevők használatával a következő tartományokra által naplózott eseményeket:

|Összetevő |Esemény tartomány|
| --- | --- |
|Tartományvezérlő ügynök jelszó szűrő dll| 10000 – 19999|
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

#### <a name="sample-event-log-message-for-event-id-10014-successful-password-set"></a>Minta eseménynapló-üzenet eseményazonosító 10014 sikeres jelszavának beállítása

A módosított jelszó a megadott felhasználó felel meg a jelenlegi Azure jelszóházirend lett érvényesítve.

 Felhasználónév: BPL_02885102771 FullName:

#### <a name="sample-event-log-message-for-event-id-10017-and-30003-failed-password-set"></a>Minta eseménynapló-üzenet eseményazonosító 10017, és nem sikerült 30003 jelszavának beállítása

10017:

A jelszó alaphelyzetbe állítása a megadott felhasználó el lett utasítva, mert nem felelt meg a jelenlegi Azure jelszóházirend. Tekintse át a további részletekért korrelált eseménynapló-üzenet.

 Felhasználónév: BPL_03283841185 FullName:

30003:

A jelszó alaphelyzetbe állítása a megadott felhasználó el lett utasítva, mert a jelenlegi Azure jelszóházirend bérlőnkénti le van tiltva jelszó listájában a jogkivonatok legalább egy egyezést mutat.

 Felhasználónév: BPL_03283841185 FullName:

Néhány egyéb fő Eseménynapló-üzenetek érdemes figyelembe vennie vannak:

#### <a name="sample-event-log-message-for-event-id-30001"></a>Eseményazonosító 30001 minta eseménynapló-üzenet

A megadott felhasználó jelszavát elfogadták, mert egy Azure jelszóházirend még nem érhető el

Felhasználónév: SomeUser FullName: néhány felhasználó

Ez a feltétel okozhatja egy vagy több, a következő okok miatt: % n

1. Az erdő tartalmaz még nem regisztrált az Azure-ral.

   A megoldás lépései: a rendszergazda regisztrálnia kell az erdő, a Register-AzureADPasswordProtectionForest parancsmag használatával.

2. Az Azure AD jelszóvédelem Proxy még nem áll rendelkezésre az adott erdőben található legalább egy számítógépen.

   A megoldás lépései: a rendszergazda telepítse és regisztrálja a Register-AzureADPasswordProtectionProxy parancsmaggal proxy.

3. A tartományvezérlő nincs hálózati kapcsolat és az Azure AD jelszó védelme Proxy példányok.

   A megoldás lépései: Győződjön meg arról, legalább egy Azure AD jelszó védelme Proxy példányra van hálózati kapcsolat.

4. A tartományvezérlő nem rendelkezik a többi tartományvezérlő a tartományban.

   A megoldás lépései: Győződjön meg arról, a tartományhoz van hálózati kapcsolat.

#### <a name="sample-event-log-message-for-event-id-30006"></a>Eseményazonosító 30006 minta eseménynapló-üzenet

A szolgáltatás most már a következő Azure jelszóházirend kényszerít.

 AuditOnly: 1

 Globális szabályzat dátuma: 2018. 05-15T00:00:00.000000000Z

 A bérlői házirend dátuma: 2018-06-10T20:15:24.432457600Z

 A bérlői szabályzat kényszerítése: 1

#### <a name="dc-agent-log-locations"></a>DC-ügynök a naplók helye

A tartományvezérlő-ügynökszolgáltatás is naplózza az eseményeket üzemeltetési vonatkozású, a következő naplófájl: \Applications és a szolgáltatások Logs\Microsoft\AzureADPasswordProtection\DCAgent\Operational

A tartományvezérlő-ügynökszolgáltatás is jelentkezhetnek hibakeresési szintűre részletes nyomkövetési események a következő naplófájl: \Applications és a szolgáltatások Logs\Microsoft\AzureADPasswordProtection\DCAgent\Trace

> [!WARNING]
> A nyomkövetési napló alapértelmezés szerint le van tiltva. Ha engedélyezve van, ez a napló nagy mennyiségű esemény fogadása, és hatással lehet a tartományvezérlő teljesítményét. Ezért a továbbfejlesztett napló kell csak akkor engedélyezhető, ha probléma mélyebb vizsgálatra van szükség, és akkor is csak egy minimális időtartamot.

### <a name="azure-ad-password-protection-proxy-service"></a>Az Azure AD jelszó protection proxy szolgáltatás

A jelszavas védelem Proxy szolgáltatás minimális események egy meghatározott készletének a következő eseménynaplóba bocsát ki: \Applications és a szolgáltatások Logs\Microsoft\AzureADPasswordProtection\ProxyService\Operational

A jelszavas védelem Proxy szolgáltatást is jelentkezhetnek hibakeresési szintűre részletes nyomkövetési események a következő naplófájl: \Applications és a szolgáltatások Logs\Microsoft\AzureADPasswordProtection\ProxyService\Trace

> [!WARNING]
> A nyomkövetési napló alapértelmezés szerint le van tiltva. Ha engedélyezve van, ez a napló fogadja-e nagy mennyiségű esemény, és hatással lehet a proxyállomás teljesítményét. Ezért ez a napló kell csak akkor engedélyezhető, ha a probléma mélyebb vizsgálatra van szükség, és akkor is csak egy minimális időtartamot.

### <a name="dc-agent-discovery"></a>DC-ügynök felderítése

A `Get-AzureADPasswordProtectionDCAgent` parancsmag is használható a különböző DC ügynökök futtatása a tartomány vagy erdő alapvető információit jeleníti meg. Ez az információ veszi át a serviceConnectionPoint objektumok a futó tartományvezérlő ügynök szolgáltatás(ok) által regisztrált. Egy példa a parancsmag kimenete a következőképpen történik:

```
PS C:\> Get-AzureADPasswordProtectionDCAgent
ServerFQDN            : bplChildDC2.bplchild.bplRootDomain.com
Domain                : bplchild.bplRootDomain.com
Forest                : bplRootDomain.com
Heartbeat             : 2/16/2018 8:35:01 AM
```

Minden egyes tartományvezérlő ügynökszolgáltatás hozzávetőleges számítónk különböző tulajdonságait frissíti. Az adatok a továbbra is az Active Directory replikáció késése.

A parancsmag lekérdezés hatókörét használatával befolyásolhatja a – erdőben vagy -Domain paraméterek.

### <a name="emergency-remediation"></a>Sürgős szervizelés

Ha egy kellemetlen helyzet áll elő, ha a tartományvezérlő-ügynökszolgáltatás problémákat okoz, a tartományvezérlő-ügynökszolgáltatás előfordulhat, hogy azonnal leáll. A tartományvezérlő ügynök jelszó szűrő dll megpróbálja meghívni a nem futó szolgáltatás, és naplózza az figyelmeztetési eseményeket (10012, 10013), de ebben az időszakban minden bejövő jelszót fogad. A tartományvezérlő-ügynökszolgáltatás majd is konfigurálhatók keresztül a Windows szolgáltatásvezérlő indítási típusaként válassza a "Letiltott" igény szerint.

### <a name="performance-monitoring"></a>Teljesítményfigyelés

A tartományvezérlő ügynök szolgáltatás szoftver telepítése a teljesítményszámláló objektum neve **az Azure AD jelszóvédelem**. Az alábbi teljesítményszámlálók jelenleg érhetők el:

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
> Fontos sorrendben hajtsa végre ezeket a lépéseket. Ha a jelszavas védelem Proxy szolgáltatás minden példányát futtatni fogja rendszeres időközönként hozza létre újból a serviceConnectionPoint objektum, valamint rendszeres időközönként hozza létre újból a sysvol-állapot.

1. Távolítsa el a jelszavas védelem Proxy szoftver összes gép. Ebben a lépésben does **nem** újraindítása szükséges.
2. Távolítsa el a tartományvezérlő ügynökszoftver az összes tartományvezérlőről. Ebben a lépésben **igényel** újraindítás.
3. Manuálisan távolítsa el a proxy összes szolgáltatáskapcsolati pontját, az egyes tartományi névhasználati környezeten. Ezek az objektumok helyét is felderítése a következő Active Directory Powershell-paranccsal:
   ```
   $scp = "serviceConnectionPoint"
   $keywords = "{EBEFB703-6113-413D-9167-9F8DD4D24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Nem hagyja ki a csillag ("*") a $keywords változó értékét a végén.

   Az eredményül kapott objektumhoz keresztül található a `Get-ADObject` parancs majd átadható olyan parancsoknak `Remove-ADObject`, vagy manuálisan törölték. 

4. Manuálisan távolítsa el az összes tartományvezérlő ügynök csatlakozási pontok minden tartományi névhasználati környezetében. Lehet, hogy egy ezeket az objektumokat az erdő, attól függően, hogyan széles körben a nyilvános előzetes verziójú szoftver telepítve lett a tartományvezérlőnkénti. Előfordulhat, hogy felderítése a helyét annak az objektumnak a következő Active Directory Powershell-paranccsal:

   ```
   $scp = "serviceConnectionPoint"
   $keywords = "{B11BB10A-3E7D-4D37-A4C3-51DE9D0F77C9}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Az eredményül kapott objektumhoz keresztül található a `Get-ADObject` parancs majd átadható olyan parancsoknak `Remove-ADObject`, vagy manuálisan törölték.

5. Manuálisan távolítsa el az erdő szintű konfigurációs állapotát. Az erdő konfigurációs állapotát egy tárolót az Active Directory konfigurációelnevezési környezetében a változatlan marad. Ez felderíthető és törölni az alábbiak szerint:

   ```
   $passwordProtectonConfigContainer = "CN=Azure AD password protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject $passwordProtectonConfigContainer
   ```

6. Manuálisan távolítsa el az összes sysvol-állapot manuálisan kapcsolódó törlése a következő mappához és annak teljes tartalmát:

   `\\<domain>\sysvol\<domain fqdn>\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ha szükséges, ezt az elérési utat is is elérhető, helyileg egy adott tartományvezérlőn; az alapértelmezett hely a következő elérési út hasonló lenne:

   `%windir%\sysvol\domain\Policies\{4A9AB66B-4365-4C2A-996C-58ED9927332D}`

   Ez az elérési út eltérő, ha a sysvol-megosztás egy nem alapértelmezett helyen lett konfigurálva.

## <a name="next-steps"></a>További lépések

A globális és egyéni letiltott jelszavak listáit további információkért tekintse meg a cikket [rossz jelszavak letiltása](concept-password-ban-bad.md)

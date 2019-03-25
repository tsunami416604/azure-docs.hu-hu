---
title: Azonosíthatja a problémákat a Windows virtuális asztal előzetes diagnosztikai szolgáltatás – Azure
description: A Windows virtuális asztal előzetes diagnosztikai funkciót és való használatát ismerteti.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: a9b8be58e8dfb27fbe896cf1c8d8dc0e91e3b24c
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58402847"
---
# <a name="identify-issues-with-the-diagnostics-feature"></a>Azonosíthatja a problémákat a diagnosztikai szolgáltatás

Windows Virtual Desktop előzetes verziójához biztosít diagnosztikai funkcióval, amely lehetővé teszi a rendszergazdák egyetlen felületen keresztül problémák azonosításához. A virtuális asztali Windows-szerepkörök jelentkezzen a diagnosztikai tevékenységet, amikor a felhasználó kommunikál a rendszer. Minden napló vonatkozó információkat, például a Windows virtuális asztal szerepkörök részt vesz a tranzakciót, hibaüzenetek, bérlői kapcsolatos információkat és felhasználói adatokat tartalmazza. Diagnosztikai tevékenységek jönnek létre mind a végfelhasználói, mind a felügyeleti műveleteket, és három fő gyűjtők osztályozhatók:

* Hírcsatorna-előfizetés tevékenységek: a végfelhasználó elindítja ezeket a tevékenységeket, amikor megpróbálnak csatlakozni a Microsoft távoli asztali alkalmazásokon keresztül hírcsatorna.
* Kapcsolat-tevékenységek: a végfelhasználó elindítja ezeket a tevékenységeket, amikor megpróbálnak egy asztali vagy RemoteApp Microsoft távoli asztali alkalmazásokon keresztül csatlakozni.
* Felügyeleti tevékenység: a rendszergazda aktiválja ezeket a tevékenységeket, amikor azok a rendszer, például a gazdagép-címkészletek létrehozása, felhasználók hozzárendelése az alkalmazás és szerepkör-hozzárendelések létrehozása műveletek végrehajtása.
  
Kapcsolatok, amelyek nem érik el a Windows virtuális asztal nem jelennek meg a diagnosztikai eredményeket mert maga diagnosztikai szerepkör-szolgáltatás része a Windows virtuális asztal. Windows virtuális asztali kapcsolatok problémáinak akkor fordulhat elő, amikor a végfelhasználó tapasztalja hálózati problémák léptek fel.

Első lépésként [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

## <a name="diagnose-issues-with-powershell"></a>Diagnosztizálhatja a problémákat a PowerShell-lel

Windows virtuális asztal diagnosztika csak egy PowerShell-parancsmagot használja, de leszűkíthet és problémák kiszűréséhez számos választható paramétereket tartalmaz. Az alábbi szakaszok tartalmazzák a parancsmagok futtatásával diagnosztizálhatja a problémákat. A legtöbb szűrők együtt is alkalmazható. Zárójelben, például a felsorolt értékeket `<tenantName>`, le kell cserélni az értékeket, amelyeket a alkalmazni az adott helyzethez.

### <a name="retrieve-diagnostic-activities-in-your-tenant"></a>A bérlő diagnosztikai tevékenységek beolvasása

Diagnosztikai tevékenységek megadásával lekérheti a **Get-RdsDiagnosticsActivities** parancsmagot. Az alábbi példaparancsmagot listáját adja vissza a diagnosztikai tevékenységek, csökkenő a legrégebbi.

```powershell
Get-RdsDiagnosticsActivities -TenantName <tenantName>
```

Hasonló más Windows virtuális asztal PowerShell parancsmagokat kell használnia a **- TenantName** paraméterrel adja meg a lekérdezésben használni szeretne a bérlő nevével. A bérlő neve akkor szinte minden diagnosztikai tevékenység lekérdezések.

### <a name="retrieve-detailed-diagnostic-activities"></a>Részletes diagnosztikai tevékenységek beolvasása

A **– részletes** paraméter további részleteket biztosít minden egyes visszaküldött diagnosztikai tevékenységhez. Minden egyes tevékenységhez formátuma a tevékenység típusa függvényében. A **– részletes** paraméterrel adhatók hozzá bármelyik **Get-RdsDiagnosticsActivities** lekérdezés, az alábbi példában látható módon.

```powershell
Get-RdsDiagnosticsActivities -TenantName <tenantName> -Detailed
```

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Lekérni egy adott diagnosztikai tevékenység által tevékenység azonosítója

A **– tevékenységazonosító** paraméter visszaadja az egy adott tevékenység diagnosztikai, ha létezik, az alábbi parancsmag-példában látható módon.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="filter-diagnostic-activities-by-user"></a>A felhasználó diagnosztikai tevékenységek szűrése

A **- UserName** paraméter visszaadja a megadott felhasználó által kezdeményezett diagnosztikai tevékenységek listáját az alábbi parancsmag-példában látható módon.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

A **- UserName** paraméter is kombinálhatók más választható szűrési paraméterekkel.

### <a name="filter-diagnostic-activities-by-time"></a>Idő szerinti szűrésre diagnosztikai tevékenységek

A visszaadott diagnosztikai tevékenység listát szűrheti a **- StartTime** és **- EndTime** paramétereket. A **- StartTime** paraméter listáját adja vissza diagnosztikai tevékenység egy adott dátumtól kezdve az alábbi példában látható módon.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018"
```

A **- EndTime** paraméter lehet hozzáadni egy parancsmagot a **- StartTime** paraméterrel adja meg egy adott időszakra vonatkozóan szeretne eredményeket kapni. Az alábbi parancsmag-példában a munkafüzet augusztus 1-én és augusztus 10 diagnosztikai tevékenységek listáját adja vissza.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

A **- StartTime** és **- EndTime** paramétereket is kombinálhatók más választható szűrési paraméterekkel.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Diagnosztikai tevékenységeket tevékenység típus szerint

Diagnosztikai tevékenységek a tevékenységek típus szerint is szűrheti a **- tevékenységtípus** paraméter. A következő parancsmagot a végfelhasználói kapcsolatok listáját adja vissza:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Connection
```

A következő parancsmagot rendszergazda felügyeleti feladatok listáját adja vissza:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

A **Get-RdsDiagnosticActivities** parancsmag jelenleg nem támogatja a hírcsatorna-t a tevékenységtípus adja meg.

### <a name="filter-diagnostic-activities-by-outcome"></a>Diagnosztikai tevékenységek szűrés eredménye

A visszaadott diagnosztikai tevékenységek listája az eredmény szerint szűrheti a **-serkenti az eredményt** paraméter. Az alábbi példaparancsmagot sikeres diagnosztikai tevékenységek listáját adja vissza.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Success
```

Az alábbi példaparancsmagot sikertelen diagnosztikai tevékenységek listáját adja vissza.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

A **-serkenti az eredményt** paraméter is kombinálhatók más választható szűrési paraméterekkel.

## <a name="common-error-scenarios"></a>Gyakori hiba forgatókönyvek

Hiba forgatókönyvek szerint vannak kategóriába sorolva a szolgáltatás belső és külső virtuális asztali Windows.

* Belső hiba: Itt adhatja meg a forgatókönyvek, melyek nem szünteti meg a bérlői rendszergazda és a támogatási problémát, fel kell oldani. Ha egy a jegy létrehozása adja meg a tevékenység azonosítója, a bérlő nevét és hozzávetőleges időkeretre a probléma lépett fel.
* Külső probléma: a forgatókönyvek, melyek a rendszergazda enyhíthetők vonatkoznak. Ezek a külső virtuális asztali Windows.

Az alábbi táblázat a gyakori hibák a rendszergazdák mutatjuk be.

>[!NOTE]
>Ebben az előzetes verzióban nem tartalmazza a teljes hibák kategorizálása és rendszeresen frissülnek. Gondoskodjon arról, hogy a legfrissebb információk, hogy ellenőrizze, ez a cikk újbóli bekapcsolásakor legalább egyszer egy hónapban kell lennie.

### <a name="external-management-error-codes"></a>Külső felügyeleti hibakódok

|Numerikus kód|Hibakód|Javasolt megoldás|
|---|---|---|
|3|UnauthorizedAccess|A felhasználó, aki megkísérelte futtatni a felügyeleti PowerShell-parancsmag nem rendelkezik az ehhez szükséges engedélyeket, vagy rosszul adott meg a felhasználóneve.|
|1000|TenantNotFound|A megadott bérlő neve nem felel meg minden olyan meglévő bérlők számára. Tekintse át az elgépelések bérlőjének a neve, és próbálkozzon újra.|
|1006|TenantCannotBeRemovedHasSessionHostPools|A bérlő nem törölhető, mert hosszú objektumokat tartalmaz. A munkamenet-gazdagép készletek először törölje, majd próbálkozzon újra.|
|2000|HostPoolNotFound|A megadott gazdagép-készlet neve nem felel meg minden olyan meglévő gazdagép-készletek. Tekintse át az elgépelések készlet állomásnevét, majd próbálkozzon újra.|
|2005|HostPoolCannotBeRemovedHasApplicationGroups|A gazdagép-készlet nem törölhető, mindaddig, amíg objektumokat tartalmaz. Először távolítsa el az összes alkalmazás-csoport a gazdagép-készletben.|
|2004|HostPoolCannotBeRemovedHasSessionHosts|Távolítsa el a munkamenet gazdagép-készlet törlése előtt minden munkamenetek gazdagép.|
|5001|SessionHostNotFound|Előfordulhat, hogy a lekérdezett munkamenetgazda offline állapotban van. Ellenőrizze a gazdagép-készlet állapotát.|
|5008|SessionHostUserSessionsExist |Az importálni kívánt felügyeleti tevékenység végrehajtása előtt jelentkezzen ki minden felhasználó a munkamenet-gazdagépen.|
|6000|AppGroupNotFound|A megadott alkalmazás-csoport neve nem egyezik a meglévő alkalmazás csoportnak sem. Tekintse át az alkalmazás neve az elgépelések, és próbálkozzon újra.|
|6022|RemoteAppNotFound|A RemoteApp megadott neve nem felel meg minden olyan távoli alkalmazások. Tekintse át az elgépelések RemoteApp nevét, és próbálkozzon újra.|
|6010|PublishedItemsExist|A közzétenni kívánt erőforrás nevét megegyezik egy már létező erőforrást. Módosítsa az erőforrás nevét, majd próbálkozzon újra.|
|7002|NameNotValidWhiteSpace|A névben ne használjon szóközöket.|
|8000|InvalidAuthorizationRoleScope|A megadott szerepkör neve nem felel meg minden olyan meglévő szerepkörneveket. Tekintse át az elgépelések szerepkör nevét, és próbálkozzon újra. |
|8001|UserNotFound |A megadott felhasználónév nem egyezik meg minden olyan meglévő felhasználói neveket. Tekintse át az elgépelések nevét, és próbálkozzon újra.|
|8005|UserNotFoundInAAD |A megadott felhasználónév nem egyezik meg minden olyan meglévő felhasználói neveket. Tekintse át az elgépelések nevét, és próbálkozzon újra.|
|8008|TenantConsentRequired|Kövesse az utasításokat [Itt](tenant-setup-azure-active-directory.md#grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service) történő a bérlő számára.|

### <a name="external-connection-error-codes"></a>Külső kapcsolatok hibakódok

|Numerikus kód|Hibakód|Javasolt megoldás|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|A munkamenetgazda nem megfelelően csatlakozik az Active Directoryban.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|A kapcsolatok nem sikerült, mert a munkamenet-gazdagép nem érhető el. A munkamenetgazda állapotának ellenőrzése.|
|-2146233088|ConnectionFailedClientDisconnect|Ha ez a hiba gyakran lát, győződjön meg arról, a felhasználó csatlakozik a hálózathoz.|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|A gazdagép felhasználó próbált meg csatlakozni a munkamenet nem kifogástalan állapotú. A virtuális gép hibakeresése.|
|-2146233088|ConnectionFailedUserNotAuthorized|A felhasználó nem rendelkezik hozzáféréssel a közzétett alkalmazás vagy asztal. A hiba jelenhet meg a rendszergazda a közzétett erőforrások eltávolítása után. Kérje meg a felhasználót, hogy a hírcsatorna a távoli asztal alkalmazás frissítése.|
|2|FileNotFound|A felhasználó megpróbált hozzáférni az alkalmazás nem megfelelően vagy telepített beállítása helytelen elérési utat.|
|3|InvalidCredentials|A felhasználónév vagy a felhasználó által megadott jelszó nem felel meg, bármely meglévő felhasználóneveket vagy jelszavakat. Tekintse át az elgépelések hitelesítő adatait, és próbálkozzon újra.|
|8|ConnectionBroken|Az ügyfél és az átjáró vagy a kiszolgáló közötti kapcsolat megszakadt. Nincs szükség művelet végrehajtására, kivéve, ha váratlanul történik.|
|14|UnexpectedNetworkDisconnect|A hálózati kapcsolat megszakadt. Kérje meg a felhasználó újra kapcsolódni.|
|24|ReverseConnectFailed|A gazdagép virtuális gép nincs közvetlen üzemel, a távoli asztali átjáró rendelkezik. Győződjön meg arról, az átjáró IP-cím feloldható legyen.|

## <a name="next-steps"></a>További lépések

Windows virtuális asztal szerepkörei kapcsolatos további információkért lásd: [Windows virtuális asztal előzetes verziójú környezet](environment-setup.md).

Elérhető PowerShell-parancsmagok listáját a Windows virtuális asztal, olvassa el a [PowerShell-referencia](/powershell/windows-virtual-desktop/overview).
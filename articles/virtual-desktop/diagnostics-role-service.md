---
title: Windows virtuális asztal diagnosztizálása problémák - Azure
description: A Windows virtuális asztal diagnosztikai szolgáltatásának használata a problémák diagnosztizálására.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce85fb70e1480ad285eee78fe20faa8d77b9a147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254260"
---
# <a name="identify-and-diagnose-issues"></a>Problémák azonosítása és diagnosztizálása

A Windows virtuális asztal diagnosztikai szolgáltatást kínál, amely lehetővé teszi a rendszergazda számára a problémák azonosítását egyetlen felületen keresztül. A Windows virtuális asztal szerepkörei diagnosztikai tevékenységet naplóznak, amikor a felhasználó a rendszerrel kommunikál. Minden napló olyan releváns információkat tartalmaz, mint például a tranzakcióban részt vevő Windows virtuális asztal szerepkörök, a hibaüzenetek, a bérlői adatok és a felhasználói adatok. A diagnosztikai tevékenységeket végfelhasználói és felügyeleti műveletek is létrehozják, és három fő gyűjtőbe sorolhatók:

* Hírcsatorna-előfizetési tevékenységek: a végfelhasználó ezeket a tevékenységeket akkor indítja el, amikor microsoft távoli asztali alkalmazásokon keresztül próbálnak csatlakozni a hírcsatornájukhoz.
* Csatlakozási tevékenységek: a végfelhasználó ezeket a tevékenységeket akkor indítja el, amikor microsoft távoli asztali alkalmazásokon keresztül próbálnak csatlakozni egy asztalhoz vagy RemoteApp-hoz.
* Felügyeleti tevékenységek: a rendszergazda minden alkalommal elindítja ezeket a tevékenységeket, amikor felügyeleti műveleteket hajtanak végre a rendszeren, például gazdagépkészleteket hoznak létre, felhasználókat rendelnek az alkalmazáscsoportokhoz, és szerepkör-hozzárendeléseket hoznak létre.
  
Azok a kapcsolatok, amelyek nem érik el a Windows virtuális asztalt, nem jelennek meg a diagnosztikai eredményekben, mert maga a diagnosztikai szerepkör-szolgáltatás része a Windows virtuális asztalnak. A Windows virtuális asztal csatlakozási problémái akkor fordulhatnak elő, ha a végfelhasználó hálózati kapcsolattal kapcsolatos problémákat tapasztal.

A kezdéshez [töltse le és importálja a Windows Virtual Desktop PowerShell-modult,](/powershell/windows-virtual-desktop/overview/) amelyet a PowerShell-munkamenetben használhat, ha még nem tette meg. Ezután futtassa a következő parancsmast a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="diagnose-issues-with-powershell"></a>A PowerShellnel kapcsolatos problémák diagnosztizálása

A Windows virtuális asztali diagnosztika csak egy PowerShell-parancsmacsat használ, de számos választható paramétert tartalmaz a problémák leszűkítéséhez és elkülönítéséhez. A következő szakaszok a problémák diagnosztizálására futtatható parancsmagokat sorolják fel. A legtöbb szűrő együtt alkalmazható. A zárójelben felsorolt értékeket, például `<tenantName>`a , a helyzetre vonatkozó értékekre kell cserélni.

>[!IMPORTANT]
>A diagnosztikai funkció egyfelhasználós hibaelhárításra alkalmas. A PowerShellt használó összes lekérdezésnek tartalmaznia kell a *-UserName* vagy *a -ActivityID* paramétereket. A figyelési képességek, használja a Log Analytics. A diagnosztikai adatok munkaterületre küldéséről a Diagnosztikai adatok küldéséről a Diagnosztikai adatok használatával a [diagnosztikai funkcióhoz](diagnostics-log-analytics.md) című témakörben talál. 

### <a name="filter-diagnostic-activities-by-user"></a>Diagnosztikai tevékenységek szűrése felhasználó szerint

A **-UserName** paraméter a megadott felhasználó által kezdeményezett diagnosztikai tevékenységek listáját adja vissza, ahogy az a következő példaparancsmagban látható.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN>
```

A **-UserName** paraméter más választható szűrési paraméterekkel is kombinálható.

### <a name="filter-diagnostic-activities-by-time"></a>Diagnosztikai tevékenységek szűrése idő szerint

A visszaadott diagnosztikai tevékenységlista a **-StartTime** és **-EndTime** paraméterekkel szűrhető. A **-StartTime** paraméter egy adott dátumtól kezdődődiagnosztikai tevékenységlistát ad vissza, ahogy az a következő példában látható.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018"
```

A **-EndTime** paraméter hozzáadható egy parancsmaghoz a **-StartTime** paraméterrel, hogy megadhatja azt az időtartamot, amelyhez eredményeket szeretne kapni. A következő példa parancsmag visszaadja a diagnosztikai tevékenységek listáját augusztus 1 és augusztus 10 között.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -StartTime "08/01/2018" -EndTime "08/10/2018"
```

A **-StartTime** és **-EndTime** paraméterek más választható szűrési paraméterekkel is kombinálhatók.

### <a name="filter-diagnostic-activities-by-activity-type"></a>Diagnosztikai tevékenységek szűrése tevékenységtípus szerint

A diagnosztikai tevékenységeket tevékenységtípus szerint is szűrheti a **-ActivityType** paraméterrel. A következő parancsmag a végfelhasználói kapcsolatok listáját adja vissza:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -ActivityType Connection
```

A következő parancsmag a rendszergazdai felügyeleti feladatok listáját adja vissza:

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityType Management
```

A **Get-RdsDiagnosticActivities** parancsmag jelenleg nem támogatja a hírcsatorna ActivityType típusként történő megadását.

### <a name="filter-diagnostic-activities-by-outcome"></a>Diagnosztikai tevékenységek szűrése eredmény szerint

A visszaadott diagnosztikai tevékenységlista eredmény szerint szűrhető a **-Outcome** paraméterrel. A következő példa parancsmag a sikeres diagnosztikai tevékenységek listáját adja vissza.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -UserName <UserUPN> -Outcome Success
```

A következő példa parancsmag a sikertelen diagnosztikai tevékenységek listáját adja vissza.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -Outcome Failure
```

A **-Outcome** paraméter más választható szűrési paraméterekkel is kombinálható.

### <a name="retrieve-a-specific-diagnostic-activity-by-activity-id"></a>Adott diagnosztikai tevékenység lekérése tevékenységazonosító szerint

A **-ActivityId** paraméter egy adott diagnosztikai tevékenységet ad vissza, ha létezik, ahogy az a következő példa parancsmagban látható.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityIdGuid>
```

### <a name="view-error-messages-for-a-failed-activity-by-activity-id"></a>Sikertelen tevékenységhibaüzenetek megtekintése tevékenységazonosító szerint

A sikertelen tevékenység hibaüzeneteinek megtekintéséhez a **-Detailed** paraméterrel kell futtatnia a parancsmatot. A hibák listáját az **Objektum-kijelölés** parancsmag futtatásával tekintheti meg.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantname> -ActivityId <ActivityGuid> -Detailed | Select-Object -ExpandProperty Errors
```

### <a name="retrieve-detailed-diagnostic-activities"></a>Részletes diagnosztikai tevékenységek lekérése

A **-Részletes** paraméter további részleteket tartalmaz az egyes visszaadott diagnosztikai tevékenységekhez. Az egyes tevékenységek formátuma a tevékenység típusától függően változik. A **-Részletes** paraméter bármely **Get-RdsDiagnosticActivities** lekérdezéshez hozzáadható, ahogy az a következő példában látható.

```powershell
Get-RdsDiagnosticActivities -TenantName <tenantName> -ActivityId <ActivityGuid> -Detailed
```

## <a name="common-error-scenarios"></a>Gyakori hibaforgatókönyvek

A hibaesetek a szolgáltatáson belül és a Windows virtuális asztalon kívül vannak kategorizálva.

* Belső probléma: olyan forgatókönyveket határoz meg, amelyeket a bérlői rendszergazda nem tud enyhíteni, és támogatási problémaként kell megoldani. Amikor visszajelzést [aD](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)át a Windows Virtual Desktop Tech Community , tartalmazza a tevékenység azonosítóját és hozzávetőleges időkeretet, amikor a probléma történt.
* Külső probléma: olyan forgatókönyvekhez kapcsolódik, amelyeket a rendszergazda enyhíthet. Ezek a Windows virtuális asztalon kívül találhatók.

Az alábbi táblázat azokat a gyakori hibákat sorolja fel, amelyekbe a rendszergazdák találkozhatnak.

>[!NOTE]
>Ez a lista tartalmazza a leggyakoribb hibákat, és rendszeres ütemben frissül. Annak érdekében, hogy a legfrissebb információkkal rendelkezzen, legalább havonta egyszer ellenőrizze ezt a cikket.

### <a name="external-management-error-codes"></a>Külső felügyeleti hibakódok

|Numerikus kód|Hibakód|Javasolt megoldás|
|---|---|---|
|3|Jogosulatlan hozzáférés|A felügyeleti PowerShell-parancsmag futtatását megkísérlő felhasználó vagy nem rendelkezik jogosultsággal ehhez, vagy elgépelte a felhasználónevét.|
|1000|A bérlő nem található|A megadott bérlői név nem egyezik egyetlen meglévő bérlővel sem. Tekintse át az elírások bérlői nevét, majd próbálkozzon újra.|
|1006|TenantCannotBeRemovedHasSessionHostPools|A bérlő nem törölhető addig, amíg az objektumokat tartalmaz. Először törölje a munkamenetgazda-készleteket, majd próbálkozzon újra.|
|2000|HostPoolNem alapítva|A megadott állomáskészlet neve nem egyezik egyetlen meglévő állomáskészletnek sem. Tekintse át az elírások gazdakészletének nevét, majd próbálkozzon újra.|
|2005|A hostpool nem távolítható elalkalmazáscsoportok|A gazdagépkészlet nem törölhető, amíg objektumokat tartalmaz. Először távolítsa el az összes alkalmazáscsoportot a gazdakészletből.|
|2004|HostPool nem távolítható elhassessionhosts|A munkamenetgazda-készlet törlése előtt távolítsa el az összes munkamenet-állomást.|
|5001|SessionHostNem található|Lehet, hogy a lekérdezett munkamenet-gazdagép offline állapotban van. Ellenőrizze a gazdakészlet állapotát.|
|5008|SessionHostUserSessionsExist |A tervezett felügyeleti tevékenység végrehajtása előtt ki kell jelentkeznie a munkamenet-gazdagépen lévő összes felhasználóból.|
|6000|AppGroupNem található|A megadott alkalmazáscsoport neve nem egyezik egyetlen meglévő alkalmazáscsoporttal sem. Tekintse át az elírások alkalmazáscsoportjának nevét, majd próbálkozzon újra.|
|6022|RemoteAppNem található|A megadott RemoteApp-név nem egyezik egyetlen RemoteApps alkalmazással sem. Tekintse át az elírások RemoteApp-nevét, majd próbálkozzon újra.|
|6010|PublishedItemsExist|A közzétenni kívánt erőforrás neve megegyezik a már létező erőforrás nevével. Módosítsa az erőforrás nevét, majd próbálkozzon újra.|
|7002|NameNotValidWhiteSpace|Ne használjon szóközt a névben.|
|8000|InvalidAuthorizationRoleScope|A megadott szerepkörnév nem egyezik egyetlen meglévő szerepkörnévvel sem. Tekintse át az elírások szerepkörnevét, majd próbálkozzon újra. |
|8001|A felhasználó nem található |A megadott felhasználónév nem egyezik egyetlen meglévő felhasználónévvel sem. Tekintse át az elírások nevét, majd próbálkozzon újra.|
|8005|UserNotFoundinaad |A megadott felhasználónév nem egyezik egyetlen meglévő felhasználónévvel sem. Tekintse át az elírások nevét, majd próbálkozzon újra.|
|8008|Bérlőhozzájárulásszükséges|Kövesse az [itt](tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) található utasításokat, hogy beleegyezését adja a bérlőnek.|

### <a name="external-connection-error-codes"></a>Külső kapcsolathibakódok

|Numerikus kód|Hibakód|Javasolt megoldás|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|A munkamenetgazda nincs megfelelően csatlakoztatva az Active Directoryhoz.|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|A kapcsolatok nem sikerültek, mert a munkamenetgazda nem érhető el. Ellenőrizze a munkamenet gazdagépének állapotát.|
|-2146233088|ConnectionFailedClientDisconnect|Ha gyakran jelenik meg ez a hiba, ellenőrizze, hogy a felhasználó számítógépe csatlakozik-e a hálózathoz.|
|-2146233088|ConnectionFailedNoHealthyRdshElérhető|A munkamenet, amelyhez a gazdagép felhasználója megpróbált csatlakozni, nem kifogástalan. Hibakeresés a virtuális gép.|
|-2146233088|ConnectionFailedUserNotAuthorized|A felhasználónak nincs engedélye a közzétett alkalmazás vagy asztal elérésére. A hiba akkor jelenhet meg, amikor a rendszergazda eltávolította a közzétett erőforrásokat. Kérje meg a felhasználót, hogy frissítse a hírcsatornát a Távoli asztal alkalmazásban.|
|2|A fájl nem található|Az alkalmazás, amelyet a felhasználó megpróbált elérni, helytelenül van telepítve, vagy helytelen elérési útra van állítva.|
|3|Érvénytelen Hitelesítő adatok|A felhasználó által megadott felhasználónév vagy jelszó nem egyezik meg egyetlen meglévő felhasználónévvel vagy jelszóval sem. Tekintse át az elírások hitelesítő adatait, majd próbálkozzon újra.|
|8|Kapcsolattörlve|Megszakadt a kapcsolat az ügyfél és az átjáró vagy a kiszolgáló között. Nincs szükség cselekvésre, hacsak nem váratlanul történik.|
|14|VáratlanHálózatleválasztás|Megszakadt a kapcsolat a hálózattal. Kérje meg a felhasználót, hogy csatlakozzon újra.|
|24|ReverseConnectFailed|A gazdavirtuális gép nek nincs közvetlen látómezeje a Távoli asztali átjáróhoz. Győződjön meg arról, hogy az átjáró IP-címe feloldható.|

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni a Windows virtuális asztalon belüli szerepkörökről, olvassa el a [Windows virtuális asztal környezetcímű témakört.](environment-setup.md)

A Windows Virtual Desktop elérhető PowerShell-parancsmagjainak listáját a [PowerShell-hivatkozás című témakörben láthatja.](/powershell/windows-virtual-desktop/overview)

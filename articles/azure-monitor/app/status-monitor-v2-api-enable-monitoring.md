---
title: 'Az Azure Állapotmonitor v2 API-referenciája: Figyelés engedélyezése | Microsoft Docs'
description: Állapotmonitor v2 API-referenciát. Enable-ApplicationInsightsMonitoring. Webhelyek teljesítményének figyelése a webhely újbóli üzembe helyezése nélkül. Együttműködik a helyszínen, a virtuális gépeken vagy az Azure-on üzemeltetett ASP.NET Web Apps szolgáltatásokkal.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d3963889e3604fb67cb526b992e7ca27b1212b59
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326356"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring"></a>Állapotmonitor v2 API: ApplicationInsightsMonitoring engedélyezése

Ez a cikk olyan parancsmagot ismertet, amely az az [. ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)tagja.

## <a name="description"></a>Leírás

Engedélyezi az IIS-alkalmazások kód alapú csatolásának figyelését a célszámítógépen.

Ez a parancsmag módosítja az IIS applicationHost. config fájlt, és beállítja a beállításkulcsokat.
Emellett létrehoz egy applicationinsights. rendszerállapotkulcsot. config fájlt is, amely meghatározza az egyes alkalmazások által használt kialakítási kulcsot.
Az IIS betölti a RedfieldModule az indításkor, amely az alkalmazások indításakor beadja a Application Insights SDK-t az alkalmazásokba.
A módosítások érvénybe léptetéséhez indítsa újra az IIS-t.

Miután engedélyezte a figyelést, javasoljuk, hogy [élő metrikák](live-stream.md) használatával gyorsan ellenőrizze, hogy az alkalmazás küldjön-e nekünk telemetria.


> [!NOTE] 
> - Első lépésként szükség van egy kialakítási kulcsra. További információt az [erőforrás létrehozása](create-new-resource.md#copy-the-instrumentation-key)című témakörben talál.
> - Ehhez a parancsmaghoz meg kell vizsgálni és el kell fogadnia a licenc-és adatvédelmi nyilatkozatot.

> [!IMPORTANT] 
> Ehhez a parancsmaghoz rendszergazdai engedélyekkel rendelkező PowerShell-munkamenetre és emelt szintű végrehajtási szabályzatra van szükség. További információ: a [PowerShell futtatása rendszergazdaként emelt szintű végrehajtási házirenddel](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Példák

### <a name="example-with-a-single-instrumentation-key"></a>Példa egyetlen kialakítási kulccsal
Ebben a példában az aktuális számítógépen lévő összes alkalmazás egyetlen kialakítási kulcshoz van rendelve.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Példa a kialakítási kulcs megfeleltetésére
Ebben a példában:
- `MachineFilter`az aktuális számítógépnek felel meg a `'.*'` helyettesítő karakter használatával.
- `AppFilter='WebAppExclude'``null` rendszerállapot-kulcsot biztosít. A megadott alkalmazás nem lesz kialakítva.
- `AppFilter='WebAppOne'`egy egyedi kialakítási kulcsot rendel hozzá a megadott alkalmazáshoz.
- `AppFilter='WebAppTwo'`egy egyedi kialakítási kulcsot rendel hozzá a megadott alkalmazáshoz.
- Végezetül a`'.*'` helyettesítő karaktert ishasználjaakorábbiszabályoknaknemmegfelelőwebalkalmazásokésazalapértelmezettkialakításikulcshozzárendeléséhez.`AppFilter`
- A szóközök hozzáadódnak az olvashatósághoz.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Paraméterek

### <a name="-instrumentationkey"></a>– InstrumentationKey
**Szükséges.** Ezzel a paraméterrel egyetlen rendszerállapot-kulcsot adhat meg a célszámítógépen lévő összes alkalmazás számára.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Szükséges.** Ezzel a paraméterrel több rendszerállapot-kulcs és az egyes alkalmazások által használt rendszerállapot-kulcsok leképezése is megadható.
A beállítással `MachineFilter`egyetlen telepítési parancsfájlt is létrehozhat több számítógép számára.

> [!IMPORTANT]
> Az alkalmazások a szabályok megadásának sorrendjében egyeznek meg a szabályokkal. Ezért a legpontosabb szabályokat kell megadnia az első és a legáltalánosabb szabályok közül.

#### <a name="schema"></a>Séma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- A **MachineFilter** a számítógép C# vagy a virtuális gép nevének kötelező regexje.
    - a ". *" mindennek megfelel
    - A "számítógépnév" csak a pontosan megadott névvel rendelkező számítógépekhez fog megfelelni.
- A **AppFilter** az IIS C# -hely nevének kötelező regexje. A [Get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)parancs futtatásával lekérheti a kiszolgálón található helyek listáját.
    - a ". *" mindennek megfelel
    - A "SiteName" csak az IIS-helynek felel meg, amely a pontos nevet adja meg.
- A **InstrumentationKey** az előző két szűrőnek megfelelő alkalmazások figyelésének engedélyezéséhez szükséges.
    - Ha a figyelés kizárásához szabályokat szeretne meghatározni, hagyja üresen ezt az értéket.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Nem kötelező.** Ezzel a kapcsolóval engedélyezheti a rendszerállapot-kezelő motornak, hogy eseményeket és üzeneteket gyűjtsön arról, hogy mi történik a felügyelt folyamat végrehajtása során. Ezek az események és üzenetek függőségi eredmény kódokat, HTTP-műveleteket és SQL-parancsok szövegét tartalmazzák.

A kialakítási motor terhelést hoz létre, és alapértelmezés szerint ki van kapcsolva.

### <a name="-acceptlicense"></a>-AcceptLicense
**Nem kötelező.** Ezzel a kapcsolóval fogadhatja el a fej nélküli telepítések licencét és adatvédelmi nyilatkozatát.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Ha webkiszolgálókat tartalmazó fürttel rendelkezik, lehet, hogy [megosztott konfigurációt](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)használ.
A HttpModule nem lehet beinjektálni ebbe a megosztott konfigurációba.
Ez a parancsfájl nem fog működni, ha az üzenetben további telepítési lépések szükségesek.
Ezzel a kapcsolóval figyelmen kívül hagyhatja ezt az ellenőrzés, és folytathatja az előfeltételek telepítését. További információ: [ismert ütközés-az-IIS-Shared-Configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Részletes
**Általános paraméter.** Ezzel a kapcsolóval részletes naplókat jeleníthet meg.

### <a name="-whatif"></a>– WhatIf 
**Általános paraméter.** Ezzel a kapcsolóval tesztelheti és érvényesítheti a bemeneti paramétereket a figyelés tényleges engedélyezése nélkül.

## <a name="output"></a>Output


#### <a name="example-output-from-a-successful-enablement"></a>Példa a sikeres engedélyezésből származó kimenetre

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="next-steps"></a>További lépések

  A telemetriai adatok megtekintése:
 - [Ismerje](../../azure-monitor/app/metrics-explorer.md) meg a mérőszámokat a teljesítmény és a használat figyeléséhez.
- [Események és naplók keresése](../../azure-monitor/app/diagnostic-search.md) a problémák diagnosztizálásához.
- További speciális lekérdezésekhez [használja](../../azure-monitor/app/analytics.md) az elemzést.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) annak biztosításához, hogy a hely élőben maradjon.
- [Vegyen fel webes ügyfél-telemetria](../../azure-monitor/app/javascript.md) a kivételek megjelenítéséhez a weboldali kódból és a nyomkövetési hívások engedélyezéséhez.
- [Adja hozzá a Application INSIGHTS SDK-t a kódhoz](../../azure-monitor/app/asp-net.md) , hogy nyomkövetési és naplózási hívásokat helyezzen el.
 
 További Állapotmonitor v2:
 - A Állapotmonitor v2 [hibáinak megoldásához](status-monitor-v2-troubleshoot.md) használja az útmutatót.
 - [A konfiguráció](status-monitor-v2-api-get-config.md) beszerzésével ellenőrizze, hogy a beállítások megfelelően vannak-e rögzítve.
 - [A](status-monitor-v2-api-get-status.md) figyelés ellenőrzésének állapotának beolvasása.

---
title: 'Az Azure Állapotfigyelőt v2 API-leírások: Engedélyezze a monitorozást |} A Microsoft Docs'
description: Állapot figyelő v2 API-hivatkozás. Enable-ApplicationInsightsMonitoring. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. ASP.NET-webalkalmazásokat együttműködik a helyileg üzemeltetett, a virtuális gépek vagy az Azure-ban.
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
ms.openlocfilehash: e87bfad11eee5b86d35e6b4f2846b094c467e0ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66734173"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>A figyelő v2 API állapota: Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

Ez a cikk ismerteti, amely tagja a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosított, és ezt nem javasoljuk a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, és néhány előfordulhat, hogy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Leírás

Lehetővé teszi, hogy adatfolyamat létrehozása kód nélküli csatolása egy célszámítógépen IIS-alkalmazások figyelésére.

Ezzel a parancsmaggal módosíthatja az IIS applicationHost.config, és a bizonyos beállításkulcsok beállításával.
Azt is létrehoz egy applicationinsights.ikey.config fájlt, amely meghatározza a minden alkalmazás által használt kialakítási kulcsot.
Az IIS betölti a RedfieldModule lesz az Application Insights SDK helyezhet el alkalmazásokat, az alkalmazások indításkor.
Indítsa újra az IIS a módosítások érvénybe léptetéséhez.

Miután engedélyezte a figyelés, javasoljuk, hogy használjon [élő mérőszámok](live-stream.md) gyorsan ellenőrizheti, ha az alkalmazás USA telemetriai adatokat küldenek.


> [!NOTE] 
> - Első lépésként szüksége van egy kialakítási kulcsot. További információkért lásd: [erőforrás létrehozása](create-new-resource.md#copy-the-instrumentation-key).
> - Ez a parancsmag megköveteli, hogy tekintse át és fogadja el a licenc és az adatvédelmi nyilatkozatot.

> [!IMPORTANT] 
> Ez a parancsmag megköveteli egy PowerShell-munkamenetet rendszergazdai jogosultságokkal rendelkezik, és a egy rendszergazda jogú végrehajtási házirend. További információkért lásd: [PowerShell futtatása rendszergazdaként az emelt szintű végrehajtási házirendjével](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy).

## <a name="examples"></a>Példák

### <a name="example-with-a-single-instrumentation-key"></a>A példában egyetlen kialakítási kulccsal
Ebben a példában az adott számítógépen minden alkalmazás egyetlen eszközkulcs vannak hozzárendelve.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Példa-kialakítási kulcs térképek
Ebben a példában:
- `MachineFilter` az aktuális számítógépet megfelelteti a `'.*'` helyettesítő karakter.
- `AppFilter='WebAppExclude'` biztosít egy `null` kialakítási kulcsot. A megadott alkalmazás nem lesznek tagolva.
- `AppFilter='WebAppOne'` a megadott alkalmazás rendeli hozzá egy egyedi kialakítási kulcsot.
- `AppFilter='WebAppTwo'` a megadott alkalmazás rendeli hozzá egy egyedi kialakítási kulcsot.
- Végül `AppFilter` is használ a `'.*'` helyettesítő karakter megadásával minden olyan webes alkalmazás, amely nem egyezik a korábbi szabályok, és rendelje hozzá egy alapértelmezett kialakítási kulcs egyezik.
- Tárolóhelyek kerülnek, az olvashatóság érdekében.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Paraméterek

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Szükséges.** Ez a paraméter használatával egyetlen kialakítási kulcs megadni a cél számítógépen az összes alkalmazás számára.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Szükséges.** Ez a paraméter használatával adja meg több kialakítási kulcs és a egy hozzárendelését az egyes alkalmazások által használt kialakítási kulcs.
Létrehozhat több számítógép egy telepítési szkript beállításával `MachineFilter`.

> [!IMPORTANT]
> Alkalmazások egyezni fog a sorrendben, a szabályok biztosított szabályok alapján. Úgy kell megadnia a legtöbb speciális szabályok először, és a legtöbb általános szabályokat tartanak.

#### <a name="schema"></a>Séma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** szükséges C# regex a számítógép vagy virtuális gép neve.
    - ". *" megfelelő összes
    - A pontos megadott nevét a "Számítógépnév" csak a számítógépek meg fog egyezni.
- **AppFilter** szükséges C# regex a számítógép vagy virtuális gép neve.
    - ". *" megfelelő összes
    - "ApplicationName" egyezni fog csak az IIS-alkalmazások a megadott pontos nevét.
- **InstrumentationKey** van szükség ahhoz, hogy az előző két szűrőknek megfelelő alkalmazások figyelését.
    - Ez az érték null, ha szeretne kizárni a figyelési szabályokat definiálhat, hagyja.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Nem kötelező.** Ez a kapcsoló használatával engedélyezhető a rendszerállapot-motor gyűjtendő események és a egy felügyelt folyamatot végrehajtása közben rögzítsék üzeneteket. Ezen események és az üzenetek között függőségi eredménykódok, HTTP-műveletek és SQL-parancsszöveg.

A rendszerállapot-motor többletterheléssel, és alapértelmezés szerint ki van kapcsolva.

### <a name="-acceptlicense"></a>-AcceptLicense
**Nem kötelező.** Használja ezt a kapcsolót a licenc és az adatvédelmi nyilatkozatot távfelügyelt telepítés fogadására.

### <a name="-verbose"></a>-Verbose
**Általános paraméter.** Ez a kapcsoló használatával jeleníti meg a részletes naplókat.

### <a name="-whatif"></a>-WhatIf 
**Általános paraméter.** Használja ezt a kapcsolót, tesztelése és ellenőrzése a bemeneti paramétereket ténylegesen a figyelés engedélyezése nélkül.

## <a name="output"></a>Kimenet


#### <a name="example-output-from-a-successful-enablement"></a>Példa a kimenetre való sikeres engedélyezése

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
 - [Metrikák böngészése](../../azure-monitor/app/metrics-explorer.md) teljesítményének figyelése és használati.
- [Eseményeket és naplókat kereshet](../../azure-monitor/app/diagnostic-search.md) problémák diagnosztizálásához.
- [Elemzések](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) , hogy a hely elérhető maradjon.
- [Ügyfél-telemetriát adhat hozzá](../../azure-monitor/app/javascript.md) lássa a weblapkód kivételeit és nyomkövetési hívásokat engedélyezése.
- [Az Application Insights SDK hozzáadása a kódhoz](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhíváskat szúrhasson.
 
 Hozzon ki még többet Állapotfigyelőt v2:
 - Használja az útmutatóban [hibaelhárítása](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
 - [A konfiguráció első](status-monitor-v2-api-get-config.md) annak ellenőrzéséhez, hogy a beállításainak megfelelően rögzítve.
 - [A kapcsolat állapotának lekérése](status-monitor-v2-api-get-status.md) vizsgálhatja meg a figyelés.

---
title: 'Az Azure állapota figyelő v2 API-leírások: Engedélyezze a monitorozást |} A Microsoft Docs'
description: Állapot figyelő v2 API referencia engedélyezése – ApplicationInsightsMonitoring. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
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
ms.openlocfilehash: e2d964316e83138711547fb18fc5d04c56b4002f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870522"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>A figyelő v2 API állapota: Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

Ez a dokumentum ismerteti, amely tartalmazza a szükséges, amelynek a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Leírás

Engedélyezési kód nélküli csatolása a célgépen IIS alkalmazások figyelését.
Ezzel a parancsmaggal módosíthatja az IIS applicationHost.config, és a bizonyos beállításkulcsok beállításával.
Ez a parancsmag is létrehoz egy applicationinsights.ikey.config, amely meghatározza, melyik alkalmazás melyik kialakítási kulcsot használ.
Az IIS betölti a RedfieldModule indításkor, amely az Application Insights SDK fogja tölteni alkalmazásokba, mivel ezek az alkalmazások indítása.
Indítsa újra az IIS a módosítások érvénybe léptetéséhez.

Miután engedélyezte a figyelés, javasoljuk a [élő mérőszámok](live-stream.md) gyorsan megfigyelni, ha az alkalmazás USA telemetriai adatokat küldenek.


> [!NOTE] 
> Első lépésként egy kialakítási kulcsot kell rendelkeznie. További információkért olvassa el [hozzon létre új erőforrást](create-new-resource.md#copy-the-instrumentation-key).


> [!IMPORTANT] 
> Ez a parancsmag egy PowerShell-munkamenetet rendszergazdai jogosultságokkal, és a egy rendszergazda jogú végrehajtási házirend van szükség. Olvasási [Itt](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy) további információt.

> [!NOTE] 
> Ez a parancsmag kell, hogy tekintse át és fogadja el a licenc és az adatvédelmi nyilatkozatot.


## <a name="examples"></a>Példák

### <a name="example-with-single-instrumentation-key"></a>Példa az egyetlen kialakítási kulcs
Ebben a példában az aktuális számítógép összes alkalmazás hozzá lesz rendelve egy egyetlen kialakítási kulcsot.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Példa a kialakítási kulcs térkép
Ebben a példában 
- `MachineFilter` az aktuális gépen használatával egyezni fog a `'.*'` helyettesítő karakter.
- `AppFilter='WebAppExclude'` biztosít egy `null` InstrumentationKey. Ez az alkalmazás nem lesznek tagolva.
- `AppFilter='WebAppOne'` Ez az alkalmazás fogja hozzárendelni egy egyedi kialakítási kulcsot.
- `AppFilter='WebAppTwo'` Emellett rendeli az adott alkalmazást egy egyedi kialakítási kulcsot.
- Végül `AppFilter` is használ a `'.*'` minden más webes alkalmazás megfelelő helyettesítő nem egyezik a korábbi szabályok, és hozzárendeli egy alapértelmezett kialakítási kulcsot.
- A tárolóhelyek az olvashatóság érdekében csak hozzá.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Paraméterek 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Szükséges.** Ez a paraméter használatával adjon meg egy egyetlen Rendszerállapotkulcsot használatra összes alkalmazás a célszámítógépen.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Szükséges.** Ez a paraméter használatával adja meg több erőforráskulcsot és mely alkalmazásokat mely rendszerállapotkulcsot használandó térképét. Létrehozhat több gépek egyetlen telepítési parancsfájl a MachineFilter beállításával. 

> [!IMPORTANT] 
> Alkalmazások egyezni fog a sorrendben, amely már a megadott szabályok alapján. Ilyen kell megadnia a legtöbb speciális szabályok először, és a legtöbb általános szabályokat tartanak.

#### <a name="schema"></a>Séma
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** van szükség a c# reguláris kifejezést a számítógép vagy virtuális gép neve.
    - ". *" megfelelő összes
    - "Számítógépnév" csak azokat a számítógépeket, a névnek pontosan megegyeznek.
- **AppFilter** van szükség a c# reguláris kifejezést a számítógép vagy virtuális gép neve.
    - ". *" megfelelő összes
    - "ApplicationName" csak IIS-alkalmazások a névnek pontosan megegyeznek.
- **InstrumentationKey** van szükség ahhoz, hogy az a fenti két szűrőknek megfelelő alkalmazások figyelését.
    - Ez az érték null értékű, ha szeretne kizárni a figyelési szabályokat definiálhat, hagyja üresen


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Nem kötelező.** Ez a kapcsoló használatával engedélyezhető a rendszerállapot-motor gyűjtendő események és az üzenetek, hogy mi történik a felügyelt folyamat végrehajtása közben. Beleértve többek között a függőségi eredménykódok, HTTP-műveletek és SQL-parancsszöveg. A rendszerállapot-motor hozzáadja a további erőforrásokra, és alapértelmezés szerint ki van kapcsolva.

### <a name="-acceptlicense"></a>-AcceptLicense
**Nem kötelező.** Használja ezt a kapcsolót a licenc és az adatvédelmi nyilatkozatot távfelügyelt telepítés fogadására.

### <a name="-verbose"></a>-Verbose
**Általános paraméter.** Használja ezt a kapcsolót a kimenetben a részletes naplókat.

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


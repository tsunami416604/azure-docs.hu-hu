---
title: 'Az Azure állapota figyelő v2 API-leírások: Állítsa be a konfigurációs |} A Microsoft Docs'
description: Állapot figyelő v2 API referencia Set-ApplicationInsightsMonitoringConfig. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. A helyszíni, valamint a virtuális gépeken, illetve az Azure-ban üzemeltetett ASP.NET-webappokhoz is használható.
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
ms.openlocfilehash: 953edcb98de6ea705721aef0922562d23e18f0f5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148240"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>A figyelő v2 API állapota: Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Ez a dokumentum ismerteti, amely tartalmazza a szükséges, amelynek a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információkért lásd: [kiegészítő használati feltételek a Microsoft Azure Előzetesekre vonatkozó](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Leírás

Állítsa be a konfigurációs fájl ismétlődő teljes újratelepítése nélkül. Indítsa újra az IIS a módosítások érvénybe léptetéséhez.

> [!IMPORTANT] 
> Ez a parancsmag egy PowerShell-munkamenetet rendszergazdai jogosultság szükséges.


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


### <a name="-verbose"></a>-Verbose
**Általános paraméter.** Használja ezt a kapcsolót a kimenetben a részletes naplókat.


## <a name="output"></a>Kimenet

Alapértelmezés szerint nincs kimenete.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Részletes példa beállítás a konfigurációs fájl segítségével - InstrumentationKey kimenete

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Részletes példa beállítás a konfigurációs fájl segítségével – InstrumentationKeyMap kimenete

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>További lépések

  A telemetriai adatok megtekintése:
 - [A metrikák áttekintése](../../azure-monitor/app/metrics-explorer.md) a teljesítmény és a használat figyeléséhez
- [Eseményeket és naplókat kereshet](../../azure-monitor/app/diagnostic-search.md) problémák diagnosztizálásához
- [Elemzések](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez
- [Irányítópultok létrehozása](../../azure-monitor/app/app-insights-dashboards.md)
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) , hogy a hely elérhető maradjon.
- [Ügyfél-telemetriát adhat hozzá](../../azure-monitor/app/javascript.md) lássa a weblapkód kivételeit és nyomkövetési hívásokat szúrhasson be.
- [Application Insights SDK hozzáadása a kódhoz](../../azure-monitor/app/asp-net.md) , hogy Ön nyomkövetési és naplóhíváskat szúrhasson
 
 Hozzon ki még többet Állapotfigyelőt v2:
 - Használja az útmutatóban [hibaelhárítás](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
 - [A konfiguráció első](status-monitor-v2-api-get-config.md) annak ellenőrzéséhez, hogy a beállításainak megfelelően rögzítve.
 - [A kapcsolat állapotának lekérése](status-monitor-v2-api-get-status.md) vizsgálhatja meg a figyelés.

---
title: 'Az Azure Állapotfigyelőt v2 API-leírások: Állítsa be a konfigurációs |} A Microsoft Docs'
description: Állapot figyelő v2 API-hivatkozás. Set-ApplicationInsightsMonitoringConfig. A webhely újbóli üzembe helyezése nélkül webhely teljesítményének megfigyeléséhez. ASP.NET-webalkalmazásokat együttműködik a helyileg üzemeltetett, a virtuális gépek vagy az Azure-ban.
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
ms.openlocfilehash: e63d935b3c11766c4981ffb035dd45ad4019797c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807070"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v040-alpha"></a>A figyelő v2 API állapota: Set-ApplicationInsightsMonitoringConfig (v0.4.0-alpha)

Ez a dokumentum ismerteti, amely tagja a parancsmag a [Az.ApplicationMonitor PowerShell-modul](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Állapot figyelő v2 jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió egy szolgáltatásiszint-megállapodás nélkül biztosított, és ezt nem javasoljuk a termelési számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, és néhány előfordulhat, hogy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Leírás

Beállítja a konfigurációs fájlt a teljes újratelepítése nélkül.
Indítsa újra az IIS a módosítások érvénybe léptetéséhez.

> [!IMPORTANT] 
> Ez a parancsmag megköveteli egy PowerShell-munkamenetet rendszergazdai jogosultságokkal rendelkezik.


## <a name="examples"></a>Példák

### <a name="example-with-a-single-instrumentation-key"></a>A példában egyetlen kialakítási kulccsal
Ebben a példában az aktuális számítógép összes alkalmazás hozzá lesz rendelve egy egyetlen kialakítási kulcsot.

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
    - "Számítógépnév" egyezni fog csak azokat a számítógépeket, a megadott néven.
- **AppFilter** szükséges C# regex a számítógép vagy virtuális gép neve.
    - ". *" megfelelő összes
    - "ApplicationName" egyezni fog a megadott nevű csak az IIS alkalmazásokat.
- **InstrumentationKey** van szükség ahhoz, hogy az alkalmazásokat, amelyek az előző két szűrőknek megfelelő figyelését.
    - Ez az érték null, ha szeretne kizárni a figyelési szabályokat definiálhat, hagyja.


### <a name="-verbose"></a>-Verbose
**Általános paraméter.** Ez a kapcsoló használatával jeleníti meg a részletes naplókat.


## <a name="output"></a>Output

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
 - [Metrikák böngészése](../../azure-monitor/app/metrics-explorer.md) teljesítményének figyelése és használati.
- [Eseményeket és naplókat kereshet](../../azure-monitor/app/diagnostic-search.md) problémák diagnosztizálásához.
- [Elemzések](../../azure-monitor/app/analytics.md) az összetettebb lekérdezésekhez.
- [Irányítópultok létrehozása](../../azure-monitor/app/overview-dashboard.md).
 
 További telemetriai funkciók hozzáadása:
 - [Létrehozhat webes teszteket](monitor-web-app-availability.md) , hogy a hely elérhető maradjon.
- [Ügyfél-telemetriát adhat hozzá](../../azure-monitor/app/javascript.md) lássa a weblapkód kivételeit és nyomkövetési hívásokat engedélyezése.
- [Az Application Insights SDK hozzáadása a kódhoz](../../azure-monitor/app/asp-net.md) így nyomkövetési és naplóhíváskat szúrhasson
 
 Hozzon ki még többet Állapotfigyelőt v2:
 - Használja az útmutatóban [hibaelhárítása](status-monitor-v2-troubleshoot.md) Állapotfigyelőt v2.
 - [A konfiguráció első](status-monitor-v2-api-get-config.md) annak ellenőrzéséhez, hogy a beállításainak megfelelően rögzítve.
 - [A kapcsolat állapotának lekérése](status-monitor-v2-api-get-status.md) vizsgálhatja meg a figyelés.

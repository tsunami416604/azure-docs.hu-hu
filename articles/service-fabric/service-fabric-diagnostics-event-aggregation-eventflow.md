---
title: Az Azure Service Fabric eseményösszesítése az EventFlow-val
description: Ismerje meg az események összesítését és gyűjtését az EventFlow használatával az Azure Service Fabric-fürtök figyeléséhez és diagnosztikájához.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: cde24657cc8ed78b91e72df16d51df4077a6e030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463092"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Eseményösszesítés és -gyűjtés az EventFlow használatával

[A Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) az eseményeket egy csomópontról egy vagy több figyelési helyre irányíthatja. Mivel a szolgáltatásprojektben NuGet-csomagként szerepel, az EventFlow-kód és a szolgáltatással való konfigurációs utazás, kiküszöbölve az Azure Diagnostics korábban említett csomópontonkénti konfigurációs problémát. Az EventFlow a szolgáltatási folyamaton belül fut, és közvetlenül csatlakozik a konfigurált kimenetekhez. A közvetlen kapcsolat miatt az EventFlow az Azure, a tároló és a helyszíni szolgáltatás központi telepítéseihez működik. Legyen óvatos, ha az EventFlow-t nagy sűrűségű forgatókönyvekben, például egy tárolóban futtatja, mert minden EventFlow-folyamat külső kapcsolatot hoz létre. Tehát, ha a fogadó több folyamat, kapsz több kimenő kapcsolatok! Ez nem annyira a Service Fabric-alkalmazások, mert egy `ServiceType` futtatás i. ugyanazon folyamat összes replikák, és ez korlátozza a kimenő kapcsolatok száma. Az EventFlow eseményszűrést is kínál, így csak a megadott szűrőnek megfelelő események kerülnek elküldésre.

## <a name="set-up-eventflow"></a>EventFlow beállítása

Az EventFlow bináris fájljai NuGet-csomagok készleteként érhetők el. Ha az EventFlow-t hozzá szeretné adni egy Service Fabric szolgáltatásprojekthez, kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza a "NuGet-csomagok kezelése" parancsot. Váltson a "Tallózás"`Diagnostics.EventFlow`fülre, és keressen a " ":

![EventFlow NuGet csomagok a Visual Studio NuGet csomagkezelő felhasználói felületén](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Megjelenik a különböző csomagok listája, "Bemenetek" és "Kimenetek" felirattal. Az EventFlow különböző naplózási szolgáltatókat és elemzőket támogat. Az EventFlow szolgáltatást üzemeltető szolgáltatásnak az alkalmazásnaplók forrásától és céljától függően megfelelő csomagokat kell tartalmaznia. A mellett, hogy az alapvető ServiceFabric csomag, is szükség van legalább egy bemeneti és kimeneti konfigurálva. Például hozzáadhatja a következő csomagokat, hogy EventSource-eseményeket küldjön az Application Insightsnak:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource`a szolgáltatás EventSource osztályából és a szabványos EventSources forrásokból, például a *Microsoft-ServiceFabric-Services-ből* és a *Microsoft-ServiceFabric-Actors-ből*származó adatok rögzítéséhez)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights`(a naplókat egy Azure Application Insights-erőforrásba küldjük)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(lehetővé teszi az EventFlow-folyamat inicializálását a Service Fabric szolgáltatáskonfigurációjából, és jelenti a diagnosztikai adatok Service Fabric állapotjelentésekként való küldésével kapcsolatos problémákat)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource`csomag hoz a szolgáltatási projektet a .NET Framework 4.6 vagy újabb célértékre. A csomag telepítése előtt győződjön meg arról, hogy beállította a megfelelő célkeretrendszert a projekttulajdonságokban.

Az összes csomag telepítése után a következő lépés az EventFlow konfigurálása és engedélyezése a szolgáltatásban.

## <a name="configure-and-enable-log-collection"></a>Naplógyűjtés konfigurálása és engedélyezése
A naplók küldéséért felelős EventFlow-folyamat egy konfigurációs fájlban tárolt specifikációból jön létre. A `Microsoft.Diagnostics.EventFlow.ServiceFabric` csomag egy kezdő EventFlow `PackageRoot\Config` konfigurációs fájlt `eventFlowConfig.json`telepít a megoldásmappában, melynek neve . Ezt a konfigurációs fájlt módosítani kell az `EventSource` alapértelmezett szolgáltatásosztály ból származó adatok és a konfigurálni kívánt egyéb bemenetek rögzítéséhez, valamint az adatok nak a megfelelő helyre történő elküldéséhez.

>[!NOTE]
>Ha a projektfájl VisualStudio 2017 formátumú, a `eventFlowConfig.json` fájl nem lesz automatikusan hozzáadva. A probléma megoldásához hozza `Config` létre a fájlt `Copy if newer`a mappában, és állítsa a build műveletet a számára. 

Itt van egy minta *eventFlowConfig.json* alapján a NuGet csomagok fent említett:
```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

A szolgáltatás ServiceEventSource neve a ServiceEventSource osztályra `EventSourceAttribute` alkalmazott Name tulajdonság értéke. Ez mind meg van `ServiceEventSource.cs` adva a fájlban, amely a szolgáltatáskód része. A következő kódrészletben például a ServiceEventSource neve *MyCompany-Application1-Stateless1:*

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Vegye `eventFlowConfig.json` figyelembe, hogy a fájl a szolgáltatáskonfigurációs csomag része. A fájl módosításai szerepelhetnek a szolgáltatás teljes vagy csak konfigurációs frissítéseiközött, a Service Fabric frissítési állapotának ellenőrzése és az automatikus visszaállítás függvényében, ha frissítési hiba van. További információ: [Service Fabric alkalmazás frissítés.](service-fabric-application-upgrade.md)

A *szűrők* szakasza a config lehetővé teszi, hogy tovább testre az információt, amely megy keresztül az EventFlow folyamat a kimenetek, amely lehetővé teszi, hogy csepp vagy mellékelése bizonyos információkat, vagy módosítsa az esemény adatok szerkezetét. A szűrésről további információt az [EventFlow-szűrők című témakörben talál.](https://github.com/Azure/diagnostics-eventflow#filters)

Az utolsó lépés az EventFlow-folyamat példányosítása a szolgáltatás `Program.cs` indítási kódjában, amely a fájlban található:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

A metódus paramétereként `CreatePipeline` `ServiceFabricDiagnosticsPipelineFactory` átadott név az EventFlow naplógyűjtemény-folyamatot képviselő *állapotentitás* neve. Ez a név akkor használatos, ha az EventFlow észlelési és hiba, és jelenti, hogy a Service Fabric állapoti alrendszeren keresztül.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>A Service Fabric-beállítások és alkalmazásparaméterek használata az eventFlowConfig alkalmazásban

Az EventFlow támogatja a Service Fabric-beállítások és alkalmazásparaméterek használatát az EventFlow-beállítások konfigurálásához. A Service Fabric beállítási paramétereire hivatkozhat ezzel a speciális szintaxissal az értékekhez:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>`A Service Fabric konfigurációs szakaszának `<setting-name>` neve, és az EventFlow-beállítás konfigurálásához használt értéket biztosító konfigurációs beállítás. Ennek módjáról a [Service Fabric-beállítások és alkalmazásparaméterek támogatása](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters)lehetőséget.

## <a name="verification"></a>Ellenőrzés

Indítsa el a szolgáltatást, és figyelje meg a Debug kimeneti ablakot a Visual Studióban. A szolgáltatás indítása után meg kell kezdenie a bizonyíték, hogy a szolgáltatás rekordokat küld a beállított kimenetre. Nyissa meg az eseményelemzési és vizualizációs platformot, és ellenőrizze, hogy a naplók megjelentése (eltarthat néhány percig).

## <a name="next-steps"></a>További lépések

* [Eseményelemzés és vizualizáció az Application Insights segítségével](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Eseményelemzés és vizualizáció az Azure Monitor-naplókkal](service-fabric-diagnostics-event-analysis-oms.md)
* [EventFlow dokumentáció](https://github.com/Azure/diagnostics-eventflow)

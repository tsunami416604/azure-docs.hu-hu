---
title: Az Azure Service Fabric esemény összesítési rendelkező EventFlow |} Microsoft Docs
description: További tudnivalók összesítésére és események gyűjtése EventFlow figyelési és diagnosztika Azure Service Fabric-fürt segítségével.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 9b851b2d75cf78a02dd223788085ac9a0963376e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Esemény összevonásának és a gyűjtemény EventFlow használatával

[Microsoft Diagnostics EventFlow](https://github.com/Azure/diagnostics-eventflow) irányíthatja a események csomópont egy vagy több figyelési célhelyre. Mivel a NuGet-csomagot a projekt része, EventFlow kód és a konfigurációs változatlan marad a szolgáltatás, így kiküszöböli a fentebb említett Azure Diagnostics kapcsolatos csomópontonként konfigurációs probléma. EventFlow belül a szolgáltatási folyamat fut, és a beállított kimenetek közvetlenül csatlakozik. A közvetlen kapcsolat miatt EventFlow használható Azure, a tároló és a helyszíni-szolgáltatási telepítéseket. Ügyeljen arra, ha futtatja EventFlow nagy sűrűségű helyzetekben, például a tárolóban lévő minden EventFlow folyamat ugyanis egy külső kapcsolatot. Így, ha több folyamatok működteti, több kimenő kapcsolatok! Ez nem mértékű problémát jelent a Service Fabric-alkalmazások esetében, mert az összes replika egy `ServiceType` futtatja ugyanabban a folyamatban, és a kimenő kapcsolatok számának korlátozása. EventFlow is kínál események szűrése, így csak a megadott szűrőnek megfelelő események kerülnek.

## <a name="set-up-eventflow"></a>EventFlow beállítása

EventFlow bináris NuGet-csomagok készletként érhetők el. Service Fabric-szolgáltatás projektbe EventFlow hozzáadásához kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza a "Manage NuGet packages". Váltson át a "Tallózás" fülre, és keresse meg "`Diagnostics.EventFlow`":

![A Visual Studio NuGet-Csomagkezelő felhasználói felület EventFlow NuGet-csomagok](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Látni fogja a különböző csomagok listája jelenik meg, az "A bemeneti" és "Kimeneti" címkével ellátott. EventFlow különböző különböző naplózási szolgáltatók és elemzőkkel támogatja. A szolgáltatásüzemeltetési EventFlow attól függően, hogy a forrás- és az alkalmazásnaplókat a megfelelő csomagokat kell tartalmaznia. A core ServiceFabric csomag mellett is szükség van legalább egy bemeneti és kimeneti konfigurálva. Például a következő csomagok EventSource események küldése az Application Insights is hozzáadhat:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` a szolgáltatás az EventSource osztályból származik, és a szabványos EventSources adatok rögzítéséhez *Microsoft-ServiceFabric-szolgáltatások* és *Microsoft-ServiceFabric-szereplője*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (fogjuk küldeni a naplókat az Azure Application Insights-erőforrás)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(lehetővé teszi, hogy a Service Fabric szolgáltatáskonfiguráció EventFlow folyamat inicializálása és jelentéseket, a Service Fabric állapotjelentések diagnosztikai adatok küldésének problémákat)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` csomag igényli-e a projekt, amelyekre a .NET-keretrendszer 4.6-os vagy újabb. Ellenőrizze, hogy beállította a megfelelő célkeretrendszer a projekt tulajdonságait a csomag telepítése előtt.

A csomagok telepítése után a következő lépés, hogy konfigurálja és EventFlow engedélyezése a szolgáltatásban.

## <a name="configure-and-enable-log-collection"></a>Konfigurálja és engedélyezi a naplógyűjtést
A EventFlow-feldolgozási folyamat felelős a naplókat küld a konfigurációs fájlban tárolt specifikáció készült. A `Microsoft.Diagnostics.EventFlow.ServiceFabric` a csomag telepíti a kiindulási EventFlow konfigurációs fájl `PackageRoot\Config` nevű megoldás mappa `eventFlowConfig.json`. A konfigurációs fájlnak kell módosítani kell az alapértelmezett szolgáltatás adatait `EventSource` osztály, és bármely más bemeneti adatokat szeretné konfigurálni, és küldhetnek adatokat a megfelelő helyre.

>[!NOTE]
>Ha a projektfájlban VisualStudio 2017 formátuma a `eventFlowConfig.json` fájl nem lesz automatikusan hozzáadva. Javítsa ki a hozzon létre a fájlt a a `Config` mappa, és a létrehozási művelet `Copy if newer`. 

Íme egy minta *eventFlowConfig.json* a fent említett NuGet-csomagok alapján:
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

Szolgáltatás ServiceEventSource neve nem a név tulajdonságának értéke a `EventSourceAttribute` ServiceEventSource osztály. Az összes megadott a `ServiceEventSource.cs` fájl, amely a szolgáltatáskód hibáit része. Például a következő kódrészletet a neve, a ServiceEventSource: *értéket-Alkalmaz1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Vegye figyelembe, hogy `eventFlowConfig.json` fájl szolgáltatás konfigurációs csomag része. A fájl módosításait a szolgáltatás a Service Fabric frissítési állapot-ellenőrzési eredményeire és automatikus visszaállítási, ha a frissítés nem sikerülne teljes vagy konfiguráció-csak frissítés tartalmazhat. További információkért lásd: [Service Fabric az alkalmazásfrissítés](service-fabric-application-upgrade.md).

A *szűrők* konfigurációs szakasza lehetővé teszi további, amelyet szeretne lépjen a EventFlow-feldolgozási folyamaton keresztül a kimenetek, lehetővé téve dobja el, és bizonyos információval adatok személyre szabására, vagy módosítsa a szerkezete a eseményadatok. A szűrés további információkért lásd: [EventFlow szűrők](https://github.com/Azure/diagnostics-eventflow#filters).

Az utolsó lépés, hogy a szolgáltatás indítási kódban található EventFlow csővezeték példányosítható `Program.cs` fájlt:

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

Nevét paraméterként, a `CreatePipeline` metódusában a `ServiceFabricDiagnosticsPipelineFactory` neve a *állapotfigyelő entitás* képviselő a EventFlow napló adatgyűjtési folyamatot. Ezt a nevet használja, ha a EventFlow észlel, és hiba és jelenti a a Service Fabric állapotfigyelő alrendszer keresztül.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>Service Fabric-beállítások és az alkalmazás paraméterek használata eventFlowConfig

EventFlow támogatja a Service Fabric-beállítások és alkalmazások paremeters EventFlow beállítások konfigurálása. Olvassa el a Service Fabric beállítások paraméterek értékek ezen különleges szintaxis használatával:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` a Service Fabric konfigurációs szakasz neve és `<setting-name>` adva egy EventFlow beállítás konfigurálásához használt konfigurációs beállítás. Olvassa el a további információt ebben az esetben lépjen [Service Fabric-beállítás- és alkalmazás támogatása](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Ellenőrzés

Indítsa el a szolgáltatást, és tekintse meg a hibakeresési Visual Studio kimeneti ablakában. A szolgáltatás az elindítása után jelenítse meg az megbizonyosodhat róla, hogy a szolgáltatás rekordot küld a kimeneti konfigurált. Nyissa meg az esemény elemzése és a képi megjelenítés platform, és győződjön meg arról, hogy a naplók megjelenítendő van elindítva (eltarthat néhány percig) fel.

## <a name="next-steps"></a>További lépések

* [Esemény elemzése és a képi megjelenítés az Application insights szolgáltatással](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Esemény elemzése és Naplóelemzési a képi megjelenítés](service-fabric-diagnostics-event-analysis-oms.md)
* [EventFlow dokumentáció](https://github.com/Azure/diagnostics-eventflow)

---
title: Az Azure Service Fabric esemény összesítése az eventflow segítségével |} A Microsoft Docs
description: További tudnivalók összesítése és események gyűjtése a monitorozást és diagnosztikát az Azure Service Fabric-fürtök az eventflow segítségével segítségével.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: bdc6c9476529b986f425d56544fd4b1afd8a864e
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668296"
---
# <a name="event-aggregation-and-collection-using-eventflow"></a>Események összesítése és gyűjtemény használatával eventflow segítségével

[A Microsoft Diagnostics eventflow segítségével](https://github.com/Azure/diagnostics-eventflow) irányíthatja az eseményeket egy csomópontról egy vagy több figyelési célhelyre. Mivel a szolgáltatás része a projektet a NuGet-csomagot, a eventflow segítségével kódot és a konfigurációt a szolgáltatással, így kiküszöböli a csomópontonkénti konfigurációs probléma korábban említettük, az Azure Diagnostics kapcsolatos utazási. Eventflow segítségével a szolgáltatás folyamaton belül fut, és közvetlenül csatlakozik a konfigurált kimenetek. A közvetlen kapcsolat miatt eventflow segítségével működik, Azure, a tároló és a helyszíni üzembe helyezéséhez. Legyen óvatos a futtatásakor eventflow segítségével a nagy sűrűségű forgatókönyvek, például egy tárolóban, mivel minden egyes eventflow segítségével folyamat egy külső kapcsolatot létesít. Így, ha a különböző folyamatok, több kimenő kapcsolatok! Ez nem a legtöbb jelent problémát egy Service Fabric-alkalmazásokat, mert az összes replika egy `ServiceType` futtassa ugyanabban a folyamatban, és ezzel a megoldással a kimenő kapcsolatok száma. Eventflow segítségével is kínál az események szűrésének, hogy csak azokat az eseményeket, amelyek megfelelnek a megadott szűrő érkeznek.

## <a name="set-up-eventflow"></a>Állítsa be az eventflow segítségével

NuGet-csomagok készleteként eventflow segítségével bináris érhetők el. Eventflow segítségével hozzáadása egy Service Fabric szolgáltatási projektet, kattintson a jobb gombbal a projektre a Megoldáskezelőben, és válassza a "Manage NuGet packages." Váltson át a "Tallózás" fülre, és keressen rá a "`Diagnostics.EventFlow`":

![A Visual Studio NuGet-Csomagkezelő felhasználói felület eventflow segítségével NuGet-csomagok](./media/service-fabric-diagnostics-event-aggregation-eventflow/eventflow-nuget.png)

Látni fogja a különböző csomagok listájában jelennek meg, a "Inputs" és "Kimenete" címkével ellátott. Eventflow segítségével számos különböző naplózási szolgáltatók és elemzők támogatja. A szolgáltatásüzemeltetési eventflow segítségével attól függően, a forrás- és az alkalmazásnaplókat a megfelelő csomagokat kell tartalmaznia. Mellett a fő ServiceFabric-csomagot is szükség van legalább egy bemeneti és kimeneti konfigurálva. Ha például a következő csomagok EventSource események küldése az Application Insights is hozzáadhat:

* `Microsoft.Diagnostics.EventFlow.Inputs.EventSource` Az EventSource osztály a szolgáltatás és a standard szintű EventSources adatok rögzítéséhez *Microsoft-ServiceFabric-szolgáltatások* és *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Outputs.ApplicationInsights` (fogjuk elküldeni a naplókat az Azure Application Insights-erőforrás a)
* `Microsoft.Diagnostics.EventFlow.ServiceFabric`(lehetővé teszi, hogy a Service Fabric-szolgáltatás konfigurációját az eventflow segítségével folyamat inicializálása és jelentések problémával, mint a Service Fabric-állapotjelentések diagnosztikai adatok küldése)

>[!NOTE]
>`Microsoft.Diagnostics.EventFlow.Inputs.EventSource` csomag igényli-e a projekt, amelyekre a .NET-keretrendszer 4.6-os vagy újabb. Mindenképpen állítsa be a megfelelő cílová architektura a projekt tulajdonságai a csomag telepítése előtt.

Összes csomag telepítése után a a következő lépés az, eventflow segítségével engedélyezheti a szolgáltatásban.

## <a name="configure-and-enable-log-collection"></a>Konfigurálja és engedélyezi a naplók összegyűjtése
Egy konfigurációs fájlban specifikáció felelős küldeni a naplókat az eventflow segítségével folyamat jön létre. A `Microsoft.Diagnostics.EventFlow.ServiceFabric` a csomag egy kezdési eventflow segítségével konfigurációs fájl alapján telepíti `PackageRoot\Config` nevű megoldás mappát `eventFlowConfig.json`. Ezt a konfigurációs fájlt az alapértelmezett szolgáltatás adatok rögzítése módosítani kell, `EventSource` osztály, és szeretné konfigurálni, és adatokat küldeni a megfelelő helyen bemenetet.

>[!NOTE]
>Ha a projektfájl VisualStudio 2017 formátuma a `eventFlowConfig.json` fájl nem automatikusan hozzáadja. Kijavítani ezt a fájlt létrehozni a a `Config` mappát, és állítsa a létrehozási művelet `Copy if newer`. 

Íme egy példa *eventFlowConfig.json* a fent említett NuGet-csomagok alapján:
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

A szolgáltatás ServiceEventSource név, a Name tulajdonság értékét a `EventSourceAttribute` ServiceEventSource osztály. Ez az összes megadott a `ServiceEventSource.cs` fájlt, amely része a webszolgáltatás kódjához. Ha például az alábbi kódrészlet a ServiceEventSource neve nem *értéket – Alkalmaz1-Stateless1*:

```csharp
[EventSource(Name = "MyCompany-Application1-Stateless1")]
internal sealed class ServiceEventSource : EventSource
{
    // (rest of ServiceEventSource implementation)
}
```

Vegye figyelembe, hogy `eventFlowConfig.json` fájl szolgáltatás konfigurációs csomag részét képezi. Ez a fájl módosítása is részét képezhetik teljes - vagy csak konfigurációs frissítés a szolgáltatást, a Service Fabric frissítési állapot-ellenőrzési és automatikus visszaállítási, ha a frissítés nem vonatkoznak. További információkért lásd: [Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md).

A *szűrők* szakaszában a konfiguráció lehetővé teszi további, amelyet szeretne az eventflow segítségével folyamat lépésben oldhatják fel a kimeneteket, dobja el, vagy bizonyos adatokat tartalmaznak, így információk testreszabásához, vagy módosítja az adatszerkezetet az a eseményadatok. A szűrés további információkért lásd: [eventflow segítségével szűrők](https://github.com/Azure/diagnostics-eventflow#filters).

Az utolsó lépés az, hogy hozza létre a a szolgáltatás indítási code-ban található eventflow segítségével folyamat `Program.cs` fájlt:

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

Nevét paraméterként, a `CreatePipeline` módszere a `ServiceFabricDiagnosticsPipelineFactory` neve a *egészségügyi entitás* jelölő az eventflow segítségével log adatgyűjtési folyamat. Ezt a nevet használja, ha a eventflow segítségével észlel, hiba és a jelentések, a Service Fabric health alrendszer keresztül.

### <a name="use-service-fabric-settings-and-application-parameters-in-eventflowconfig"></a>A Service Fabric-beállítások és a alkalmazásparamétereket eventFlowConfig használata

Eventflow segítségével támogatja az eventflow segítségével beállítások konfigurálása a Service Fabric-beállítások és alkalmazások paraméterek használatával. Olvassa el a Service Fabric beállítások paraméterek a speciális szintaxis használatával az értékeket:

```json
servicefabric:/<section-name>/<setting-name>
```

`<section-name>` a Service Fabric-konfigurációs szakasz neve és `<setting-name>` az konfigurációs beállítás, amely az értéket az eventflow segítségével beállítás konfigurálásához használt. Olvassa el a ehhez kapcsolatos információ: [támogatása a Service Fabric-beállítások és alkalmazások paraméterek](https://github.com/Azure/diagnostics-eventflow#support-for-service-fabric-settings-and-application-parameters).

## <a name="verification"></a>Ellenőrzés

Indítsa el a szolgáltatást, és tekintse meg a hibakeresés a Visual Studio kimeneti ablakában. Miután a szolgáltatás elindult, akkor kezdenek arra vonatkozóan, hogy a szolgáltatás a konfigurált kimeneti rekordot küld. Keresse meg az esemény elemzési és vizualizációs platform, és győződjön meg arról, hogy naplók megjelenítése kezdte meg az fel (eltarthat néhány percig).

## <a name="next-steps"></a>További lépések

* [Esemény elemzése és vizualizációs az Application insights segítségével](service-fabric-diagnostics-event-analysis-appinsights.md)
* [Esemény elemzése és képi megjelenítése a Azure Monitor naplóira](service-fabric-diagnostics-event-analysis-oms.md)
* [Dokumentáció eventflow segítségével](https://github.com/Azure/diagnostics-eventflow)

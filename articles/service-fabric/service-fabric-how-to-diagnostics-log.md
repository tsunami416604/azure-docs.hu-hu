---
title: Alkalmazásnapló-események létrehozása a .NET Service Fabric-alkalmazáshoz az Azure-ban vagy egy önálló fürt
description: Ismerje meg a naplózási hozzáadása egy Azure-fürtön vagy egy különálló – fürt által futtatott .NET Service Fabric-alkalmazásokat.
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
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: d1b3dc25dd9bda9d7f9d9152c2a94cea8321f5cf
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58660851"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Naplózás hozzáadása a Service Fabric-alkalmazás

Az alkalmazás adjon meg elég információt forensically hibakeresése, ha problémák merülnek fel. A naplózás nem adhat hozzá a Service Fabric-alkalmazás a legfontosabb témák egyikét. Ha hiba történik, jó naplózást is egy módot biztosít hibák vizsgálata. Napló mintázatok elemzésével javíthatja a teljesítményt vagy az alkalmazás tervezési módjai tapasztalhatja. Ez a dokumentum bemutatja néhány különböző naplózási lehetőségeket.

## <a name="eventflow"></a>Eventflow segítségével

A [szalagtár eventflow segítségével](https://github.com/Azure/diagnostics-eventflow) suite lehetővé teszi az alkalmazások meghatározása a diagnosztikai adatok gyűjtésére, és ahol, érdemes lehet használt kimeneti adattípus a. Diagnosztikai adatok az alkalmazások nyomkövetéseit kérelemteljesítmény-számlálókat bármi lehet. Azt fut ugyanabban a folyamatban, ahol az alkalmazás, így a kommunikációs terhelést másodpercekre csökken. További információ az eventflow segítségével és a Service Fabric: [Azure Service Fabric esemény összesítése az eventflow segítségével](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Strukturált EventSource események használata

Események használati eset meghatározó üzenet lehetővé teszi az esemény, az esemény környezetében csomag adatait. Könnyebben kereshet, és a szűrő nevét vagy a megadott esemény tulajdonságok értékei alapján. Strukturálja a rendszerállapot-kimenet teszi könnyebben olvasható, de szükséges további gondolkodási és időpontot, az egyes használati esetekhez esemény definiálása. 

Néhány esemény definíciókat meg lehet osztani a teljes alkalmazáson. Például egy metódus indítási vagy leállítási esemény lenne felhasználható sok szolgáltatás az alkalmazáson belül. Előfordulhat, hogy a tartomány-specifikus szolgáltatást, például egy rendelési rendszer egy **CreateOrder** esemény, amely rendelkezik a saját egyedi esemény. Ez a megközelítés előfordulhat, hogy sok események létrehozása, és előfordulhat, hogy a tranzakciókoordináció-azonosítók projektcsapatok között. 

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The instance constructor is private to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    // The ServiceTypeRegistered event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceTypeRegisteredEventId = 3;
    [Event(ServiceTypeRegisteredEventId, Level = EventLevel.Informational, Message = "Service host process {0} registered service type {1}", Keywords = Keywords.ServiceInitialization)]
    public void ServiceTypeRegistered(int hostProcessId, string serviceType)
    {
        WriteEvent(ServiceTypeRegisteredEventId, hostProcessId, serviceType);
    }

    // The ServiceHostInitializationFailed event contains a unique identifier, an event attribute that defined the event, and the code implementation of the event.
    private const int ServiceHostInitializationFailedEventId = 4;
    [Event(ServiceHostInitializationFailedEventId, Level = EventLevel.Error, Message = "Service host initialization failed", Keywords = Keywords.ServiceInitialization)]
    public void ServiceHostInitializationFailed(string exception)
    {
        WriteEvent(ServiceHostInitializationFailedEventId, exception);
    }

    ...

```

### <a name="using-eventsource-generically"></a>EventSource általános használatával

Adott események meghatározása nehéz lehet, mivel Sokan néhány események, amelyek általában kimenete egy karakterlánc adataik paraméterek közös csoportját határozza meg. Nagy része a strukturált aspektus elvész, és a bonyolultabb, és az eredmények szűréséhez. Ebben a megközelítésben néhány események, amelyek megfelelnek a naplózási szintek általában vannak meghatározva. Az alábbi kódrészlet egy hibakeresési és a hiba üzenetet határozza meg:

```csharp
[EventSource(Name = "MyCompany-VotingState-VotingStateService")]
internal sealed class ServiceEventSource : EventSource
{
    public static readonly ServiceEventSource Current = new ServiceEventSource();

    // The Instance constructor is private, to enforce singleton semantics.
    private ServiceEventSource() : base() { }

    ...

    private const int DebugEventId = 10;
    [Event(DebugEventId, Level = EventLevel.Verbose, Message = "{0}")]
    public void Debug(string msg)
    {
        WriteEvent(DebugEventId, msg);
    }

    private const int ErrorEventId = 11;
    [Event(ErrorEventId, Level = EventLevel.Error, Message = "Error: {0} - {1}")]
    public void Error(string error, string msg)
    {
        WriteEvent(ErrorEventId, error, msg);
    }

    ...

```

A strukturált és Általános kialakítási hibrid használatával is is megfelelően működjön. Strukturált kialakítási szolgál a jelentéskészítési hibák és a metrikák. A részletes naplózást, a hibaelhárításhoz mérnökök által felhasznált általános események használható.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

Az ASP.NET Core-naplózás ([Microsoft.Extensions.Logging NuGet-csomag](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) egy naplózási keretrendszer, amely a standard szintű naplózási API-t biztosít az alkalmazás. Más naplózási háttérrendszerek támogatása ASP.NET Core-naplózás lehet csatlakoztatni. Így sokkal kód módosítása nélkül az alkalmazás a bejelentkezés támogatását számos feldolgozását.

1. Adja hozzá a **Microsoft.Extensions.Logging** NuGet csomagot a projekthez, eszközt szeretne. Továbbá adja hozzá a szolgáltató csomagokat. További információkért lásd: [az ASP.NET Core-naplózás](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Adjon hozzá egy **használatával** direktívát **Microsoft.Extensions.Logging** a szolgáltatás fájlhoz.
3. Adjon meg egy privát változót a szolgáltatás osztályon belül.

   ```csharp
   private ILogger _logger = null;
   ```

4. Az osztály a konstruktorban lévő adja hozzá ezt a kódot:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Indítsa el a szándékkal, a metódusok a kódot. Az alábbiakban néhány minták:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Más naplózási szolgáltatók használatával

Néhány külső szolgáltatók használatát a megközelítés az előző szakaszban leírt beleértve [Serilog](https://serilog.net/), [NLog](https://nlog-project.org/), és [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Ezen az ASP.NET Core-naplózás is csatlakoztathatja, vagy külön-külön használhatná őket. Serilog van olyan szolgáltatás, amely bővíti a naplózó küldött üzenetek. Ez a funkció akkor lehet hasznos, a kimenetben a szolgáltatás neve, típusa és a partíciónak az adatait. Ez a funkció használata az ASP.NET Core-infrastruktúra, hajtsa végre ezeket a lépéseket:

1. Adja hozzá a **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**, és **Serilog.Sinks.Observable** NuGet-csomagok a projekthez. 
2. Hozzon létre egy `LoggerConfiguration` és a naplózó példány.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Adjon hozzá egy `Serilog.ILogger` a szolgáltatás konstruktor, és az újonnan létrehozott naplózó pass argumentum.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. A szolgáltatás konstruktor hoz létre a tulajdonság enrichers **servicetypename tulajdonság is**, **ServiceName**, **PartitionId**, és **InstanceId** .

   ```csharp
   public Stateless(StatelessServiceContext context, Serilog.ILogger serilog)
       : base(context)
   {
       PropertyEnricher[] properties = new PropertyEnricher[]
       {
           new PropertyEnricher("ServiceTypeName", context.ServiceTypeName),
           new PropertyEnricher("ServiceName", context.ServiceName),
           new PropertyEnricher("PartitionId", context.PartitionId),
           new PropertyEnricher("InstanceId", context.ReplicaOrInstanceId),
       };

       serilog.ForContext(properties);

       _logger = new LoggerFactory().AddSerilog(serilog.ForContext(properties)).CreateLogger<Stateless>();
   }
   ```

5. Alakítsa ki úgy a kódot ugyanaz, mintha az ASP.NET Core Serilog nélkül használja.

   >[!NOTE]
   >Azt javasoljuk, hogy Ön *nem* használhatja a statikus `Log.Logger` az előző példában. A Service Fabric belül egyetlen folyamat ugyanolyan szolgáltatás több példánya is üzemeltethet. Ha a statikus `Log.Logger`, a legutolsó író, a tulajdonság enrichers jelennek meg értékek az összes futtató példányhoz. Ez az egyik oka _logger változó a szolgáltatás osztály tagjai változót. Is, meg kell adni a `_logger` közös kód, amely előfordulhat, hogy használható-szolgáltatás számára elérhető.

## <a name="next-steps"></a>További lépések

- További információ [alkalmazás figyelése a Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Olvassa el a naplózás [eventflow segítségével](service-fabric-diagnostics-event-aggregation-eventflow.md) és [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).











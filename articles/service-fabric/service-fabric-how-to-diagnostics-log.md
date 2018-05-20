---
title: Alkalmazásnapló-események generálása egy .NET Service Fabric-alkalmazás az Azure rendszerben vagy egy önálló fürtön
description: Ismerje meg a naplózási felvétele az Azure-fürttel vagy egy önálló fürtön futtatott .NET Service Fabric-alkalmazás.
services: service-fabric
documentationcenter: .net
author: thraka
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/27/2018
ms.author: adegeo
ms.openlocfilehash: ed9aaf67b4f6749ea6d505a51fbc76e3d1cf0870
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="add-logging-to-your-service-fabric-application"></a>Naplózási hozzáadása a Service Fabric-alkalmazás

Az alkalmazás kell utaljon forensically hibakeresési, amikor probléma merül fel. Naplózás az egyik legfontosabb, amit a Service Fabric-alkalmazás is hozzáadhat. Ha hiba történik, jó naplózási tudhatja meg oly módon, vizsgálja meg a hibákat. Napló minták elemzésével, azt tapasztalhatja lehetőségek a teljesítmény vagy az alkalmazás javítására. Ez a dokumentum néhány különböző naplózási beállításokat mutatja be.

## <a name="eventflow"></a>EventFlow

A [EventFlow könyvtár](https://github.com/Azure/diagnostics-eventflow) csomag lehetővé teszi az alkalmazások milyen diagnosztikai adatainak összegyűjtése meghatározásához, és ha azok kell gyártandó való. Diagnosztikai adatok az alkalmazások nyomkövetéseit kérelemteljesítmény-számlálókat bármi lehet. Az fut meg az alkalmazás, így kommunikációs terhelés másodpercekre csökken. További információ a EventFlow és a Service Fabric: [Azure Service Fabric esemény összesítési EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).

### <a name="using-structured-eventsource-events"></a>Strukturált EventSource eseményeket használ

Események nagybetűhasználattal definiáló üzenet lehetővé teszi csomagadatok az eseményről, az esemény környezetében. Könnyebben kereshet, és a szűrő nevét vagy a megadott esemény tulajdonságok értékei alapján. A instrumentation kimeneti teszi szerkezetének kialakítása könnyebben olvasható, de igényel további gondolat és időpontot, adja meg minden egyes esetnél esemény. 

Néhány esemény definíciókat meg lehet osztani a teljes alkalmazás között. Például egy metódus indítási vagy leállítási esemény volna használni az alkalmazáson belül számos szolgáltatásban. Tartományspecifikus szolgáltatás, például egy rendelés rendszer rendelkezhet egy **CreateOrder** esemény, amelynek a saját egyedi esemény. Ezt a módszert előfordulhat, hogy sok események generálásához, és előfordulhat, hogy a azonosítók összehangolását projekt csapatok. 

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

Adott események meghatározása nehéz lehet, mert sokan meghatározása néhány események, amelyek általában kimenete egy karakterlánc adataikat paraméterek közös állítja be. Nagy részét a strukturált aspektus elvész, és nehezebb keresheti ki és az eredmények szűréséhez. A ezt a módszert használja néhány olyan események, amelyek általában felelnek meg a naplózási szintek határozzák meg. A következő kódrészletet a hibakeresési és a hiba üzenet határozza meg:

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

A strukturált és általános instrumentation hibrid használatával is is működőképesek. Strukturált instrumentation és metrikákat szolgál. Általános események a részletes naplózást, a hibaelhárítási mérnökök által felhasznált használható.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Logging

Az ASP.NET Core-naplózás ([Microsoft.Extensions.Logging NuGet-csomag](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) egy naplózási keretrendszer, amely a szabványos naplózási alkalmazásprogramozási Felületet biztosít az alkalmazás. Más naplózási háttérkiszolgálókon támogatása az ASP.NET Core naplózás lehet csatlakoztatni. Így az alkalmazás naplózási támogatása számos dolgoz fel, mennyi kód módosítása nélkül.

1. Adja hozzá a **Microsoft.Extensions.Logging** NuGet csomag a projekt meg eszköz szeretné. Továbbá adja hozzá a szolgáltató csomagokat. További információkért lásd: [ASP.NET Core-naplózás](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Adja hozzá a **használatával** az irányelv **Microsoft.Extensions.Logging** a szolgáltatás fájlba.
3. A szolgáltatás osztályon belül a titkos változó megadása.

   ```csharp
   private ILogger _logger = null;
   ```

4. Az osztály konstruktorában adja hozzá ezt a kódot:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Indítsa el a kódot a metódusok leírására. Az alábbiakban néhány minták:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Más naplózási szolgáltatók használata

Egyes harmadik fél szolgáltató használja a megközelítést az előző szakaszban leírt beleértve [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/), és [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Ezen az ASP.NET Core naplózás csatlakoztatható, illetve külön-külön használhatja őket. Serilog egy szolgáltatás, amely a következőképpen színesíti a naplózó küldött üzenetek rendelkezik. Ez a szolgáltatás a szolgáltatás neve, típusa és a partíciónak az adatait a mentendő hasznos lehet. Az ASP.NET alapvető infrastruktúra Ez a funkció használatához tegye ezeket a lépéseket:

1. Adja hozzá a **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**, és **Serilog.Sinks.Observable** NuGet-csomagok a projekthez. 
2. Hozzon létre egy `LoggerConfiguration` és a tranzakciónaplókat tartalmazó példány.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Adja hozzá a `Serilog.ILogger` szolgáltatás konstruktor, és az újonnan létrehozott naplózó fázis argumentum.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. A szolgáltatás konstruktor hoz létre a tulajdonság enrichers **ServiceTypeName**, **szolgáltatásnév**, **PartitionId**, és **InstanceId** .

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

5. Állíthatnak be a kódját azonos, ha az ASP.NET Core Serilog nélkül.

   >[!NOTE]
   >Azt javasoljuk, hogy Ön *nem* használja a statikus `Log.Logger` az előző példában a. A Service Fabric tárolhatja, a szolgáltatás ugyanolyan egyetlen eljáráson belül több példányát. Ha a statikus `Log.Logger`, a tulajdonság enrichers utolsó írója futtatják szoftverpéldányok értékeit megjeleníti. Ez az egyik oka a _logger változó szolgáltatásosztály saját tagként változó. Emellett meg kell győződnie a `_logger` közös kód, amely felhasználható szolgáltatásban elérhető.

## <a name="next-steps"></a>További lépések

- További információt olvashat [alkalmazás figyelése a Service Fabric](service-fabric-diagnostics-event-generation-app.md).
- Olvassa el a naplózási [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) és [Windows Azure diagnosztikai](service-fabric-diagnostics-event-aggregation-wad.md).











---
title: Naplóesemények létrehozása .NET alkalmazásból
description: Megtudhatja, hogyan adhat hozzá naplózást a .NET Service Fabric-alkalmazáshoz, amelyet egy Azure-fürtön vagy egy önálló fürtön üzemeltet.
author: srrengar
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: srrengar
ms.openlocfilehash: 8c4721584e74bd7f7111c516f2d16bd190392bb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614366"
---
# <a name="add-logging-to-your-service-fabric-application"></a>Naplózás hozzáadása Service Fabric-alkalmazáshoz

Az alkalmazásnak elegendő információt kell nyújtania ahhoz, hogy probléma esetén kriminalisztikailag debugot okozhassa. A naplózás az egyik legfontosabb dolog, amelyet hozzáadhat a Service Fabric-alkalmazáshoz. Hiba esetén a jó naplózás lehetőséget adhat a hibák kivizsgálására. A naplóminták elemzésével megtalálhatja a módját, hogy javítsa az alkalmazás teljesítményét vagy kialakítását. Ez a dokumentum néhány különböző naplózási lehetőséget mutat be.

## <a name="eventflow"></a>EventFlow (Eseményfolyamat)

Az [EventFlow-függvénycsomag](https://github.com/Azure/diagnostics-eventflow) lehetővé teszi az alkalmazások számára, hogy meghatározzák, milyen diagnosztikai adatokat gyűjtsenek, és hová kell azokat kikell adni. A diagnosztikai adatok a teljesítményszámlálóktól az alkalmazásnyomkövetésekig bármi lehet. Az alkalmazással azonos folyamat ban fut, így a kommunikációs terhelés minimálisra csökken. Az EventFlow és a Service Fabric szolgáltatásról további információt az [Azure Service Fabric Event Aggregation with EventFlow című témakörben talál.](service-fabric-diagnostics-event-aggregation-eventflow.md)

### <a name="using-structured-eventsource-events"></a>Strukturált EventSource-események használata

Az üzenetesemények használati eset szerint történő definiálása lehetővé teszi az eseményadatait az esemény kontextusában történő csomagolását. A megadott eseménytulajdonságok neve vagy értéke alapján könnyebben kereshet és szűrhet. A műszerezési kimenet strukturálása megkönnyíti az olvasást, de több gondolkodást és időt igényel az egyes használati eseteseményekhez való esemény meghatározásához. 

Egyes eseménydefiníciók megoszthatók a teljes alkalmazás között. Például egy metódus indítási vagy leállítási esemény lenne újra felkell használni az alkalmazáson belüli számos szolgáltatás között. Egy tartományspecifikus szolgáltatás, például egy rendelési rendszer, rendelkezhet egy CreateOrder-eseménnyel, amely saját egyedi eseménnyel rendelkezik. **CreateOrder** Ez a megközelítés számos eseményt generálhat, és potenciálisan az azonosítók koordinálását igényli a projektcsapatok között. 

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

### <a name="using-eventsource-generically"></a>Az EventSource általános használata

Mivel bizonyos események definiálása nehéz lehet, sok ember definiál néhány eseményt olyan közös paraméterekkel, amelyek általában karakterláncként adják ki az adataikat. A strukturált szempont nagy része elvész, és nehezebb keresni és szűrni az eredményeket. Ebben a megközelítésben néhány olyan esemény van definiálva, amelyek általában megfelelnek a naplózási szinteknek. A következő kódrészlet hibakeresési és hibaüzenetet határoz meg:

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

A strukturált és általános műszerek hibridjének használata is jól működhet. A strukturált instrumentation a hibák és mutatók jelentésére szolgál. Az általános események a mérnökök által hibaelhárításhoz felhasznált részletes naplózáshoz használhatók.

## <a name="microsoftextensionslogging"></a>Microsoft.Extensions.Naplózás

A ASP.NET Core naplózás ([Microsoft.Extensions.Logging NuGet csomag](https://www.nuget.org/packages/Microsoft.Extensions.Logging)) egy naplózási keretrendszer, amely egy szabványos naplózási API-t biztosít az alkalmazásszámára. Más naplózási háttérrendszerek támogatása csatlakoztatható ASP.NET Core naplózáshoz. Ez ad ön egy széles körű támogatást bejelentkezés az alkalmazás feldolgozása, anélkül, hogy sok kódot.

1. Adja hozzá a **Microsoft.Extensions.Logging** NuGet csomagot az hangszerelni kívánt projekthez. Adja hozzá a szolgáltatói csomagokat is. További információ: [Logging in ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Adjon hozzá egy **using** directive for **Microsoft.Extensions.Logging** a szolgáltatásfájlhoz.
3. Definiáljon egy privát változót a szolgáltatási osztályon belül.

   ```csharp
   private ILogger _logger = null;
   ```

4. A szervizosztály konstruktorában adja hozzá ezt a kódot:

   ```csharp
   _logger = new LoggerFactory().CreateLogger<Stateless>();
   ```

5. Kezdje el a kód programozását a módszereiben. Íme néhány minta:

   ```csharp
   _logger.LogDebug("Debug-level event from Microsoft.Logging");
   _logger.LogInformation("Informational-level event from Microsoft.Logging");

   // In this variant, we're adding structured properties RequestName and Duration, which have values MyRequest and the duration of the request.
   // Later in the article, we discuss why this step is useful.
   _logger.LogInformation("{RequestName} {Duration}", "MyRequest", requestDuration);
   ```

### <a name="using-other-logging-providers"></a>Más naplózási szolgáltatók használata

Egyes külső szolgáltatók az előző szakaszban leírt megközelítést használják, beleértve a [Serilog,](https://serilog.net/) [NLog](https://nlog-project.org/)és [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Ezek mindegyikét csatlakoztathatja ASP.NET Core naplózáshoz, vagy külön használhatja őket. Serilog van egy funkció, amely gazdagítja az összes üzenetet küldött egy logger. Ez a szolgáltatás hasznos lehet a szolgáltatás nevének, típusának és partíciójának adatainak kimenetéhez. Ha ezt a funkciót a ASP.NET Core infrastruktúrában szeretné használni, tegye az alábbi lépéseket:

1. Adja hozzá a **Serilog**, **Serilog.Extensions.Logging**, **Serilog.Sinks.Literate**és **Serilog.Sinks.Observable** NuGet csomagokat a projekthez. 
2. Hozzon `LoggerConfiguration` létre egy és a naplózó példányt.

   ```csharp
   Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
   ```

3. Adjon `Serilog.ILogger` hozzá egy argumentumot a szolgáltatáskonstruktorhoz, és adja át az újonnan létrehozott naplózót.

   ```csharp
   ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
   ```

4. A szolgáltatáskonstruktorban létrehozza a **ServiceTypeName**, **ServiceName**, **PartitionId**és InstanceId tulajdonságdígelzéítőit. **InstanceId**

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

5. A kódot ugyanúgy kell beiktatni, mintha ASP.NET Core-t használna Serilog nélkül.

   >[!NOTE]
   >Azt javasoljuk, hogy *ne* használja `Log.Logger` a statikus az előző példában. A Service Fabric egyetlen folyamaton belül több azonos szolgáltatástípusból álló példányt is üzemeltethet. A statikus `Log.Logger`használata esetén a tulajdonságszintetícérók utolsó írója minden futó példány értékét megfogja jeleníteni. Ez az egyik oka annak, hogy a _logger változó a szolgáltatásosztály privát tagváltozója. Emellett elérhetővé kell `_logger` tennie a közös kódot, amely különböző szolgáltatásokban használható.

## <a name="next-steps"></a>További lépések

- További információ az [alkalmazásfigyelésről a Service Fabric alkalmazásban.](service-fabric-diagnostics-event-generation-app.md)
- További információ az [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) és a [Windows Azure Diagnosztika naplózásáról.](service-fabric-diagnostics-event-aggregation-wad.md)











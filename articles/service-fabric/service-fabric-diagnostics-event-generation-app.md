---
title: "Az Azure Service Fabric alkalmazásszintű figyelése |} Microsoft Docs"
description: "Tudnivalók az alkalmazás és szolgáltatás szintű esemény és figyelése és diagnosztizálása Azure Service Fabric-fürtök használt naplókat."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/26/2017
ms.author: dekapur
ms.openlocfilehash: 3c472904641108b7383cd0f1416c47460f8de11a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="application-and-service-level-event-and-log-generation"></a>Alkalmazás és szolgáltatás szintű esemény és a napló létrehozása

## <a name="instrumenting-the-code-with-custom-events"></a>Egyéni események kód tagolása

A kód tagolása alapja legtöbb egyéb elemeinek a szolgáltatások figyelése. Instrumentation csak úgy is, hogy valami nem megfelelő, és javításra szorul diagnosztizálása érdekében. Bár technikailag lehetséges hibakereső kapcsolódni egy éles szolgáltatáshoz, nincs gyakori eljárásnak számít. Úgy hogy a WMI-adatok részletes fontos.

Egyes termékek automatikusan állíthatnak be a kódját. Bár ezek a megoldások is működőképesek, kézi instrumentation szinte mindig szükség. A végén az alkalmazás hibakeresése forensically elegendő információt kell rendelkeznie. Ez a dokumentum ismerteti a kódot, és ha egyik módszer kiválasztásához másikkal tagolása eltérő megközelítést.

## <a name="eventsource"></a>Eseményforrás
A Service Fabric megoldást a Visual Studio sablonból létrehozásakor egy **EventSource**-osztályt (**ServiceEventSource** vagy **ActorEventSource**) jön létre . A sablon jön létre, az alkalmazás vagy szolgáltatás események adhat hozzá. A **EventSource** neve **kell** egyedinek lennie, és az alapértelmezett sablon karakterláncból értéket - érdemes nevezhető&lt;megoldás&gt;-&lt;projekt &gt;. Több **EventSource** tulajdonságdefiníciót használja ugyanazt a nevet a futási időben problémát okoz. Minden egyes meghatározott esemény egyedi azonosítóval kell rendelkeznie. Ha egy azonosító nem egyedi, futásidejű hiba történik. Egyes szervezetek preassign külön fejlesztési csapat közötti ütközések elkerülésével azonosítói értékeit a tartományait. További információkért lásd: [előlegbekérő 's blog](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) vagy a [az MSDN dokumentációjában tájékozódhat](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

### <a name="using-structured-eventsource-events"></a>Strukturált EventSource eseményeket használ

Az eseményeket az ebben a szakaszban szereplő példák mindegyikében megadva egy különleges esetben, például amikor egy szolgáltatástípus regisztrálva van. Üzenetek definiált használati eset, adatok hozzáadható a szöveget a hiba, és több is egyszerűen keressen, és a szűrő nevét vagy a megadott tulajdonságok értékei alapján. Szerkezetének kialakítása a instrumentation kimeneti teszi adathalmazokban való felhasználását, de szükséges, további gondolat és időt adja meg egy új eseményt minden használati eset. Néhány esemény definíciókat meg lehet osztani a teljes alkalmazás között. Például egy metódus indítási vagy leállítási esemény volna használni az alkalmazáson belül számos szolgáltatásban. Tartományspecifikus szolgáltatás, például egy rendelés rendszer rendelkezhet egy **CreateOrder** esemény, amelynek a saját egyedi esemény. Ezt a módszert előfordulhat, hogy sok események generálásához, és előfordulhat, hogy a azonosítók összehangolását projekt csapatok. 

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
```

A strukturált és általános instrumentation hibrid használatával is is működőképesek. Strukturált instrumentation és metrikákat szolgál. Általános események a részletes naplózást, a hibaelhárítási mérnökök által felhasznált használható.

## <a name="aspnet-core-logging"></a>Az ASP.NET Core naplózása

Fontos alapos megtervezéséről, hogyan fogja beállíthatják a kódot. A jobb oldali instrumentation terv segítséget nyújt a potenciálisan destabilizing kódbázis, és hogy a kód reinstrument majd kellene elkerülése érdekében. Kockázatok csökkentése érdekében dönthet úgy, mint egy rendszerállapot-tára [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), amely Microsoft ASP.NET Core része. Az ASP.NET Core rendelkezik egy [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) felület, amely csak akkor használhatja a szolgáltató az Ön által választott, ugyanakkor minimalizálja a meglévő kódot hatással. Az ASP.NET Core Windows és Linux kódot is használhatja, és a teljes .NET-keretrendszer, ezért a rendszerállapot-kód szabványosított. Ez további felfedezte alatt:

### <a name="using-microsoftextensionslogging-in-service-fabric"></a>A Service Fabric Microsoft.Extensions.Logging használata

1. A Microsoft.Extensions.Logging NuGet-csomag hozzáadása a projekthez kívánt eszközt. Továbbá adja hozzá a szolgáltató csomagokat (a külső csomag, lásd az alábbi példában). További információkért lásd: [ASP.NET Core-naplózás](https://docs.microsoft.com/aspnet/core/fundamentals/logging).
2. Adja hozzá a **használatával** irányelv Microsoft.Extensions.Logging számára a szolgáltatás fájlba.
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

## <a name="using-other-logging-providers"></a>Más naplózási szolgáltatók használata

Egyes harmadik fél szolgáltató használja a megközelítést az előző szakaszban leírt beleértve [Serilog](https://serilog.net/), [NLog](http://nlog-project.org/), és [Loggr](https://github.com/imobile3/Loggr.Extensions.Logging). Ezen az ASP.NET Core naplózás csatlakoztatható, illetve külön-külön használhatja őket. Serilog egy szolgáltatás, amely a következőképpen színesíti a naplózó küldött üzenetek rendelkezik. Ez a szolgáltatás a szolgáltatás neve, típusa és a partíciónak az adatait a mentendő hasznos lehet. Az ASP.NET alapvető infrastruktúra Ez a funkció használatához tegye ezeket a lépéseket:

1. A Serilog Serilog.Extensions.Logging és Serilog.Sinks.Observable NuGet-csomagok hozzáadása a projekthez. A következő példában is hozzáadhat Serilog.Sinks.Literate. Jobb megközelítés a cikk későbbi részében látható.
2. Serilog hozzon létre egy LoggerConfiguration és a tranzakciónaplókat tartalmazó példány.

  ```csharp
  Log.Logger = new LoggerConfiguration().WriteTo.LiterateConsole().CreateLogger();
  ```

3. Adja hozzá a Serilog.ILogger argumentum a szolgáltatás konstruktor, és adja át az újonnan létrehozott naplózó.

  ```csharp
  ServiceRuntime.RegisterServiceAsync("StatelessType", context => new Stateless(context, Log.Logger)).GetAwaiter().GetResult();
  ```

4. A szolgáltatás konstruktort, adja hozzá az alábbi kód, amely létrehozza a tulajdonság enrichers a a **ServiceTypeName**, **szolgáltatásnév**, **PartitionId**, és **InstanceId** a szolgáltatás tulajdonságait. Hozzáadja a is egy tulajdonság enricher az ASP.NET Core naplózási gyári, hogy használhassa Microsoft.Extensions.Logging.ILogger a kódban.

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
  >Azt javasoljuk, hogy nem adja meg a statikus Log.Logger az előző példában a. A Service Fabric tárolhatja, a szolgáltatás ugyanolyan egyetlen eljáráson belül több példányát. A statikus Log.Logger használja, ha a tulajdonság enrichers utolsó írója futtatják szoftverpéldányok értékeinek jelennek meg. Ez az egyik oka a _logger változó szolgáltatásosztály saját tagként változó. Is meg kell adni a _logger közös kód, amely felhasználható szolgáltatásban elérhető.

## <a name="choosing-a-logging-provider"></a>Egy naplózási szolgáltató kiválasztása

Ha az alkalmazás nagy teljesítményű, **EventSource** van általában egy jó módszer. **EventSource** *általában* kevesebb erőforrást használ, és az ASP.NET Core naplózás, illetve a rendelkezésre álló külső megoldások jobb teljesítményt.  Ez számos szolgáltatás, de ha a szolgáltatás-e a teljesítmény-központú, használja a problémát nem **EventSource** megfelelőbb választás lehet. Azonban ezek előnyök a naplózás, strukturált **EventSource** egy nagyobb befektetési a mérnöki csapat igényel. Ha lehetséges néhány naplózási beállítások gyors prototípus tegye, és válassza ki azt, amelyik az igényeinek leginkább megfelelő.

## <a name="next-steps"></a>Következő lépések

Miután kiválasztotta a naplózás szolgáltató is beállíthatják az alkalmazások és szolgáltatások, a naplók és események összesíthető ahhoz, azok bármely analysis platform lehet küldeni kell. További információ a [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) és [ÜVEGVATTA](service-fabric-diagnostics-event-aggregation-wad.md) jobb megértése érdekében ajánlott beállítások egy része.

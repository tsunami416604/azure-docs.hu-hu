---
title: "Megbízható kommunikáció áttekintése |} Microsoft Docs"
description: "A Reliable Services kommunikációs modellt, beleértve a szolgáltatások figyelőinek megnyitásakor, végpontok megoldása és -szolgáltatások közötti kommunikáció áttekintése."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: BharatNarasimman
ms.assetid: 36217988-420e-409d-b0a4-e0e875b6eac8
ms.service: service-fabric
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 209e657678b7f300f13fc16181a14d8ef422466d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>A Reliable Services kommunikációs API-k használata
Az Azure Service Fabric platformként rendszer teljesen független kapcsolatos szolgáltatások közötti kommunikáció. Protokollok és a verem elfogadhatók, az UDP HTTP. A szolgáltatás fejlesztők kiválaszthatja, hogyan lépjen kapcsolatba a szolgáltatások van. A Reliable Services alkalmazás-keretrendszer biztosít, beépített kommunikációs verem, valamint az API-t is használhatja a kommunikációhoz egyéni összetevők létrehozása.

## <a name="set-up-service-communication"></a>Szolgáltatások közötti kommunikáció beállítása
A megbízható szolgáltatások API szolgáltatások közötti kommunikáció egy egyszerű felületen használ. Nyissa meg a szolgáltatási végpont, egyszerűen valósítja meg az illesztő:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

A szolgáltatás-alapú osztály metódus-felülbírálása visszaküldésével majd a kommunikációs figyelő megvalósítás is hozzáadhat.

Az állapotmentes szolgáltatások:

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

Az állapotalapú szolgáltatások:

> [!NOTE]
> Állapot-nyilvántartó megbízható szolgáltatások nem támogatottak a Java még.
>
>

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

Mindkét esetben meg kell visszaadnia figyelők. Ez lehetővé teszi a szolgáltatás a több végpontot, potenciálisan protokollal különböző, több figyelői használatával történő figyelésre. Például előfordulhat, hogy egy HTTP-figyelő és egy külön WebSocket-figyelő. Minden egyes figyelő nevét és az eredményül kapott gyűjteménye lekérdezi *name: cím* párok jelentésekként jelennek meg a JSON-objektumból, amikor egy ügyfél egy szolgáltatáspéldány vagy partíció figyelő címeket igényel.

Az állapotmentes szolgáltatások a felülbírálás ServiceInstanceListeners gyűjteményének beolvasása. A `ServiceInstanceListener` létrehozásához függvényt tartalmaz egy `ICommunicationListener(C#) / CommunicationListener(Java)` és elnevezi azt. Állapotalapú szolgáltatások esetén a felülbírálás ServiceReplicaListeners gyűjteményének beolvasása. Ez egy némileg eltérő a állapotmentes párjukhoz, mert egy `ServiceReplicaListener` rendelkezik olyan elemmel nyithatja meg egy `ICommunicationListener` a másodlagos replikákon. Nem csak használhatók több kommunikációs figyelőket a szolgáltatásban, de azt is megadhatja mely figyelők a másodlagos replikákon kérelmek fogadásához, és melyeket figyelni csak az elsődleges replikára változott.

Például rendelkezhet egy ServiceRemotingListener, amely RPC-hívások csak az elsődleges replikára változott, és egy második, egyéni figyelő, amely olvasási kérések a másodlagos replikákon HTTP Protokollon keresztül:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> Egy szolgáltatás minden egyes figyelő több figyelői létrehozásakor **kell** egy egyedi nevet kell adni.
>
>

Végezetül ismertetik a végpontokat a szolgáltatáshoz szükséges a [szolgáltatás jegyzékfájl](service-fabric-application-and-service-manifests.md) végpontokon szakaszban.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

A kommunikációs figyelő férhetnek hozzá a végpont erőforrások vannak rendelve azt a `CodePackageActivationContext` a a `ServiceContext`. A figyelő is indíthatja a kérések figyelését, ha meg van nyitva.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> A teljes szolgáltatás csomag végpont erőforrások megegyeznek, és azok osztja a Service Fabric szolgáltatás aktiválásakor. Több, ugyanazt a ServiceHost üzemeltetett szolgáltatás replika megoszthatja ugyanazt a portot. Ez azt jelenti, hogy a kommunikáció figyelő támogatnia kell a port megosztása. Ez az ajánlott módszer van a kommunikációs figyelőt, hogy a partíció, és replika/példány-azonosító használata a figyelési cím létrehozása során.
>
>

### <a name="service-address-registration"></a>Service cím regisztrálása
Rendszerszolgáltatás hívása a *szolgáltatás* Service Fabric-fürtök futtatja. A szolgáltatás a szolgáltatások és az-címét, hogy minden példány vagy a replikát a szolgáltatás nem a tartományregisztráló. Ha a `OpenAsync(C#) / openAsync(Java)` metódusában egy `ICommunicationListener(C#) / CommunicationListener(Java)` befejeződött, a visszatérési érték a szolgáltatás lekérdezi regisztrálva. A visszatérési érték a szolgáltatás lekérdezi közzétett egy olyan karakterlánc, amelynek az értéke bármi lehet minden. A karakterlánc értéke ügyfelek látják, ha azok kérjen egy címet a szolgáltatás a szolgáltatás a.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

A Service Fabric biztosít az API-k, amelyek lehetővé teszik az ügyfelek és egyéb szolgáltatások majd feltenni ehhez a címhez szolgáltatás neve. Ez azért fontos, mert a szolgáltatás-cím nem statikus. Szolgáltatások helyezi át a fürt erőforrás és a rendelkezésre állás céljából. Azt a mechanizmust, amelyek lehetővé teszik az ügyfelek fel tudják oldani a figyelő cím egy szolgáltatás számára.

> [!NOTE]
> A kommunikációs figyelő írásával teljes segédlet, lásd: [Service Fabric Web API szolgáltatások OWIN önálló üzemeltető](service-fabric-reliable-services-communication-webapi.md) C#, Java írhat a saját HTTP megvalósított NFS-kiszolgáló, mivel lásd EchoServer alkalmazás Példa: https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>A szolgáltatással folytatott kommunikáció
A megbízható szolgáltatások API biztosít az alábbi kódtárak, szolgáltatások kommunikáló ügyfelek írni.

### <a name="service-endpoint-resolution"></a>Szolgáltatási végpont felbontás
A szolgáltatással való kommunikáció első lépése a megoldásához végpontcím a partíció vagy a szolgáltatást, kérdezze meg a kívánt példány. A `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` segédprogram osztály egy alapszintű primitív, amellyel az ügyfelek határozza meg a végpont egy szolgáltatás futási időben. A Service Fabric terminológia határozhatja meg a szolgáltatások végpontja nevezzük a *végpont névfeloldási szolgáltatás*.

A fürtön belüli szolgáltatások csatlakoznak, az alapértelmezett beállításokkal ServicePartitionResolver is létrehozható. Ez az az ajánlott használati esetek többségében:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Egy másik fürthöz szolgáltatásaihoz csatlakozni egy ServicePartitionResolver fürt átjáró végpontok készlete hozhatja létre. Ne feledje, hogy átjáró végpontok csak másik végpontok az azonos fürthöz való csatlakozáshoz. Példa:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Másik lehetőségként `ServicePartitionResolver` adható meg a függvény létrehozásához egy `FabricClient` belső használatára:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient`egy olyan objektum, a Service Fabric-fürt a fürt a különböző felügyeleti műveleteihez folytatott kommunikációhoz használt. Ez akkor hasznos, ha azt szeretné, hogy egy szolgáltatás partíció feloldó hogyan működjön együtt a fürt teljesebb körű vezérlése. `FabricClient`elvégzi a belső gyorsítótár, és általában költséges szeretne létrehozni, ezért fontos újból `FabricClient` példányok szerint lehetséges.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

A resolve metódus van majd beolvassa a cím egy szolgáltatás vagy szolgáltatás partíciója particionált szolgáltatások számára.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

A szolgáltatás címe egyszerűen a egy ServicePartitionResolver segítségével megoldható, de a további munkahelyi szükséges annak érdekében, hogy megfelelően a feloldott cím is használható. Az ügyfélnek észleli, hogy a kapcsolódási kísérlet egy átmeneti hiba miatt sikertelen volt, amit követően újra kell (pl. szolgáltatás áthelyezése vagy átmenetileg nem érhető el), vagy egy állandó hiba (pl. szolgáltatást törölték vagy a kért erőforrás már nem létezik). Egy szolgáltatáspéldány vagy replikák áthelyezheti csomópontról csomópont több okokból bármikor. Lehet, hogy a ServicePartitionResolver révén címét az Ügyfélkód megpróbál kapcsolódni a időpontjára elavult. Ebben az esetben újra az ügyfélnek kell újra a címek feloldására. Így az előző `ResolvedServicePartition` azt jelzi, hogy kell-e a feloldó az újrapróbálkozáshoz ahelyett, hogy egyszerűen lekérése egy gyorsítótárazott címet.

Általában az Ügyfélkód kell nem fog működni a ServicePartitionResolver közvetlenül. Létrehozott, és átadja kommunikációs ügyfél előállítók a megbízható Services API. A előállítók a feloldó belső használja egy ügyfél objektum, amely segítségével kommunikálnak a szolgáltatások létrehozásához.

### <a name="communication-clients-and-factories"></a>Kommunikáció az ügyfelek és előállítók
A kommunikációs gyári könyvtár egy tipikus hiba-kezelési újrapróbálkozási mintát, amely megkönnyíti a feloldott Szolgáltatásvégpontok újrapróbálása kapcsolatok valósítja meg. A gyári könyvtár biztosít az újrapróbálkozási mechanizmussal, míg a hibakezelők megadnia.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`Meghatározza a kommunikációs ügyfélgyára, amely létrehozza az ügyfelek számára, amely képes kommunikálni a Service Fabric-szolgáltatás által megvalósított alapszintű felületet. A CommunicationClientFactory végrehajtásának attól függ, hogy a kommunikációs verem a Service Fabric-szolgáltatás által használt, ahol az ügyfél kommunikálni kíván. A megbízható szolgáltatások API biztosít egy `CommunicationClientFactoryBase<TCommunicationClient>`. Egy alapszintű megvalósítás CommunicationClientFactory interfész biztosít, és a vonatkozó összes kommunikációs verem feladatokat hajtja végre. (Ezek a feladatok közé tartozik egy ServicePartitionResolver segítségével határozza meg a szolgáltatási végpont). Ügyfelek általában valósítja meg az absztrakt CommunicationClientFactoryBase osztály logika, amely jellemző a kommunikációs verem kezelésére.

A kommunikációs ügyfél csak egy címet kap, és használja a szolgáltatáshoz való kapcsolódáshoz. Az ügyfél bármilyen szeretnének protokoll használható.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

A ügyfélgyára elsődlegesen létrehozásáért felelős az ügyfelek kommunikációt. Az ügyfelek, amelyek nem tartanak fenn állandó kapcsolatot, például egy HTTP-ügyfél a gyári csak kell létrehozni, és térjen vissza az ügyfélnek. Annak eldöntéséhez, hogy szükséges-e a kapcsolat újból létrehozza a gyár által állandó kapcsolatot, például az egyes bináris protokollok más protokollokat is ellenőrizni kell.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Végül egy kivételkezelőbe felel annak meghatározásáért, mi a teendő, ha kivétel lép érvénybe. Kivételek kategóriákba vannak **Újrapróbálkozást lehetővé tevő** és **Újrapróbálkozást lehetővé nem tevő**.

* **Újrapróbálkozást lehetővé nem tevő** kivételek egyszerűen get újra kiváltja vissza a hívók számára.
* **Újrapróbálkozást lehetővé tevő** kivételek további kategóriákba **átmeneti** és **nem átmeneti**.
  * **Átmeneti** kivételek, azokat, egyszerűen nélkül újra feloldani a végpont címét ismételhető. Ez magában foglalja az átmeneti hálózati problémák vagy a szolgáltatás hibaválaszok kivételével, amelyek jelzik, hogy a végpont címét nem létezik.
  * **Nem tranziens** kivételek azok, amelyek a szolgáltatási végpont címe fel kell oldani újra igényelnek. Ezek közé tartozik a kivételeket, amelyek jelzik, hogy a szolgáltatási végpont nem érhető el, amely jelzi a szolgáltatás át lett helyezve egy másik csomópont.

A `TryHandleException` lehetővé teszi egy adott kivétel kapcsolatos döntést hoznak. Ha azt **nem tudja** milyen döntést kell meghoznia kapcsolatos kivétel, az kell visszaadnia **hamis**. Ha azt **tudja** milyen döntés, ennek megfelelően állítsa be az eredmény és vissza kell **igaz**.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {        

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>A teljes kép
Az egy `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, és `IExceptionHandler(C#) / ExceptionHandler(Java)` olyan kommunikációs protokollt épül egy `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` burkolt menüelem együtt, és a hiba-kezelés és a szolgáltatás partíció cím feloldási hurok körül ezeket az összetevőket tartalmaz.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Következő lépések
* [Az ASP.NET Core megbízható szolgáltatásokkal](service-fabric-reliable-services-communication-aspnetcore.md)
* [Távoli eljáráshívások a Reliable Services távoli eljáráshívás](service-fabric-reliable-services-communication-remoting.md)
* [WCF-kommunikáció Reliable Services használatával](service-fabric-reliable-services-communication-wcf.md)

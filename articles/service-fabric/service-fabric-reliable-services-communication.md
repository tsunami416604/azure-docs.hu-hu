---
title: A Reliable Services-kommunikáció – áttekintés |} A Microsoft Docs
description: A Reliable Services modellt, beleértve a szolgáltatások figyelőinek nyitó, végpontjainak feloldását és szolgáltatások közötti kommunikáció áttekintése.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: 36217988-420e-409d-b0a4-e0e875b6eac8
ms.service: service-fabric
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 15b45cadc69830827952d87ffc2315b06b07b02c
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663400"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>A Reliable Services-kommunikáció API-k használata
Azure Service Fabric-platformként a teljesen független kapcsolatos szolgáltatások közötti kommunikációt. Protokollok és a veremhez elfogadhatók, az UDP HTTP-re. Szolgáltatás a szolgáltatás fejlesztők hogyan kapcsolatba szolgáltatások kiválasztásához. A Reliable Services alkalmazási keretrendszer biztosít beépített kommunikációs környezetet, valamint az API-kat használhatja az egyéni kommunikációs összetevőket hozhat létre.

## <a name="set-up-service-communication"></a>A szolgáltatások közötti kommunikáció beállítása
A Reliable Services API-t a szolgáltatások közötti kommunikáció egy egyszerű felületet használja. Nyisson meg egy végpontot a szolgáltatáshoz, egyszerűen csak ez az interfész megvalósításához:

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

Ezután hozzáadhatja a kommunikációs figyelőjének megvalósítás egy osztály service-alapú módszer felülbírálásban visszaküldésével.

Az állapotmentes szolgáltatások:

```csharp
public class MyStatelessService : StatelessService
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

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

Mindkét esetben figyelői gyűjteményét adja vissza. Ez lehetővé teszi, hogy a szolgáltatás több végpontot, potenciálisan protokollal másik, több kérésfigyelőt használatával. Előfordulhat például, a HTTP-figyelő és a egy külön WebSocket-figyelő. Minden egyes figyelő nevét és az eredményül kapott gyűjteménye lekérdezi *name: cím* párok jelölt JSON-objektumként, amikor egy ügyfél egy szolgáltatáspéldány vagy egy partíció a figyelő címeket igényel.

Az állapotmentes szolgáltatás a felülbírálás ServiceInstanceListeners gyűjteményét adja vissza. A `ServiceInstanceListener` hozhat létre olyan függvényt tartalmaz egy `ICommunicationListener(C#) / CommunicationListener(Java)` , és elnevezi azt. Az állapotalapú szolgáltatások esetében a felülbírálás ServiceReplicaListeners gyűjteményét adja vissza. Ez az állapot nélküli párjukhoz kissé eltérhetnek mivel egy `ServiceReplicaListener` nyissa meg a lehetőség van egy `ICommunicationListener` másodlagos replikákon. Nem csak használható több kommunikációs figyelőket egy szolgáltatásban, de mely figyelők a másodlagos replikákon kérelmek fogadásához, és melyeket csak elsődleges replikára figyeljen is megadhat.

Például rendelkezhet egy ServiceRemotingListener, amely a távoli eljáráshívások csak az elsődleges replikára, és a egy második, egyéni figyelő, amely olvasási kérelmek, a másodlagos replikákon HTTP-n keresztül:

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
> Több kérésfigyelőt egy szolgáltatáshoz, minden egyes figyelő létrehozásakor **kell** egy egyedi nevet kell megadni.
>
>

Végül ismertetik a végpontok a szolgáltatás a szükséges a [Szolgáltatásjegyzék](service-fabric-application-and-service-manifests.md) a végpontok szakaszában.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

A kommunikációs figyelőjének hozzáférhet a végpont a lefoglalt erőforrások a `CodePackageActivationContext` a a `ServiceContext`. A figyelő el is indíthatja a kérések figyelését, ha meg van nyitva.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Végponti erőforrás a teljes csomag közös, és azok lefoglalásának Service Fabric által, ha a csomag aktiválva van. Több, ugyanazt a ServiceHost üzemeltetett szolgáltatás replika előfordulhat, hogy ossza meg ugyanazt a portot. Ez azt jelenti, hogy a kommunikáció figyelője támogatja-e-port megosztása. Ennek az az ajánlott módszer a figyelés címet állít elő a partíció és replika és példány azonosító használandó kommunikációs figyelőjének szól.
>
>

### <a name="service-address-registration"></a>Service cím regisztrálása
A rendszer-szolgáltatás neve a *elnevezési szolgáltatásban* fut a Service Fabric-fürtökön. Az elnevezési szolgáltatás nem a regisztráló services és a címek, amely minden példány vagy a replikát a szolgáltatás figyel. Ha a `OpenAsync(C#) / openAsync(Java)` metódusa egy `ICommunicationListener(C#) / CommunicationListener(Java)` befejeződött, a visszatérési érték a rendszer regisztrálja az elnevezési szolgáltatásban. A visszaadott érték, amely lekérdezi az elnevezési szolgáltatásban közzétett egy karakterláncot, amelynek az értéke bármi lehet minden. A karakterlánc értéke az ügyfelek látják, ha az elnevezési szolgáltatás a szolgáltatás cím megadását.

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

A Service Fabric biztosít az API-k, amelyek lehetővé teszik az ügyfelek és egyéb szolgáltatások szolgáltatásnév szerint ez a cím majd meg. Ez azért fontos, mert a szolgáltatás-cím nem statikus. Szolgáltatások helyezi át a fürt resource és a rendelkezésre állás céljából. Ez az a mechanizmust, amelyek lehetővé teszik az ügyfelek számára, hogy a szolgáltatás figyel-e címének feloldására.

> [!NOTE]
> Hogyan írható olyan kommunikációs figyelőjének a teljes lépésenkénti útmutatóért lásd: [OWIN önálló futtató Service Fabric webes API szolgáltatások](service-fabric-reliable-services-communication-webapi.md) a C#, mivel a Javához készült írhat saját HTTP-kiszolgáló megvalósítási EchoServer lásd: Példa: az alkalmazások https://github.com/Azure-Samples/service-fabric-java-getting-started.
>
>

## <a name="communicating-with-a-service"></a>A szolgáltatással való kommunikáció
A Reliable Services API-t biztosít az alábbi kódtárak szolgáltatások kommunikáló írni.

### <a name="service-endpoint-resolution"></a>Szolgáltatási végpont felbontás
A szolgáltatással való kommunikáció első lépése, hogy a partíció vagy szeretne beszélgetni a szolgáltatás példánya egy végpont címének feloldására. A `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` segédprogram osztály egy alapvető elemi egységei, amely segít az ügyfeleknek eldönteni, a futásidőben a szolgáltatások végpontja. A Service Fabric-terminológiában a folyamat, amely meghatározza, hogy a szolgáltatások végpontja nevezzük a *szolgáltatás-végponti feloldási*.

Fürtön belüli szolgáltatások csatlakozni ServicePartitionResolver hozható létre az alapértelmezett beállításokkal. Ez a legtöbb helyzetben a javasolt felhasználás:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Szeretne csatlakozni egy másik fürtben a szolgáltatások, egy ServicePartitionResolver létrehozhatók a fürt átjáró végpontok között. Vegye figyelembe, hogy átjáró végpontok csak különböző végpontok ugyanazon a fürtön való kapcsolódáshoz. Példa:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Másik lehetőségként `ServicePartitionResolver` kaphatnak a függvény létrehozásához egy `FabricClient` belső használatára:

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

`FabricClient` olyan objektum, amely a különböző műveletek a fürthöz a Service Fabric-fürt közötti kommunikációra szolgál. Ez akkor hasznos, ha azt szeretné, hogy jobban szabályozhatja, hogy egy szolgáltatás partíció feloldó hogyan működjön együtt a fürt. `FabricClient` belsőleg gyorsítótárazásának, és általában költséges a létrehozása, ezért fontos, hogy újra felhasználhatja `FabricClient` példányok, amennyire csak lehetséges.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Feloldás metódus szolgál majd a cím egy szolgáltatás vagy egy particionált Services szolgáltatás partíció beolvasása.

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

A szolgáltatás címének használatával könnyedén egy ServicePartitionResolver feloldható, de további munkahelyi azért szükséges, hogy a helyesen a feloldott cím is használható. Az ügyfélnek meg kell-e a csatlakozási kísérlet egy átmeneti hiba miatt sikertelen volt, és újra meg lehet próbálni észlelése (pl. szolgáltatás áthelyezték vagy átmenetileg nem érhető el), vagy állandó hiba (pl. szolgáltatás törölve lett vagy a kért erőforrás már nem létezik). Egy szolgáltatáspéldány vagy replikák áthelyezheti csomópontról csomópontra több okból bármikor. A szolgáltatás címének ServicePartitionResolver révén az Ügyfélkód megpróbál kapcsolódni a ideje elavulttá válhatnak. Ebben az esetben újra az ügyfélnek kell újra feloldani a címet. Így az előző `ResolvedServicePartition` azt jelzi, hogy kell-e a feloldó az újrapróbálkozáshoz ahelyett, hogy egyszerűen csak olvashatók be egy gyorsítótárazott címet.

Általában az Ügyfélkód kell nem működik a ServicePartitionResolver közvetlenül. Létrehozott, és átkerül az ügyfél factoryt kommunikáció a Reliable Services API-t. Az előállítók a feloldó belső célokra használja fel, hogy egy ügyfél-objektum, amely a kommunikáció a szolgáltatásokkal használható.

### <a name="communication-clients-and-factories"></a>Kommunikáció az ügyfelek és gyárak
A kommunikációs gyári könyvtár egy tipikus hibakezelési újrapróbálkozási minta, amely egyszerűbbé teszi a feloldott Szolgáltatásvégpontok újrapróbálkozás kapcsolatokat implementál. A gyári könyvtár az újrapróbálkozási mechanizmust biztosít, míg a, adja meg a hiba-kezelők.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` az alapszintű felületet egy kommunikációs ügyfélgyára, amely egy Service Fabric-szolgáltatást is kommunikálhatnak az ügyfelek által megvalósított határoz meg. A CommunicationClientFactory végrehajtása attól függ, hogy a kommunikációs verem a Service Fabric-szolgáltatás által használt, ahol az ügyfél kommunikálni kíván. A Reliable Services API-t biztosít olyan `CommunicationClientFactoryBase<TCommunicationClient>`. Ez egy alapszintű megvalósítás CommunicationClientFactory felületének biztosít, és a kommunikációs-t a gyakori feladatokat hajt végre. (Ezek a feladatok között, egy ServicePartitionResolver segítségével határozza meg a szolgáltatási végpont). Az ügyfelek általában az absztrakt CommunicationClientFactoryBase osztályt, amely kezeli a kommunikációs verem specifikus logika megvalósítása.

A kommunikáció ügyfél csak-címet kap, és használja a szolgáltatáshoz való csatlakozáshoz. Az ügyfél bármilyen szeretné protokollt használhat.

```csharp
public class MyCommunicationClient : ICommunicationClient
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

Az ügyfél-előállító elsősorban létrehozásáért felelős az ügyfelek kommunikációt. Azon ügyfeleknél, amelyek nem fenntartani egy állandó kapcsolatot, például egy HTTP-ügyfelet a gyári csak hozhat létre, és adja vissza az ügyfélnek szüksége van. Egyéb protokollok, amelyek karbantartása egy állandó kapcsolatot, például az egyes bináris protokollok kell is hitelesítenie kell meghatározni, hogy kell-e a kapcsolat újból létrehozza az előállító.  

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

Végül egy kivételkezelő felelős amely meghatározza, hogy milyen műveletet kell végrehajtani, ha kivétel lép fel. Kivételek kategóriákba **Újrapróbálkozást lehetővé tevő** és **Újrapróbálkozást lehetővé nem tevő**.

* **Újrapróbálkozást lehetővé nem tevő** kivételek egyszerűen get rethrown térjen vissza a hívónak.
* **Újrapróbálkozást lehetővé tevő** kivételek további kategóriákba **átmeneti** és **nem átmeneti**.
  * **Átmeneti** kivételek, amelyek egyszerűen újból próbálkozhat újra feloldani a szolgáltatási végpont címe nélkül. Ez magában foglalja az átmeneti hálózati hibák vagy a szolgáltatás hibaválaszok egyikéből, amelyek jelzik, hogy nem létezik a végpont-címét.
  * **A nem átmeneti** kivételek, amelyek szükség van a szolgáltatási végpont címre újra fel kell oldani. Ezek közé tartozik a kivételek, amelyek jelzik, hogy a szolgáltatási végpont nem érhető el, a szolgáltatás jelző át lett helyezve egy másik csomópont.

A `TryHandleException` meghozta a döntést, egy adott kivétel kapcsolatban. Ha azt **nem tudja** milyen döntést kell kapcsolatos kivétel kell visszaadnia **hamis**. Ha azt **tudja** milyen dönti el, kell ennek megfelelően állítsa be az eredmény, valamint vissza **igaz**.

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
### <a name="putting-it-all-together"></a>Végső összeállítás
Az egy `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, és `IExceptionHandler(C#) / ExceptionHandler(Java)` épülő projektszolgáltatásokat egy kommunikációs protokollt egy `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` burkolja, minden egy helyen, és a hibakezelési és service partíció cím feloldási hurok körül ezeket az összetevőket tartalmaz.

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

## <a name="next-steps"></a>További lépések
* [Az ASP.NET Core Reliable Services szolgáltatással](service-fabric-reliable-services-communication-aspnetcore.md)
* [A Reliable Services-táveléréssel kezdeményezett távoli eljáráshívások](service-fabric-reliable-services-communication-remoting.md)
* [WCF-kommunikáció a Reliable Services használatával](service-fabric-reliable-services-communication-wcf.md)

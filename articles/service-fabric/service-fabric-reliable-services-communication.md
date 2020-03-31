---
title: Megbízható szolgáltatások kommunikáció – áttekintés
description: A Reliable Services kommunikációs modell áttekintése, beleértve a figyelők megnyitását a szolgáltatásokon, a végpontok feloldását és a szolgáltatások közötti kommunikációt.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 3c1a6cfa5227369bf1cde4af087019727c22c0c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75462957"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>A Reliable Services kommunikációs API-k használata
Az Azure Service Fabric platformként teljesen független a szolgáltatások közötti kommunikációról. Az UDP-től http-ig minden protokoll és halm elfogadható. A szolgáltatásfejlesztőnek kell eldöntenie, hogy a szolgáltatások hogyan kommunikáljanak. A Reliable Services alkalmazáskeretrendszer beépített kommunikációs halmokat és API-kat biztosít, amelyek segítségével egyéni kommunikációs összetevőket hozhat létre.

## <a name="set-up-service-communication"></a>Szolgáltatáskommunikáció beállítása
A Reliable Services API egy egyszerű felületet használ a szolgáltatás kommunikációhoz. A szolgáltatás végpontjának megnyitásához egyszerűen valósítsa meg ezt a felületet:

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

Ezután hozzáadhatja a kommunikációs figyelő implementációját egy szolgáltatásalapú osztálymetódus felülbírálása.

Állapotnélküli szolgáltatások esetén:

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

Állapotalapú szolgáltatások esetén:

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

Mindkét esetben a hallgatók gyűjteményét adja vissza. Ez lehetővé teszi, hogy a szolgáltatás több végponton, potenciálisan különböző protokollok használatával, több figyelő használatával figyelheti. Például előfordulhat, hogy egy HTTP-figyelő és egy külön WebSocket-figyelő. Minden figyelő kap egy nevet, és az eredményül kapott *névgyűjtemény: címpárok* JSON-objektumként jelennek meg, amikor egy ügyfél egy szolgáltatáspéldány vagy egy partíció figyelőcímét kéri.

Egy állapotmentes szolgáltatás, a felülbírálási serviceinstancelisteners gyűjteményét adja vissza. A `ServiceInstanceListener` tartalmaz egy függvényt, hogy hozzon létre egy, `ICommunicationListener(C#) / CommunicationListener(Java)` és ad neki egy nevet. Állapotalapú szolgáltatások esetén a felülbírálás a ServiceReplicaListeners gyűjteményét adja vissza. Ez némileg eltér az állapotnélküli `ServiceReplicaListener` megfelelőjétől, mert `ICommunicationListener` a másodlagos replikákon is megnyithatja a másodlagos replikákat. Nem csak több kommunikációs figyelők egy szolgáltatásban, de azt is megadhatja, hogy mely figyelők fogadja el a másodlagos replikák, és melyek csak az elsődleges replikák.

Például rendelkezhet egy ServiceRemotingListener, amely rPC-hívásokat csak az elsődleges replikák, és egy második, egyéni figyelő, amely olvasási kérelmeket a másodlagos replikák HTTP-n keresztül:

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
> Ha egy szolgáltatáshoz több figyelőt hoz létre, minden figyelőnek egyedi nevet **kell** adni.
>
>

Végül írja le a végpontok, amelyek szükségesek a szolgáltatás [jegyzékfájla](service-fabric-application-and-service-manifests.md) a szakasz ban a végpontok.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

A kommunikációs figyelő a rendszerből érheti el `CodePackageActivationContext` a `ServiceContext`végponti erőforrásokat. A figyelő ezután megkezdheti a kérelmek figyelése, amikor megnyílik.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> A végponterőforrásai közösek a teljes szolgáltatáscsomagra, és a Service Fabric a szolgáltatáscsomag aktiválásakor levan foglalva. Az ugyanazon serviceHost-on üzemeltetett több szolgáltatásreplikák is megoszthatják ugyanazt a portot. Ez azt jelenti, hogy a kommunikációs figyelőnek támogatnia kell a portmegosztást. Ennek ajánlott módja az, hogy a kommunikációs figyelő a partícióazonosítót és a replika/példányazonosítót használja a figyelési cím létrehozásakor.
>
>

### <a name="service-address-registration"></a>Szolgáltatáscím regisztrálása
Az *elnevezési szolgáltatás* nevű rendszerszolgáltatás a Service Fabric-fürtökön fut. Az elnevezési szolgáltatás a szolgáltatások és azok címei regisztrálója, amelyet a szolgáltatás minden példánya vagy replikája figyel. Amikor `OpenAsync(C#) / openAsync(Java)` egy `ICommunicationListener(C#) / CommunicationListener(Java)` metódus befejeződik, a visszatérési érték regisztrálva lesz az elnevezési szolgáltatásban. Ez a visszatérési érték, amely megjelenik az elnevezési szolgáltatás egy karakterlánc, amelynek értéke bármi lehet. Ez a karakterlánc-érték az, amit az ügyfelek látnak, amikor az elnevezési szolgáltatás címét kérik.

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

A Service Fabric olyan API-kat biztosít, amelyek lehetővé teszik az ügyfelek és más szolgáltatások számára, hogy ezt a címet szolgáltatásnév szerint kérjék. Ez azért fontos, mert a szolgáltatás címe nem statikus. A szolgáltatások erőforrás-kiegyensúlyozási és rendelkezésre állási célokból kerülnek áthelyezésre a fürtben. Ez az a mechanizmus, amely lehetővé teszi az ügyfelek számára a szolgáltatás figyelési címének feloldását.

> [!NOTE]
> A kommunikációs figyelő írásának teljes útmutatóját a [Service Fabric Web API-szolgáltatások OWIN-nal a](service-fabric-reliable-services-communication-webapi.md) C# számára című témakörben talál, míg a https://github.com/Azure-Samples/service-fabric-java-getting-startedJava esetében saját HTTP-kiszolgálóimplementációt írhat, lásd: EchoServer alkalmazás példa a webhelyen.
>
>

## <a name="communicating-with-a-service"></a>Kommunikáció egy szolgáltatással
A Reliable Services API a következő függvénytárakat biztosítja a szolgáltatásokkal kommunikáló ügyfelek írásához.

### <a name="service-endpoint-resolution"></a>Szolgáltatásvégpont feloldása
A szolgáltatással való kommunikáció első lépése a kapcsolatonként beszélni kívánt szolgáltatás partíciójának vagy példányának végpontcímének feloldása. A `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` segédprogram-osztály egy alapvető primitív, amely segít az ügyfeleknek meghatározni a szolgáltatás végpontját futásidőben. A Service Fabric terminológiájában a szolgáltatás végpontjának meghatározásának folyamatát *szolgáltatásvégpont-feloldásnak nevezzük.*

A fürtön belüli szolgáltatásokhoz való csatlakozáshoz a ServicePartitionResolver az alapértelmezett beállításokkal hozható létre. Ez az ajánlott használat a legtöbb esetben:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Egy másik fürt szolgáltatásaihoz való csatlakozáshoz egy ServicePartitionResolver fürtátjáró-végpontok készletével hozható létre. Vegye figyelembe, hogy az átjáró végpontjai csak különböző végpontok, amelyek ugyanahhoz a fürthöz csatlakoznak. Példa:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternatív `ServicePartitionResolver` megoldásként, lehet adni egy `FabricClient` funkciót, hogy hozzon létre egy a használni belsőleg:

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

`FabricClient`az az objektum, amely a Fürt különböző felügyeleti műveleteihez a Service Fabric-fürttel való kommunikációra szolgál. Ez akkor hasznos, ha szeretné, hogy a szolgáltatás partíció feloldó a fürtöt. `FabricClient`belső gyorsítótárazást hajt végre, és általában költséges a létrehozás, ezért fontos, hogy a példányokat a lehető legnagyobb mértékben újra felhasználja. `FabricClient`

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

A feloldási módszer ezután egy szolgáltatás vagy egy szolgáltatáspartíció címének lekéréséhez használatos a particionált szolgáltatások hoz.

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

A szolgáltatáscím könnyen feloldható egy ServicePartitionResolver használatával, de további munkára van szükség a feloldott cím helyes használatának biztosításához. Az ügyfélnek fel kell derítenie, hogy a csatlakozási kísérlet átmeneti hiba miatt sikertelen volt-e, és újra próbálkozható-e (pl. a szolgáltatás áthelyezve vagy átmenetileg nem érhető el), vagy állandó hiba (például a szolgáltatás törölve lett, vagy a kért erőforrás már nem létezik). A szolgáltatáspéldányok vagy -replikák több okból bármikor átköltözhetnek csomópontról csomópontra. A ServicePartitionResolver-en keresztül feloldott szolgáltatáscím elavult lehet, mire az ügyfélkód megpróbál csatlakozni. Ebben az esetben az ügyfélnek újra fel kell oldania a címet. Az előző `ResolvedServicePartition` megadása azt jelzi, hogy a feloldónak újra kell próbálkoznia, nem pedig egyszerűen beolvasnia egy gyorsítótárazott címet.

Az ügyfélkódnak általában nem kell közvetlenül működnie a ServicePartitionResolver-rel. Jön létre, és továbbítja a kommunikációs ügyfél gyárak a Reliable Services API-ban. A gyárak a feloldó belső használatával hoznak létre egy ügyfélobjektumot, amely a szolgáltatásokkal való kommunikációhoz használható.

### <a name="communication-clients-and-factories"></a>Kommunikációs ügyfelek és gyárak
A kommunikációs üzemi könyvtár egy tipikus hibakezelési újrapróbálkozási mintát valósít meg, amely megkönnyíti a kapcsolatok megoldását a feloldott szolgáltatásvégpontokhoz. A gyári könyvtár biztosítja az újrapróbálkozási mechanizmust, miközben biztosítja a hibakezelőket.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`meghatározza a kommunikációs ügyfélgyár által megvalósított alapfelületet, amely olyan ügyfeleket hoz létre, amelyek egy Service Fabric-szolgáltatással tudnak beszélni. A CommunicationClientFactory megvalósítása a Service Fabric szolgáltatás által használt kommunikációs veremtől függ, ahol az ügyfél kommunikálni szeretne. A Reliable Services `CommunicationClientFactoryBase<TCommunicationClient>`API biztosít egy . Ez biztosítja a CommunicationClientFactory felület alapimplementációját, és olyan feladatokat hajt végre, amelyek az összes kommunikációs halmban közösek. (Ezek a feladatok közé tartozik a ServicePartitionResolver használata a szolgáltatás végpontjának meghatározásához). Az ügyfelek általában az absztrakt CommunicationClientFactoryBase osztályt valósítják meg a kommunikációs veremre jellemző logika kezelésére.

A kommunikációs ügyfél csak kap egy címet, és használja azt, hogy csatlakozzon egy szolgáltatáshoz. Az ügyfél bármilyen protokollt használhat, amit csak akar.

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

Az ügyfélgyár elsősorban a kommunikációs ügyfelek létrehozásáért felelős. Az ügyfelek, amelyek nem tartanak fenn állandó kapcsolatot, például egy HTTP-ügyfél, a gyár csak létre kell hoznia, és adja vissza az ügyfél. Az állandó kapcsolatot fenntartó egyéb protokollokat, például egyes bináris protokollokat a gyárnak is ellenőriznie kell annak megállapításához, hogy a kapcsolatot újra létre kell-e hozni.  

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

Végül a kivételkezelő felelős annak meghatározásáért, hogy milyen műveletet kell végrehajtani, ha kivétel történik. A kivételek **újrapróbálhatóak** és **nem újrapróbálhatók.**

* **A nem újrapróbálható** kivételek egyszerűen visszakerülnek a hívóra.
* **az újrapróbálható** kivételek tovább vannak besorolva **átmeneti** és **nem átmeneti kategóriákba.**
  * **Átmeneti** kivételek azok, amelyek egyszerűen újra megkísérelhető a szolgáltatás végpontcímének újbóli feloldása nélkül. Ezek közé tartoznak az átmeneti hálózati problémák vagy a szolgáltatáshiba-válaszok, amelyek nem léteznek a szolgáltatás végpontcímén.
  * **A nem átmeneti** kivételek azok, amelyek a szolgáltatás végpontcímének újbóli feloldódását igénylik. Ezek közé tartoznak a kivételek, amelyek azt jelzik, hogy a szolgáltatás végpontja nem érhető el, jelezve, hogy a szolgáltatás egy másik csomópontra költözött.

Az `TryHandleException` döntést hoz egy adott kivételről. Ha **nem tudja,** milyen döntéseket kell hoznia egy kivételről, akkor **hamis**at kell visszaadnia. Ha ez **nem tudja,** milyen döntést kell hoznia, meg kell határoznia az eredményt ennek megfelelően, és vissza **igaz**.

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
A `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`, `IExceptionHandler(C#) / ExceptionHandler(Java)` és épül a `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` kommunikációs protokoll, a pakolások az egészet együtt, és biztosítja a hibakezelési és szolgáltatás partíció címfeloldási hurok körül ezeket az összetevőket.

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
* [ASP.NET Core megbízható szolgáltatásokkal](service-fabric-reliable-services-communication-aspnetcore.md)
* [Távoli eljáráshívások a Megbízható szolgáltatások távoli hívásával](service-fabric-reliable-services-communication-remoting.md)
* [WCF-kommunikáció a Megbízható szolgáltatások használatával](service-fabric-reliable-services-communication-wcf.md)

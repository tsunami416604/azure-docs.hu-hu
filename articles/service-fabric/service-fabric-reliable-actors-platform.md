---
title: A Service Fabric a Reliable Actors |} Microsoft Docs
description: "Útmutatás a Reliable Actors vannak réteges a Reliable Services és a Service Fabric-platformról szolgáltatásának használatához."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: 43b3f758fe7017c0ec949ba6e28b76438cf1bc13
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Így használja a Reliable Actors a Service Fabric platformot
Ez a cikk bemutatja, hogy Reliable Actors működik-e az Azure Service Fabric-platformon. Futtassa a keretrendszer, amely a egy állapotalapú szolgáltatás megvalósítását a Reliable Actors hívása a *szereplő szolgáltatás*. Az aktor szolgáltatás összes életciklusa és a a szereplőket terjesztéséhez üzenet kezeléséhez szükséges összetevőket tartalmazza:

* Az Aktor futásidejű életciklusát, szemétgyűjtés, felügyeli, és egyszálas vezérlésről.
* Egy szereplő szolgáltatás távoli eljáráshívási figyelő távelérési szereplőkkel hívásainak fogad, és elküldi azokat a kézbesítő útvonalat a megfelelő szereplő példány.
* Az Aktor Állapotszolgáltató becsomagolja állapotszolgáltatója (például a megbízható gyűjtemények állapotszolgáltató), és egy adapter biztosít szereplő állapotkezelés.

Ezek az összetevők együttesen alkotják a megbízható szereplő keretrendszer.

## <a name="service-layering"></a>A réteges szolgáltatás
Mert maga szereplő szolgáltatás egy megbízható szolgáltatás összes a [alkalmazásmodell](service-fabric-application-model.md), életciklusát, [csomagolási](service-fabric-package-apps.md), [telepítési](service-fabric-deploy-remove-applications.md), frissítési és méretezési Reliable Services elveit aktorszolgáltatások ugyanúgy alkalmazni.

![Aktor szolgáltatás réteges][1]

A fenti ábrán a Service Fabric-alkalmazás-keretrendszerbeli és a felhasználói kód közötti kapcsolatot mutatja be. Kék elemek határoz meg a Reliable Services alkalmazás-keretrendszer, narancssárga megbízható szereplő keretében, és zöld jelenti felhasználói kód.

A Reliable Services, a szolgáltatás örökli a `StatefulService` osztály. Ez az osztály maga a származó `StatefulServiceBase` (vagy `StatelessService` állapotmentes szolgáltatások). A Reliable Actors az aktor szolgáltatását használja. Az aktor szolgáltatás egy másik megvalósításában a `StatefulServiceBase` a szereplője futtatják szereplő mintát megvalósító osztály. Mivel a szereplő szolgáltatás megvalósítása csak `StatefulServiceBase`, saját szolgáltatás abból származó írhat `ActorService` és valósít meg a szolgáltatásszint-funkciókat az azonos módon történő örökléskor `StatefulService`, például:

* Szolgáltatás biztonsági mentése és visszaállítása.
* Összes szereplő, például egy áramköri megszakító megosztás funkciót.
* Távoli eljáráshívások a szereplő maga és a minden egyes szereplő.

> [!NOTE]
> Állapotalapú szolgáltatások jelenleg nem támogatottak a Java/Linux.

### <a name="using-the-actor-service"></a>Az aktor szolgáltatással
Aktor célpéldánynál a szereplő szolgáltatás, amelyben futnak. Az aktor szolgáltatáson keresztül szereplő példányok programozott módon szerezheti be a service-környezetben. A service-környezet magában hordozza a Partícióazonosító, szolgáltatás neve, az alkalmazásnév és más Service Fabric platform-specifikus adatait.:

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```


Az összes megbízható szolgáltatások, például a aktor szolgáltatás a Service Fabric futásidejű szolgáltatás típusú szerepelnie kell. Az aktor szolgáltatás a szereplő példányán fusson az aktor típusát is regisztrálni kell az aktor szolgáltatással. Az aktorok esetében ezt a feladatot az `ActorRuntime` regisztrációs metódus végzi el. A legegyszerűbb esetben csak regisztrálhatja az aktor típusát, és az alapértelmezett beállításokkal szereplő szolgáltatás implicit módon használható:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Azt is megteheti használhatja a regisztrációs metódus által biztosított lambda saját kezűleg a szereplő szolgáltatás létrehozásához. Ezután konfigurálhatja a szereplő szolgáltatást, és explicit módon összeállítani a szereplő példányokat, ahol az függőségek beszúrása az aktor saját konstruktoraikban keresztül:

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

### <a name="actor-service-methods"></a>Aktor metódusok
Az Aktor szolgáltatás megvalósítja `IActorService` (C#) vagy `ActorService` (Java), amely viszont megvalósítja `IService` (C#) vagy `Service` (Java). Ez az a Reliable Services távoli eljáráshívási, amely lehetővé teszi a távoli eljáráshívások metódusok által használt felület. Amely a szolgáltatás távoli eljáráshívás keresztül távolról hívható szolgáltatásiszint-metódusokat tartalmaz.

#### <a name="enumerating-actors"></a>Szereplője számbavétele
Az aktor szolgáltatás lehetővé teszi, hogy egy ügyfél a szereplője, amelyen a szolgáltatás metaadatainak számbavétele. Mivel a szereplő szolgáltatás egy particionált állapotalapú szolgáltatásból, a számbavételi partíciónként történik. Mindegyik partíció sok szereplője tartalmazhat, mert a számbavétel adja vissza a rendszer lapozható eredmények készlete. A lapok visszacsatolódnak keresztül, amíg az összes olvassa el. A következő példa bemutatja, hogyan szereplő szolgáltatási egy partíció összes aktív szereplő álló lista létrehozása:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```

#### <a name="deleting-actors"></a>Szereplője törlése
Az aktor szolgáltatás is biztosít egy olyan függvényt szereplője törléséhez:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Szereplője és állapotukra törléséről további információkért lásd: a [szereplő életciklus dokumentáció](service-fabric-reliable-actors-lifecycle.md).

### <a name="custom-actor-service"></a>Egyéni szereplő szolgáltatás
Az aktor regisztrációs lambda használatával regisztrálhatja a saját egyéni szereplő szolgáltatás abból származó `ActorService` (C#) és `FabricActorService` (Java). A egyéni szereplő szolgáltatásban valósíthatja meg a saját szolgáltatásiszint-funkció szolgáltatás osztály írása `ActorService` (C#) vagy `FabricActorService` (Java). Egy egyéni szereplő szolgáltatás örökli az összes szereplő futásidejű funkciót `ActorService` (C#) vagy `FabricActorService` (Java) és a saját metódusok végrehajtásához használható.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

#### <a name="implementing-actor-backup-and-restore"></a>Végrehajtási szereplő biztonsági mentése és visszaállítása
 A következő példában az egyéni szereplő szolgáltatás adatok biztonsági mentésének szereplő már szerepel a távoli eljáráshívás-figyelő kihasználásával metódus közzététele `ActorService`:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```


Ebben a példában `IMyActorService` , amely távoli eljáráshívási szerződés `IService` (C#) és `Service` (Java), majd valósítható `MyActorService`. A távoli eljáráshívás szerződés metódusok hozzáadásával `IMyActorService` most is elérhetők ügyfél keresztül egy távoli eljáráshívás proxy létrehozásával `ActorServiceProxy`:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

## <a name="application-model"></a>Alkalmazásmodellt.
Aktorszolgáltatások Reliable Services, így az alkalmazásmodell megegyezik. Azonban a szereplő keretrendszer build tools készítése alkalmazás modell fájlok meg.

### <a name="service-manifest"></a>Szolgáltatás jegyzék
Az aktor framework összeállítási eszközök automatikus létrehozása a szereplő szolgáltatás ServiceManifest.xml fájl tartalmát. Ez a fájl tartalmazza:

* Aktor szolgáltatás típusa. A következő típusnév jön létre, a aktor projekt neve alapján. Az aktor az adatmegőrzési attribútumok alapján, a HasPersistedState jelző nincs beállítva megfelelően.
* A kódcsomag.
* A konfigurációs csomag.
* Erőforrások és a végpontok.

### <a name="application-manifest"></a>Az alkalmazásjegyzék
Az aktor framework összeállítási eszközök automatikus létrehozása az aktor szolgáltatás alapértelmezett szolgáltatásdefiníció. A build tools feltöltése az alapértelmezett szolgáltatás tulajdonságai:

* Az adatmegőrzési attribútumot a szereplő replikaszám beállítása határozza meg. Minden alkalommal, amikor az adatmegőrzési attribútumot a szereplő módosul, a replika készlet alapértelmezett szolgáltatásdefinícióban számolás ennek megfelelően.
* Partícióséma és a tartomány a teljes Int64-címtartománnyal rendelkező egységes Int64 beállítása.

## <a name="service-fabric-partition-concepts-for-actors"></a>A Service Fabric partícióazonosító fogalmak actors
Aktorszolgáltatások a particionált állapotalapú szolgáltatások. Mindegyik partíció szereplő szolgáltatási szereplője tartalmaz. Partíciók a rendszer automatikusan terjeszt a Service Fabric több csomópont feladatait. Ennek eredményeképpen szereplő példányok terjesztése.

![Aktor particionálás és terjesztési][5]

Megbízható szolgáltatások különböző partíciós séma és a partíció kulcstartományokkal hozhatja létre. Az aktor szolgáltatás Int64 particionálási sémát a teljes Int64-címtartománnyal rendelkező szereplője van leképezve partíciók használja.

### <a name="actor-id"></a>Aktor azonosítója
Minden, amely jön létre a szolgáltatásban szereplő van társítva, által képviselt egyedi azonosítója a `ActorId` osztály. `ActorId`érték nem átlátszó azonosítója, amely alkalmas szereplője egyenletes elosztása a partíciók közötti véletlenszerű azonosítók létrehozásával:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Minden `ActorId` Int64 kivonatolja. Ezért a szereplő szolgáltatás a teljes Int64-tartomány egy Int64 particionálási sémát kell használnia. Azonban használható egyéni értéket egy `ActorID`, beleértve a GUID/UUID-k, karakterláncok és Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

GUID azonosítók/UUID-k és karakterláncok használata, ha az értékek Int64 számára van kivonatolva. Azonban ha feltétlenül van explicit módon adja meg a Int64 egy `ActorId`, a Int64 felelteti meg közvetlenül egy partíció további kivonatoláshoz nélkül. Ez a módszer, mely partíció a szereplője kerülnek vezérlőre is használhatja.

## <a name="actor-using-remoting-v2-stack"></a>Aktor távoli eljáráshívás V2 verem használata
2.8 nuget-csomagot, a felhasználók már a távoli eljáráshívás V2 verem, további performant és szolgáltatások, mint az egyedi szerializálás biztosít. Távoli eljáráshívás V2 nincs visszamenőlegesen kompatibilis a meglévő távelérési verem (hívjuk most azt V1 távelérése verem szerint).

A távoli eljáráshívás V2 verem használható következő változtatásokra van szükség.
 1. Adja hozzá a következő szerelvény attribútumot szereplő kapcsolaton.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
   ```

 2. Buildelés és frissítési ActorService és szereplő ügyfél projektek V2 verem elindítására.

### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Aktor szolgáltatás frissítése távoli eljáráshívási V2 verem szolgáltatás rendelkezésre állása befolyásolása nélkül.
Ez a módosítás felül lesz a 2. lépés frissítés. Kövesse a lépéseket, ugyanabban a sorrendben, megadottaknak megfelelően.

1.  Adja hozzá a következő szerelvény attribútumot szereplő kapcsolaton. Ez az attribútum indul két figyelők az ActorService, 1-es verzió (meglévő) és V2-figyelő. Ez a változás a ActorService frissítés.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.CompatListener,RemotingClient = RemotingClient.V2Client)]
  ```

2. A fenti frissítés befejezése után ActorClients frissítése
Ez a lépés biztosítja, hogy szereplő Proxy távoli eljáráshívási V2 verem használ.

3. Ez a lépés nem kötelező megadni. A fenti attribútumát V1 figyelő eltávolítása.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>Következő lépések
* [Aktor állapotkezelés](service-fabric-reliable-actors-state-management.md)
* [Aktor életciklusának és szemétgyűjtési gyűjtése](service-fabric-reliable-actors-lifecycle.md)
* [Actors API referenciadokumentációt tartalmaz](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-példakód](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

---
title: Azure Service Fabric szereplője funkciók végrehajtására |} Microsoft Docs
description: Ismerteti, hogyan lehet írni a saját szereplő implementáló szolgáltatásszintű szolgáltatások StatefulService örökléskor azonos módon.
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
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 60989825ecdefa853d0e2df99619e3cb350cb6bc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Az aktor szolgáltatásban végrehajtási szolgáltatásiszint-funkciók
A [szolgáltatás réteges](service-fabric-reliable-actors-platform.md#service-layering), maga a szereplő szolgáltatás egy olyan megbízható szolgáltatás.  A saját szolgáltatás abból származó írhat `ActorService` és valósít meg szolgáltatási szint funkciókat a módon történő örökléskor StatefulService, például:

- Szolgáltatás biztonsági mentése és visszaállítása.
- Összes szereplő, például egy áramköri megszakító megosztás funkciót.
- Távoli eljáráshívások a szereplő maga és a minden egyes szereplő.

## <a name="using-the-actor-service"></a>Az aktor szolgáltatással
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

## <a name="actor-service-methods"></a>Aktor metódusok
Az Aktor szolgáltatás megvalósítja `IActorService` (C#) vagy `ActorService` (Java), amely viszont megvalósítja `IService` (C#) vagy `Service` (Java). Ez az a Reliable Services távoli eljáráshívási, amely lehetővé teszi a távoli eljáráshívások metódusok által használt felület. Szolgáltatás távoli eljáráshívás keresztül távolról hívható, és lehetővé teszik használt szolgáltatásiszint-metódusokat tartalmaz [számbavétele](service-fabric-reliable-actors-enumerate.md) és [törlése](service-fabric-reliable-actors-delete-actors.md) szereplőkkel.


## <a name="custom-actor-service"></a>Egyéni szereplő szolgáltatás
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

## <a name="implementing-actor-backup-and-restore"></a>Végrehajtási szereplő biztonsági mentése és visszaállítása
Egy egyéni szereplő szolgáltatás is elérhetővé teheti az adatok biztonsági mentésének szereplő már szerepel a távoli eljáráshívás-figyelő kihasználásával metódus `ActorService`.  Egy vonatkozó példáért lásd: [biztonsági mentési és visszaállítási szereplője](service-fabric-reliable-actors-backup-and-restore.md).

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

3. Ez a lépés nem kötelező. A fenti attribútumát V1 figyelő eltávolítása.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListener = RemotingListener.V2Listener,RemotingClient = RemotingClient.V2Client)]
    ```

## <a name="next-steps"></a>További lépések
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

---
title: Funkciók megvalósítása az Azure Service Fabric szereplőiben
description: Leírja, hogyan kell írni a saját aktor szolgáltatás, amely megvalósítja a szolgáltatásszintű szolgáltatások ugyanúgy, mint amikor örökli StatefulService.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 55ee4c7498dcda3060d4e4221711793b80132bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502287"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Szolgáltatásszintű szolgáltatások megvalósítása az aktorszolgáltatásban

Aszolgáltatás [rétegezése,](service-fabric-reliable-actors-platform.md#service-layering)az aktor szolgáltatás maga egy megbízható szolgáltatás. Írhat saját szolgáltatást, amely származik `ActorService`. A szolgáltatásszintű szolgáltatásokat ugyanúgy valósíthatja meg, mint amikor egy állapotalapú szolgáltatást örököl, például:

- Szolgáltatás biztonsági mentése és visszaállítása.
- Megosztott funkciók az összes szereplő, például egy megszakító.
- A távoli eljárás magához az aktorszolgáltatáshoz és minden egyes szereplőhez szólítja meg.

## <a name="use-the-actor-service"></a>Az aktor szolgáltatás használata

Az aktorpéldányok hozzáférhetnek ahhoz az aktorszolgáltatáshoz, amelyben futnak. Az aktor szolgáltatáson keresztül az aktor példányok programozott módon szerezhetik be a szolgáltatási környezetet. A szolgáltatás környezetében a partíció azonosítója, a szolgáltatás neve, az alkalmazás neve és más Azure Service Fabric platformspecifikus információkat tartalmaz.

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

Mint minden megbízható szolgáltatások, az aktor szolgáltatás regisztrálva kell lennie egy szolgáltatástípussal a Service Fabric futásidejű. Az aktorszolgáltatás az aktorpéldányok futtatásához az aktor típusis regisztrálnia kell az aktor szolgáltatás. Az aktorok esetében ezt a feladatot az `ActorRuntime` regisztrációs metódus végzi el. A legegyszerűbb esetben regisztrálhatja az aktor típusát, és az aktor szolgáltatás ezután az alapértelmezett beállításokat használja.

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

Másik lehetőségként használhatja a lambda a regisztrációs módszer által biztosított az aktor szolgáltatás saját kezűlétrehozásához. Ezután konfigurálhatja az aktor szolgáltatást, és explicit módon hozhat létre az aktorpéldányok. Függőségeket adhat be az aktornak a konstruktoron keresztül.

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

## <a name="actor-service-methods"></a>Aktor szolgáltatás metódusai

Az aktor szolgáltatás megvalósítja `IActorService` (C#) vagy `IService` `Service` `ActorService` (Java), amely viszont végrehajtja (C#) vagy (Java). Ezt az összeköttetést a Reliable Services távoli eljáráshívása teszi lehetővé a szolgáltatásmetódusok on-át. Olyan szolgáltatásszintű metódusokat tartalmaz, amelyek távolról hívhatók a szolgáltatás távoli átirányításán keresztül. Használhatja a szereplők [számbavételére](service-fabric-reliable-actors-enumerate.md) és [törlésére.](service-fabric-reliable-actors-delete-actors.md)


## <a name="custom-actor-service"></a>Egyéni aktor szolgáltatás

Az aktor regisztráció lambda használatával regisztrálhatja saját egyéni `ActorService` aktor `FabricActorService` szolgáltatás, amely származik (C#) és (Java). Ezután megvalósíthatja a saját szolgáltatásszintű funkciókat egy olyan `ActorService` szolgáltatásosztály írásával, amely örökli (C#) vagy `FabricActorService` (Java). Az egyéni aktor szolgáltatás örökli `ActorService` az összes aktor futásidejű funkció (C#) vagy `FabricActorService` (Java). Ezt fel lehet használni a saját szolgáltatási módszerek megvalósításához.

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

## <a name="implement-actor-backup-and-restore"></a>Aktor biztonsági mentésének és visszaállításának megvalósítása

Az egyéni aktor szolgáltatás elérhetővé teheti az aktoradatok biztonsági biztonsági `ActorService`kapcsolatának módját a már jelen lévő, a kapcsolatára már használt figyelő kihasználásával. Például lásd: [A szereplők biztonsági mentése és visszaállítása.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>A terhörgő V2 (felülettel kompatibilis) veremhasználatával rendelkező aktor

A remoting V2 (felület kompatibilis, más néven V2_1) verem rendelkezik a V2-es áthágás verem összes funkciójával. A felület kompatibilis a mutatóval ellátott V1 verem, de nem visszafelé kompatibilis a V2 és V1. Ha V1-ről V2_1 vált, és nincs hatással a szolgáltatás rendelkezésre állására, kövesse a következő szakaszlépéseit.

A remoting V2_1 verem használatához a következő módosítások szükségesek:

1. Adja hozzá a következő összeállítási attribútumot az aktorfelületekhez.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Av2-verem használatának megkezdéséhez hozzon létre és frissítse az aktor szolgáltatás- és aktorügyfél-projekteket.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Aszolgáltatás aktorja a V2 (interface compatible) veremre anélkül, hogy ez befolyásolná a szolgáltatás rendelkezésre állását

Ez a módosítás kétlépésből álló frissítés. Kövesse a lépéseket ebben a sorrendben.

1. Adja hozzá a következő összeállítási attribútumot az aktorfelületekhez. Ez az attribútum elindítja az aktor szolgáltatás két figyelője, V1 (meglévő) és a V2_1 figyelő. Frissítse az aktor szolgáltatást ezzel a módosítással.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Frissítse az aktor ügyfelek befejezése után az előző frissítés.
   Ez a lépés biztosítja, hogy az aktor proxy a a verem a mot V2_1ing.

3. Ez a lépés nem kötelező. Módosítsa az előző attribútumot a V1-figyelő eltávolításához.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>A remoting V2 veremben lévő aktor

A 2.8-as verziójú NuGet csomaggal a felhasználók most már használhatják a remoting V2 vermet, amely jobban teljesít, és olyan funkciókat biztosít, mint az egyéni szerializálás. A v2-es mutatóval való kapcsolatvisszanem kompatibilis a meglévő átirányító veremmel (amelyet ma V1-es átirányító veremnek neveznek).

A következő módosítások szükségesek a moting V2 verem használatához.

1. Adja hozzá a következő összeállítási attribútumot az aktorfelületekhez.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Az aktor szolgáltatás és az aktor ügyfélprojektek létrehozása és frissítése a V2-verem használatának megkezdéséhez.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Frissítse az aktor szolgáltatást a szolgáltatás szolgáltatás rendelkezésre állásának befolyásolása nélkül a szolgáltatás rendelkezésre állása nélkül.

Ez a módosítás kétlépésből álló frissítés. Kövesse a lépéseket ebben a sorrendben.

1. Adja hozzá a következő összeállítási attribútumot az aktorfelületekhez. Ez az attribútum elindítja az aktor szolgáltatás két figyelője, V1 (meglévő) és a V2 figyelő. Frissítse az aktor szolgáltatást ezzel a módosítással.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Frissítse az aktor ügyfelek befejezése után az előző frissítés.
   Ez a lépés biztosítja, hogy az aktor proxy a moting V2 verem.

3. Ez a lépés nem kötelező. Módosítsa az előző attribútumot a V1-figyelő eltávolításához.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>További lépések

* [Szereplő állapotkezelése](service-fabric-reliable-actors-state-management.md)
* [A szereplő életciklusa és a szemétgyűjtés](service-fabric-reliable-actors-lifecycle.md)
* [Actors API referenciadokumentáció](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

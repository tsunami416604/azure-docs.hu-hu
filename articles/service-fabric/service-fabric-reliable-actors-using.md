---
title: Az Azure Service Fabric actors valósít meg olyan funkciókat |} A Microsoft Docs
description: Ismerteti, hogyan írhat saját aktorszolgáltatás, ugyanúgy, mint a szolgáltatásiszint-funkciókat tartalmazza, amikor öröklik a statefulservice-ből.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 57894770ad9d27430d5803c9a93ce6973355878a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662975"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Szolgáltatásszintű funkciók megvalósítása az aktor service-ben

Leírtak szerint [rétegezett szolgáltatás](service-fabric-reliable-actors-platform.md#service-layering), az aktorszolgáltatás magát egy olyan megbízható szolgáltatás. Írhat saját szolgáltatás származó `ActorService`. Is meg lehet valósítani szolgáltatásszintű funkciók ugyanúgy, mint amikor az állapotalapú szolgáltatások, például öröklése:

- Szolgáltatás biztonsági mentése és visszaállítása.
- Megosztott funkció az összes szereplő, például egy áramkör-megszakító.
- Távoli eljáráshívások az aktorszolgáltatás magát és minden egyes színész.

## <a name="use-the-actor-service"></a>Az aktor használatára

Aktorpéldányok hozzáférése az aktorszolgáltatás folyamatukban futnak. Az aktor szolgáltatáson keresztül aktorpéldányokat programozott módon szerezheti be a szolgáltatási környezet. A szolgáltatási környezet rendelkezik a Partícióazonosító, szolgáltatásnév, alkalmazás neve és egyéb Azure Service Fabric platform jellemző információkat.

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

Az összes megbízható szolgáltatásokhoz hasonlóan az aktorszolgáltatást regisztrálni kell egy bizonyos szolgáltatástípusként a Service Fabric-futtatókörnyezet. Az az aktorszolgáltatás futtatni tudja az aktorpéldányokat az aktor típusát is regisztrálni kell az aktorszolgáltatásba. Az aktorok esetében ezt a feladatot az `ActorRuntime` regisztrációs metódus végzi el. A legegyszerűbb esetben regisztrálhatja az aktor típusát, és az aktorszolgáltatás majd az alapértelmezett beállítást használja.

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

Azt is megteheti az egy lambda, a regisztrációs módszer által biztosított segítségével kialakíthatja az aktorszolgáltatás saját magának. Ezután konfigurálhatja az aktorszolgáltatás és explicit módon hozhat létre az aktorpéldányokat. Függőségek megváltoztathatják a az aktor keresztül a konstruktor.

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

## <a name="actor-service-methods"></a>Aktor service módszerek

Az aktor service valósítja meg `IActorService` (C#) vagy `ActorService` (Java), amely viszont valósít meg `IService` (C#) vagy `Service` (Java). Ez az interfész a Reliable Services távelérésének lehetővé tétele, ami lehetővé teszi a távoli eljáráshívások szolgáltatás módszerek használják. Szolgáltatás távelérésének lehetővé tétele keresztül távolról hívható szolgáltatásiszint-metódusokat tartalmaz. Használhatja azt, hogy [számbavétele](service-fabric-reliable-actors-enumerate.md) és [törlése](service-fabric-reliable-actors-delete-actors.md) actors.


## <a name="custom-actor-service"></a>Egyéni aktorszolgáltatás

Az aktor regisztrációs lambda használatával regisztrálhatja a saját egyéni aktorszolgáltatás származó `ActorService` (C#) és `FabricActorService` (Java). Ezután valósítható meg a saját szolgáltatásiszint-funkciót, ha egy szolgáltatás osztályból `ActorService` (C#) vagy `FabricActorService` (Java). Egy egyéni aktorszolgáltatás örökli az összes aktor futtatókörnyezet funkcióinak `ActorService` (C#) vagy `FabricActorService` (Java). Saját szolgáltatás módszerek végrehajtásához használható.

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

## <a name="implement-actor-backup-and-restore"></a>Alkalmazzon aktorok biztonsági mentése és visszaállítása

Egy egyéni aktorszolgáltatás tehetők közzé a biztonsági másolatok aktor kihasználásával a távoli eljáráshívás figyelő már szerepel a metódus `ActorService`. Egy vonatkozó példáért lásd: [biztonsági mentési és visszaállítási actors](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>A távoli eljáráshívás V2 (interface-kompatibilis) stack használó aktor

A távoli eljáráshívás V2 (csatoló kompatibilis, más néven V2_1) stack rendelkezik a V2 távoli eljáráshívás verem összes funkcióját. A kapcsolat a távoli eljáráshívás V1 verem kompatibilis, de már nem kompatibilis a V2 és V1. Frissítse a V1 V2_1 nincs hatással a szolgáltatás rendelkezésre állására, kövesse a következő szakaszban.

A következő módosítások szükségesek a távoli eljáráshívás V2_1 verem használata:

1. Adja hozzá a következő szerelvény attribútumot az aktorok.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Hozhat létre, és frissítse az aktorszolgáltatás és az ügyfél aktorprojektek a V2-verem használatának megkezdéséhez.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Aktor szolgáltatás frissítése távoli eljáráshívás V2 (interface-kompatibilis) stack anélkül, hogy befolyásolná a szolgáltatás rendelkezésre állása

Ez a módosítás egy kétlépéses frissítése. Kövesse az itt látható sorrendben.

1. Adja hozzá a következő szerelvény attribútumot az aktorok. Ez az attribútum elindít két figyelői aktorszolgáltatás, V1 (meglévő) és a V2_1 figyelő. Ez a változás az aktorszolgáltatás frissítése.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Az aktor ügyfelek frissítése, az előző frissítés befejezése után.
   Ebben a lépésben gondoskodik arról, hogy az aktor proxyt használ-e a távoli eljáráshívás V2_1 stack.

3. Ez a lépés nem kötelező. Módosítsa az előző attribútum távolítsa el a V1-figyelőt.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>A távoli eljáráshívás V2 verem használó aktor

A 2.8-as verziója NuGet-csomagot, a felhasználók most már használhatja a távoli eljáráshívás V2 verem, amely jobb, és biztosítja a Funkciók, például egyéni sorba rendezésre. Távoli eljáráshívás V2 nem visszamenőlegesen kompatibilis a meglévő távoli eljáráshívás hibával (most már a V1 távoli eljáráshívás verem is nevezik).

A következő módosítások szükségesek a távoli eljáráshívás V2 verem használata.

1. Adja hozzá a következő szerelvény attribútumot az aktorok.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Hozhat létre, és frissítse az aktorszolgáltatás és aktorprojektek ügyfél használatához a V2-vermet.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>A távoli eljáráshívás V2 verem az aktorszolgáltatás frissíteni anélkül, hogy befolyásolná a szolgáltatás rendelkezésre állása

Ez a módosítás egy kétlépéses frissítése. Kövesse az itt látható sorrendben.

1. Adja hozzá a következő szerelvény attribútumot az aktorok. Ez az attribútum elindít két figyelői aktorszolgáltatás, V1 (meglévő) és a V2-figyelő. Ez a változás az aktorszolgáltatás frissítése.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Az aktor ügyfelek frissítése, az előző frissítés befejezése után.
   Ebben a lépésben gondoskodik arról, hogy az aktor proxyt használ-e a távoli eljáráshívás V2 stack.

3. Ez a lépés nem kötelező. Módosítsa az előző attribútum távolítsa el a V1-figyelőt.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>További lépések

* [Aktor állapotkezelés](service-fabric-reliable-actors-state-management.md)
* [Actors-életciklus-kezelés és szemétgyűjtés gyűjtemény](service-fabric-reliable-actors-lifecycle.md)
* [Aktorok API dokumentációja](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

---
title: Funkciók megvalósítása az Azure Service Fabric actors |} A Microsoft Docs
description: Ismerteti, hogyan írhat saját aktorszolgáltatás, amely megvalósítja a szolgáltatásszintű funkciók ugyanúgy statefulservice-ből öröklődés esetén tenné.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 6aff9e9599d31942f994f3cb4e5e9219f33dc7e1
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205520"
---
# <a name="implementing-service-level-features-in-your-actor-service"></a>Szolgáltatásszintű funkciók megvalósítása az aktor service-ben
Leírtak szerint [rétegezett szolgáltatás](service-fabric-reliable-actors-platform.md#service-layering), az aktorszolgáltatás magát egy olyan megbízható szolgáltatás.  Írhat saját szolgáltatás származó `ActorService` és szolgáltatásszintű funkciók megvalósítása ugyanúgy ugyanúgy, mint például a statefulservice-ből, öröklődés esetén:

- Szolgáltatás biztonsági mentése és visszaállítása.
- Megosztott funkció az összes szereplő, például egy áramkör-megszakító.
- Távoli eljáráshívások az aktorszolgáltatás magát és minden egyes színész.

## <a name="using-the-actor-service"></a>Az aktor szolgáltatással
Aktorpéldányok hozzáférése az aktorszolgáltatás folyamatukban futnak. Az aktor szolgáltatáson keresztül aktorpéldányokat programozott módon szerezheti be a szolgáltatási környezet. A szolgáltatási környezet rendelkezik a Partícióazonosító, szolgáltatásnév, alkalmazás neve és egyéb Service Fabric platform jellemző információkat:

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

Az összes megbízható szolgáltatásokhoz hasonlóan az aktorszolgáltatást regisztrálni kell egy bizonyos szolgáltatástípusként a Service Fabric-futtatókörnyezet. Az az aktorszolgáltatás futtatni tudja az aktorpéldányokat az aktor típusát is regisztrálni kell az aktorszolgáltatásba. Az aktorok esetében ezt a feladatot az `ActorRuntime` regisztrációs metódus végzi el. A legegyszerűbb esetben egyszerűen regisztrálhatja az aktor típusát, és alapértelmezett beállítások használatával az aktorszolgáltatás implicit módon használható:

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

Azt is megteheti az egy lambda, a regisztrációs módszer által biztosított segítségével kialakíthatja az aktorszolgáltatás saját magának. Ezután konfigurálhatja az aktorszolgáltatás, és explicit módon hozhat létre az aktorpéldányokat, ahol az függőségek beszúrása az aktor a konstruktor keresztül:

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
Az Aktor service valósítja meg `IActorService` (C#) vagy `ActorService` (Java), amely viszont valósít meg `IService` (C#) vagy `Service` (Java). Ez az a Reliable Services távelérésének lehetővé tétele, ami lehetővé teszi a távoli eljáráshívások szolgáltatás módszerek által használt kapcsolat. Szolgáltatás távelérésének lehetővé tétele keresztül távolról lehet hívni, és lehetővé teszi, hogy szolgáltatásiszint-metódusokat tartalmaz [számbavétele](service-fabric-reliable-actors-enumerate.md) és [törlése](service-fabric-reliable-actors-delete-actors.md) actors.


## <a name="custom-actor-service"></a>Egyéni aktorszolgáltatás
Az aktor regisztrációs lambda használatával regisztrálhatja a saját egyéni aktorszolgáltatás származó `ActorService` (C#) és `FabricActorService` (Java). Az egyéni aktorszolgáltatás Megvalósíthat a saját szolgáltatásiszint-funkciót, ha egy szolgáltatás osztályból `ActorService` (C#) vagy `FabricActorService` (Java). Egy egyéni aktorszolgáltatás örökli az összes aktor futtatókörnyezet funkcióinak `ActorService` (C#) vagy `FabricActorService` (Java) és a saját szolgáltatás módszerek végrehajtásához használható.

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

## <a name="implementing-actor-backup-and-restore"></a>Aktorok biztonsági mentés és visszaállítás végrehajtása
Egy egyéni aktorszolgáltatás tehetők közzé a biztonsági másolatok aktor kihasználásával a távoli eljáráshívás figyelő már szerepel a metódus `ActorService`.  Egy vonatkozó példáért lásd: [biztonsági mentési és visszaállítási actors](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-using-remoting-v2interfacecompatible-stack"></a>Az aktor távoli eljáráshívás V2(InterfaceCompatible) Stack használatával
Távoli eljáráshívás V2 (más néven V2_1 InterfaceCompatible) verem minden V2 távoli eljáráshívás funkcióját a verem mellett felület kompatibilis stack távoli eljáráshívás V1 stack, de nem kompatibilis a V2 és V1 rendelkezik. Nyugodtan Dolgozhatok addig is a frissítés V1-től V2_1 a szolgáltatás rendelkezésre állása befolyásolása nélkül, kövesse az alábbi [cikk](#actor-service-upgrade-to-remoting-v2interfacecompatible-stack-without-impacting-service-availability).

Következő módosítások szükségesek a távoli eljáráshívás V2_1 verem használata.
 1. Adja hozzá a következő szerelvény attribútumot az Aktorok.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

 2. Build és a frissítés ActorService és Aktor ügyfél projektek V2 Stack használatának megkezdéséhez.

#### <a name="actor-service-upgrade-to-remoting-v2interfacecompatible-stack-without-impacting-service-availability"></a>Aktor szolgáltatás távelérésének lehetővé tétele V2(InterfaceCompatible) Stack szolgáltatás rendelkezésre állása befolyásolása nélkül frissítse.
Ez a változás a 2. lépés – frissítés lesz. Felsorolt, kövesse a lépéseket, ugyanabban a sorrendben.

1.  Adja hozzá a következő szerelvény attribútumot az Aktorok. Ez az attribútum indul el két ActorService, V1-figyelője (meglévő) és V2_1 figyelő. A frissítés ActorService ezt a módosítást.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
  ```

2. A fenti frissítés befejezése után frissítse a ActorClients.
Ez a lépés biztosítja, hogy Aktor Proxy távoli eljáráshívás V2_1 Stack használ.

3. Ez a lépés nem kötelező. A fenti attribútumát távolítsa el a V1-figyelőt.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-using-remoting-v2-stack"></a>Az aktor távoli eljáráshívás V2 Stack használatával
2.8-as nuget-csomagot, a felhasználók mostantól használhatják a távoli eljáráshívás V2 stack, amely több nagy teljesítményű, és biztosítja a Funkciók, például egyéni sorba rendezésre. Távoli eljáráshívás V2 nem kompatibilis a meglévő távoli eljáráshívás stack (hívjuk most azt V1 távoli eljáráshívás veremként).

Következő módosítások szükségesek a távoli eljáráshívás V2 verem használata.
 1. Adja hozzá a következő szerelvény attribútumot az Aktorok.
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

 2. Build és a frissítés ActorService és Aktor ügyfél projektek V2 Stack használatának megkezdéséhez.

#### <a name="actor-service-upgrade-to-remoting-v2-stack-without-impacting-service-availability"></a>Aktor szolgáltatás távelérésének lehetővé tétele V2 Stack szolgáltatás rendelkezésre állása befolyásolása nélkül frissítse.
Ez a változás a 2. lépés – frissítés lesz. Felsorolt, kövesse a lépéseket, ugyanabban a sorrendben.

1.  Adja hozzá a következő szerelvény attribútumot az Aktorok. Ez az attribútum indul el két ActorService, V1-figyelője (meglévő) és V2-figyelő. A frissítés ActorService ezt a módosítást.

  ```csharp
  [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
  ```

2. A fenti frissítés befejezése után frissítse a ActorClients.
Ez a lépés biztosítja, hogy Aktor Proxy távoli eljáráshívás V2 Stack használ.

3. Ez a lépés nem kötelező. A fenti attribútumát távolítsa el a V1-figyelőt.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>További lépések
* [Aktor állapotkezelés](service-fabric-reliable-actors-state-management.md)
* [Actors-életciklus-kezelés és szemétgyűjtés gyűjtemény](service-fabric-reliable-actors-lifecycle.md)
* [Aktorok API dokumentációja](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-mintakód](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

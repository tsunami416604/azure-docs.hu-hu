---
title: A Reliable Actors keretrendszer a polimorfizmus |} Microsoft Docs
description: "Hierarchiák .NET felületek és Reliable Actors keretében funkcióit és API-definíciók létrehozása."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/29/2017
ms.author: vturecek
ms.openlocfilehash: 36a59a41b2261369a2062c76ef90aebf7e24a221
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>A Reliable Actors keretrendszer a polimorfizmus
A Reliable Actors keretrendszer használatával szeretné használni a kialakítás objektumorientált ugyanazok a módszerek számos szereplője build teszi lehetővé. Ezek a módszerek egyike polimorf, amely lehetővé teszi, hogy a típusok és felületek több öröklik az általános a szülők. Öröklődés Reliable Actors keretében általában modelljébe .NET néhány további korlátozásokkal. Java/Linux, esetén ez azt jelenti a Java-modell.

## <a name="interfaces"></a>Felületek
A Reliable Actors keretrendszer szükséges legalább egy csatoló a szereplő típus által végrehajtandó adható meg. Ez az interfész létrehozásához egy proxy osztály, amely segítségével az ügyfelek által a szereplője folytatott kommunikációhoz használatos. Felületek örökölhet egyéb felületek, mindaddig, amíg minden egy szereplő típus által megvalósított illesztőfelület és az összes a szülők végső soron származik IActor(C#) vagy Actor(Java). IActor(C#) és Actor(Java) a platform által definiált alap-illesztőfelületben a szereplőket a .NET- és a Java keretrendszert a kulcsattribútumokkal. Ebből kifolyólag a klasszikus polimorfizmus példa alakzatok használatával lehet, hogy kinéznie:

![A shape szereplőket felület hierarchia][shapes-interface-hierarchy]

## <a name="types"></a>Típusok
A hierarchiában szereplő típusú, amely alapján a platform által biztosított szereplő alaposztály is létrehozhat. Alakzatok, ha lehetséges, hogy base `Shape`(C#) vagy `ShapeImpl`(Java) típusa:

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

A altípushoz `Shape`(C#) vagy `ShapeImpl`(Java) a következő módszerek lehet felülbírálni.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Megjegyzés: a `ActorService` az aktor attribútum. Ez az attribútum be van állítva a megbízható szereplő keretrendszer, hogy azt automatikusan hozzon létre egy szolgáltatás, az ilyen típusú szereplője üzemeltetéséhez. Bizonyos esetekben érdemes létrehozni, amely kizárólag olyan funkciókat altípusainál való megosztás, és soha nem használandó tényleges szereplője példányosítható alaptípusa. Ezekben az esetekben kell használnia a `abstract` annak jelzésére, hogy soha ne hozzon létre egy adott típusú alapján szereplő kulcsszó.

## <a name="next-steps"></a>Következő lépések
* Lásd: [hogyan a Reliable Actors keretrendszer kihasználja a Service Fabric-platformról](service-fabric-reliable-actors-platform.md) megbízhatóságát, méretezhetőségét és konzisztens állapotú legyen.
* További tudnivalók a [szereplő életciklus](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

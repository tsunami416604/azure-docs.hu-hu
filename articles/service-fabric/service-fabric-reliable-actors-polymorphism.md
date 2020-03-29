---
title: Polimorfizmus a Megbízható szereplők keretrendszerben
description: A Reliable Actors keretrendszerben .NET-csatolók és -típusok hierarchiáit hozhat létre a funkciók és az API-definíciók újrafelhasználásához.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4e485463f41cdfbadeb166ecbb3a86d4a32c1589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348926"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfizmus a Megbízható szereplők keretrendszerben
A Reliable Actors keretrendszer lehetővé teszi, hogy a szereplők segítségével számos ugyanazokat a technikákat, amelyeket használni objektum-orientált tervezés. Az egyik ilyen technika a polimorfizmus, amely lehetővé teszi, hogy a típusok és a felületek örököljenek az általánosabb szülőktől. Öröklődés a Reliable Actors keretrendszer általában követi a .NET modell néhány további megkötések. Java/Linux esetén a Java modellt követi.

## <a name="interfaces"></a>Interfészek
A Reliable Actors keretrendszer megköveteli, hogy legalább egy felületet kell végrehajtani az aktor típus. Ez a felület egy proxyosztály létrehozására szolgál, amelyet az ügyfelek a szereplőkkel való kommunikációra használhatnak. A felületek örökölhetnek más felületekről, amennyiben minden olyan felület, amelyet egy aktor típus valósít meg, és az összes szülő végül az IActor(C#) vagy az Actor(Java) fájlból származik. Az IActor(C#) és az Actor(Java) a platform által definiált alapfelületek a .NET, illetve java keretrendszerek szereplői számára. Így a klasszikus polimorfizmus példa segítségével formák tűnhet valahogy így:

![Illesztőfelület-hierarchia alakzatszereplőkszámára][shapes-interface-hierarchy]

## <a name="types"></a>Típusok
Aktortípusok hierarchiáját is létrehozhatja, amelyek a platform által biztosított alap aktor osztályból származnak. Alakzatok esetében lehet egy alap `Shape`(C#) `ShapeImpl`vagy (Java) típusa:

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

A (C#) `Shape`vagy `ShapeImpl`(Java) altípusai felülbírálhatják a metódusok az alapból.

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

Figyelje `ActorService` meg az attribútumot az aktor típuson. Ez az attribútum azt mondja a Reliable Actor keretrendszer, amely automatikusan hozzon létre egy szolgáltatást az ilyen típusú szereplők üzemeltetéséhez. Bizonyos esetekben előfordulhat, hogy olyan alaptípust szeretne létrehozni, amely kizárólag az altípusokkal való funkciók megosztására szolgál, és soha nem használható konkrét szereplők létrehozására. Ezekben az esetekben `abstract` a kulcsszó segítségével jelezheti, hogy az adott típus alapján soha nem hoz létre aktort.

## <a name="next-steps"></a>További lépések
* Tekintse [meg, hogy a Reliable Actors keretrendszer hogyan használja ki a Service Fabric platform](service-fabric-reliable-actors-platform.md) megbízhatóságot, méretezhetőséget és konzisztens állapotot.
* További információ a [szereplő életciklusáról.](service-fabric-reliable-actors-lifecycle.md)

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

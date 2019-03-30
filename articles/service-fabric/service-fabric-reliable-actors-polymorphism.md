---
title: A Reliable Actors keretrendszerben polimorfizmus |} A Microsoft Docs
description: .NET felületek és-funkciókat és API-definíciók újra felhasználhatja a Reliable Actors keretrendszerben típusok hierarchiákat hozhat létre.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: vturecek
ms.assetid: ef0eeff6-32b7-410d-ac69-87cba8b8fd46
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: c14b3006184f7bd6dcd1eb67be11bd0214957d72
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58662958"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>A Reliable Actors keretrendszerben polimorfizmus
A Reliable Actors-keretrendszer lehetővé teszi ugyanazokat a technikákat szeretné használni Tervező objektumorientált többféle actors hozhat létre. Ezek a módszerek egyike polimorfizmus, amellyel típusok és felületek több öröklése az szülők általánosítva. A Reliable Actors keretrendszerben öröklési általában néhány további korlátozások a .NET-modell követi. Java/Linux, esetén, a Java modelljét követi.

## <a name="interfaces"></a>Adapterek
A Reliable Actors-keretrendszer kell megadni az aktor típusát által végrehajtandó legalább egy csatoló. Ez az interfész a actors kommunikálni az ügyfelek által használható proxyosztály létrehozására szolgál. Mindaddig, amíg minden felület, amelyet az aktor típusát és az összes hozzá tartozó szülő végső soron származtatást IActor örökölt egyéb felületek felületek is (C#) vagy Actor(Java). IActor (C#) és Actor(Java) rendre a platform által meghatározott base felületek aktorok a .NET, Java és a keretrendszereket. Így a klasszikus polimorfizmus példa az alakzatok használata előfordulhat, hogy a következőhöz hasonló:

![Az alakzat szándékú felhasználók felület hierarchia][shapes-interface-hierarchy]

## <a name="types"></a>Típusok
A hierarchiában szereplő típusú, amely alapján az Aktor alaposztály a platform által biztosított is létrehozhat. Esetén alakzatokat, lehetséges, hogy egy alap `Shape`(C#) vagy `ShapeImpl`(Java) típusa:

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

A altípushoz `Shape`(C#) vagy `ShapeImpl`(Java) felül lehet bírálni a következő metódusokat.

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

Megjegyzés: a `ActorService` attribútum az aktor típusát. Ez az attribútum jelzi, hogy azt automatikusan hozzon létre ilyen típusú actors üzemeltető szolgáltatás a Reliable Actors keretrendszerben. Bizonyos esetekben célszerű létrehozni, amely kizárólag szánt funkciók osztanak meg altípus és a rendszer soha nem használja vezérlőként konkrét actors alaptípusa. Ezekben az esetekben kell használnia a `abstract` jelzi, hogy soha ne hozzon létre egy adott típus alapján szereplő kulcsszót.

## <a name="next-steps"></a>További lépések
* Lásd: [hogyan használja a Reliable Actors-keretrendszer a a Service Fabric platformot](service-fabric-reliable-actors-platform.md) megbízhatóságát, méretezhetőségét és konzisztens állapotba.
* További információ a [aktor életciklus](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png

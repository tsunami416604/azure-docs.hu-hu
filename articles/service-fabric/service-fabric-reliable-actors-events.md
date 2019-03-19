---
title: Események actor-alapú Azure Service Fabric actors |} A Microsoft Docs
description: A Service Fabric Reliable Actors eseményei bemutatása.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: fead1cf639a379fc288122e39629909786aa3f14
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57844800"
---
# <a name="actor-events"></a>Actors-események
Actors-események az aktor a legjobb értesítéseket küldhet az ügyfelek számára terveztek. Actors-események actor-ügyfél-kommunikáció lettek kialakítva, és nem alkalmazható az aktor actor kommunikációt.

Az alábbi kódrészletek azt mutatják szereplő események használata az alkalmazásban.

Adja meg, amely leírja az aktor által közzétett események illesztőfelületet. Ez az interfész kell származnia a `IActorEvents` felületet. Az argumentumok a módszerek [adatokat szerződéses szerializálható](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). A módszerek musí vrátit typ void, eseményként értesítések egyik módja, és a lehető legjobb.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Az aktor található az aktor illesztőjét által közzétett események deklarálható.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
Az ügyfél oldalán az eseménykezelő megvalósítása.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

Az ügyfélen hozzon létre egy proxyt, amely közzéteszi az eseményt az aktor felé, és iratkozzon fel az eseményeket.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

Folyamatban lévő feladatátvételi teszteket, ha az aktor előfordulhat, hogy feladatátvételt egy másik folyamat vagy csomópont. Az aktor proxy az aktív előfizetések kezeli, és automatikusan újra feliratkozik rájuk. Szabályozhatja, hogy az előfizetés újbóli intervallum keresztül a `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API-t. Előfizetés lemondása, használja a `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API-t.

Az aktor mivel azok közzé az az eseményeket. Ha az esemény-előfizetők, a az Actors modul elküldi őket az értesítést.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>További lépések
* [Actors – újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
* [Actors diagnosztizálása és teljesítményfigyelése](service-fabric-reliable-actors-diagnostics.md)
* [Aktor API dokumentációja](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C#-minta kódja](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

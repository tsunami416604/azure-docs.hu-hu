---
title: "Aktor-alapú Azure mikroszolgáltatások események |} Microsoft Docs"
description: "A Service Fabric Reliable Actors események bemutatása."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: aa01b0f7-8f88-403a-bfe1-5aba00312c24
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/13/2017
ms.author: amanbha
ms.openlocfilehash: d936670c548ff709fc2e935d3f28d94e4bde8a04
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="actor-events"></a>Szereplő események
Szereplő események teszik lehetővé a szereplő lehető legjobb értesítések küldése az ügyfelek számára. Szereplő események szereplő ügyfél kommunikációhoz, és a aktor szereplő kommunikáció nem használható.

Az alábbi kódtöredékek bemutatják, hogyan az alkalmazásban szereplő események használatára.

Adja meg, amely leírja a szereplő által közzétett események illesztőfelület. Ez az interfész típusból kell származtatni a `IActorEvents` felületet. Az argumentumok a módszerek [adategyezmény-szerializálható](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). A módszerek void értéket kell visszaadnia, és eseményként értesítések egy- és a lehető legkedvezőbb módon.

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
Az aktor felületen aktor által közzétett események deklarálható.

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
Az ügyféloldalon az eseménykezelő megvalósítása.

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

Az ügyfélen a közzétevő az esemény szereplő proxy létrehozására és az események előfizetni.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

Esetén a feladatátvétel a szereplő előfordulhat, hogy átadja egy másik folyamat vagy a csomópont. Az aktor proxy az aktív előfizetések kezeli, és automatikusan újra előfizet rájuk. Az újbóli előfizetési időköz keresztül szabályozhatja a `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API. Mondhatja, használja a `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API.

Az aktor egyszerűen közzététele az eseményeket, akkor fordulhat elő. Az esemény-előfizetők esetén, a gyakrabban futtatókörnyezet fogja őket az értesítés elküldéséhez.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Következő lépések
* [Aktor rögzítve](service-fabric-reliable-actors-reentrancy.md)
* [Aktor diagnosztika és teljesítményfigyelés](service-fabric-reliable-actors-diagnostics.md)
* [Aktor API referenciadokumentációt](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java mintakód](http://github.com/Azure-Samples/service-fabric-java-getting-started)

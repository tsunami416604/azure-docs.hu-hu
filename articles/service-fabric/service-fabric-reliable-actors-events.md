---
title: Események a szereplő-alapú Azure Service Fabric szereplők
description: Ismerje meg a Service Fabric megbízható szereplők események, hatékony módja az aktor és az ügyfél közötti kommunikáció.
author: vturecek
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 73c149a0d0992fecd1acf633891057570285df64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639666"
---
# <a name="actor-events"></a>Színész események
Az aktoresemények lekínálják a legjobb visszajelzéseket az aktortól az ügyfeleknek küldött legjobb értesítések küldésére. Az aktoresemények a szereplő és az ügyfél közötti kommunikációra szolgálnak, és nem használhatók a szereplő és a színész közötti kommunikációhoz.

A következő kódrészletek bemutatják, hogyan használhatja az aktoreseményeket az alkalmazásban.

Adjon meg egy felületet, amely leírja az aktor által közzétett eseményeket. Ennek az összeköttetésnek `IActorEvents` az összeköttetésből kell származnia. A metódusok argumentumainak [adatszerződés szerializálhatónak](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)kell lenniük . A módszereknek érvénytelennek kell lennie, mivel az eseményértesítések az egyik út és a legjobb erőfeszítés.

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
Deklarálja az aktor által közzétett eseményeket az aktor felületen.

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
Az ügyféloldalon valósítsa meg az eseménykezelőt.

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

Az ügyfélen hozzon létre egy proxyt az aktor, amely közzéteszi az eseményt, és iratkozz fel az eseményekre.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

Feladatátvétel esetén az aktor átadhat egy másik folyamatvagy csomópont. Az aktor proxy kezeli az aktív előfizetések, és automatikusan újra előfizet őket. Az API-n keresztül szabályozhatja az `ActorProxyEventExtensions.SubscribeAsync<TEvent>` újra-előfizetési időközt. A leiratkozáshoz `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` használja az API-t.

A színész, tegye közzé az eseményeket, ahogy történnek. Ha vannak előfizetők az eseményre, az Actors futásidejű elküldi nekik az értesítést.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>További lépések
* [Színész reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Aktor diagnosztikája és teljesítményfigyelése](service-fabric-reliable-actors-diagnostics.md)
* [Aktor API-referenciadokumentáció](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

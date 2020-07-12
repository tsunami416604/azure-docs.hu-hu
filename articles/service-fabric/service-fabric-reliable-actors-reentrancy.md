---
title: Újbóli belépés az Azure Service Fabric Actors szolgáltatásban
description: A Service Fabric Reliable Actors újbóli belépés bemutatása, amely logikailag elkerülheti a blokkolást a hívási környezet alapján.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 100cf1f7bf8a0c903cfd61d93d2f923c32cabd11
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260958"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors újbóli belépés
A Reliable Actors futtatókörnyezet alapértelmezés szerint lehetővé teszi a logikai hívás kontextus-alapú újbóli belépés. Ez lehetővé teszi, hogy a szereplők újra bejelentkeznek, ha ugyanabban a hívási környezeti láncban vannak. Az A-Actor például üzenetet küld a B színésznek, aki üzenetet küld a C-nek. Az üzenet feldolgozásának részeként, ha a színész A "A" résztvevőt hívja meg, az üzenet újra bejelentkező, így engedélyezve lesz. A másik hívási környezet részét képező más üzeneteket a rendszer letiltja a (z)

Két lehetőség érhető el az enumerálásban definiált Actor újbóli belépés `ActorReentrancyMode` :

* `LogicalCallContext`(alapértelmezett viselkedés)
* `Disallowed`– letiltja a újbóli belépés

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
A újbóli belépés a regisztráció során konfigurálható a `ActorService` beállításokban. A beállítás a Actors szolgáltatásban létrehozott összes színészi példányra vonatkozik.

A következő példa egy Actor szolgáltatást mutat be, amely a újbóli belépés módot állítja be `ActorReentrancyMode.Disallowed` . Ebben az esetben, ha egy színész visszaküldési üzenetet küld egy másik szereplőnek, a rendszer a típus kivételét `FabricException` fogja eldobni.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Következő lépések
* További információ a újbóli belépés a [Actor API-dokumentációjában](/previous-versions/azure/dn971626(v=azure.100))

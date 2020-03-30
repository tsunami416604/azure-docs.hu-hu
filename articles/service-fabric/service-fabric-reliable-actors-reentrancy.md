---
title: Reentrancy az Azure Service Fabric szereplőiben
description: Bevezetés a reentrancy a Service Fabric megbízható szereplők, egy módja annak, hogy logikailag ne blokkolja a híváskörnyezet alapján.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645531"
---
# <a name="reliable-actors-reentrancy"></a>Megbízható szereplők reentrancy
A Reliable Actors futásidejű, alapértelmezés szerint lehetővé teszi a logikai hívás környezetalapú reentrancy. Ez lehetővé teszi, hogy a szereplők újra beléphessenek, ha ugyanabban a hívási környezetláncban vannak. Az "A" aktor például üzenetet küld a B. aktornak, aki üzenetet küld a C. Az üzenetfeldolgozás részeként, ha a C. aktor aktor aktort hív, az üzenet újra lép, így engedélyezett lesz. Minden más, egy másik hívási környezet részét szereplő üzenetek blokkolva lesznek az A aktorban, amíg be nem fejezi a feldolgozást.

A `ActorReentrancyMode` felsorakban meghatározott aktor reentrancy-reorszámára két lehetőség áll rendelkezésre:

* `LogicalCallContext`(alapértelmezett viselkedés)
* `Disallowed`- letiltja a reentrancy-t

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
A Reentrancy a `ActorService`regisztráció során a beállításokban konfigurálható. A beállítás az aktorszolgáltatásban létrehozott összes aktorpéldányra vonatkozik.

A következő példa egy aktor szolgáltatást mutat `ActorReentrancyMode.Disallowed`be, amely a reentrancy módot állítja be. Ebben az esetben, ha egy szereplő egy újrabelépő üzenetet `FabricException` küld egy másik szereplőnek, egy kivétel jelenik meg.

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


## <a name="next-steps"></a>További lépések
* További információ a reentrancy-ről az [Actor API referenciadokumentációjában](https://msdn.microsoft.com/library/azure/dn971626.aspx)

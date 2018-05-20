---
title: Az aktor-alapú Azure mikroszolgáltatások rögzítve |} Microsoft Docs
description: A Service Fabric Reliable Actors rögzítve bemutatása
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: be23464a-0eea-4eca-ae5a-2e1b650d365e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 40f52cb399f2d7391657ce4356a0c30921d46e5f
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="reliable-actors-reentrancy"></a>Megbízható szereplője rögzítve
A Reliable Actors futásidejű alapértelmezés szerint lehetővé teszi, hogy logikai hívás környezetfüggő rögzítve. Ez lehetővé teszi a szereplőket ismételten belépő, ha azok hívás környezetben ugyanabba a láncba kell. Például A Aktor üzenetet küld szereplő B, egy üzenet küld Aktor a c kiszolgálóra. Az üzenet feldolgozása részeként szereplő C Aktor A, ha az üzenet ismételten belépő, amelynek engedélyezni. Bármely más üzeneteket, amelyek egy másik hívás környezetet részei le lesz tiltva a Aktor A feldolgozás befejezéséig.

Nincsenek definiálva szereplő rögzítve két lehetőség közül választhat a `ActorReentrancyMode` enum:

* `LogicalCallContext` (alapértelmezés)
* `Disallowed` -rögzítve letiltása

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
Rögzítve konfigurálható egy `ActorService`tartozó beállítások regisztrálás során. A beállítás csak az aktor szolgáltatásban létrehozott összes szereplő példányok.

A következő példa bemutatja az aktor szolgáltatása, amely a rögzítve mód beállítása `ActorReentrancyMode.Disallowed`. Ebben az esetben, ha egy szereplő ismételten belépő üzenetet küld egy másik szereplő, típusú kivételt `FabricException` fog jelezni.

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
* További információ a rögzítve a [szereplő API referenciadokumentációt tartalmaz](https://msdn.microsoft.com/library/azure/dn971626.aspx)

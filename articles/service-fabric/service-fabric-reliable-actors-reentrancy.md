---
title: Az Azure Service Fabric actors – újbóli belépés |} A Microsoft Docs
description: A Service Fabric Reliable Actors – újbóli belépés bemutatása.
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
ms.openlocfilehash: c7a4066a949ad6e66c45dff67f1e80801f2fa4cd
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/07/2018
ms.locfileid: "44055260"
---
# <a name="reliable-actors-reentrancy"></a>A Reliable Actors – újbóli belépés
A Reliable Actors-futtatókörnyezet, alapértelmezés szerint lehetővé teszi a logikai hívás környezetfüggő – újbóli belépés. Ez lehetővé teszi, hogy ha az azonos környezetben hívásláncot azok ismételten belépő az aktorok esetében. Például egy Aktor üzenetet küld az Aktor B, akik egy üzenetet küld az Aktor c-hez Feldolgozni az üzenetet részeként Aktor C meghívja az Aktor A, az üzenet-e ismételten belépő, így a megengedett, hogy. Bármely más hívási környezet részét képező többi üzenet le lesz tiltva az Aktor A feldolgozás befejezéséig tartó.

Két lehetőség van definiálva actors – újbóli belépés érhető el a `ActorReentrancyMode` enum:

* `LogicalCallContext` (alapértelmezés)
* `Disallowed` -letiltása – újbóli belépés

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
– Újbóli belépés konfigurálható egy `ActorService`a beállítások a regisztráció során. A beállítás az aktorszolgáltatás létrehozott összes aktorpéldányok vonatkozik.

Az alábbi példa bemutatja egy aktor szolgáltatás, amely a – újbóli belépés mód beállítása `ActorReentrancyMode.Disallowed`. Ebben az esetben, ha egy szereplő ismételten belépő üzenetet küld egy másik aktor, típusú kivételt adott `FabricException` , a rendszer hibajelzést.

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
* További információ – újbóli belépés a [Aktor API dokumentációja](https://msdn.microsoft.com/library/azure/dn971626.aspx)

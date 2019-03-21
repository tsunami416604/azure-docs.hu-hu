---
title: Az Azure Service Fabric actors törlése |} A Microsoft Docs
description: Ismerje meg, hogy manuálisan a Service Fabric Reliable Actors és állapotuk törlése.
services: service-fabric
documentationcenter: .net
author: amanbha
manager: timlt
editor: vturecek
ms.assetid: b91384cc-804c-49d6-a6cb-f3f3d7d65a8e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: ab6f8740ec1955078c6acbd9cb362a25a89e4bda
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57887391"
---
# <a name="delete-reliable-actors-and-their-state"></a>Reliable Actors és állapotuk törlése
Inaktív szereplők szemétgyűjtés csak megtisztítja a szereplő objektum, de nem távolítja el az aktor State Manager tárolt adatokat. Egy aktor újraaktiválásakor az adatok újra szeretné elérhetővé tenni azt a State Manager keresztül. Azokban az esetekben, ahol actors tárolja az adatokat az State Manager és az inaktiválása, de soha nem kapcsolta be újra azt lehet szükség az adatok törlése.

A [Aktorszolgáltatás](service-fabric-reliable-actors-platform.md) actors töröl egy távoli hívó függvényt biztosít:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

A következő hatásai vannak-e az aktor a jelenleg aktív függően egy szereplő törlése rendelkezik:

* **Aktív Aktor**
  * Aktor eltávolítják az active actors listát, és az inaktiválása.
  * Állapotában a véglegesen törlődik.
* **Inaktív Aktor**
  * Állapotában a véglegesen törlődik.

Egy aktor nelze volat törölni magát az aktor módszerek közül a, mert az aktor nem lehet törölni, amelyben a futtatókörnyezet kapott körül actor-hívás egyszálas hozzáférés zárolása egy aktor hívás környezeten belül végrehajtása közben.

Reliable actors – további információért olvassa el a következőket:
* [Actors – időzítők és emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Actors-események](service-fabric-reliable-actors-events.md)
* [Actors – újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
* [Actors diagnosztizálása és teljesítményfigyelése](service-fabric-reliable-actors-diagnostics.md)
* [Aktor API dokumentációja](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C#-minta kódja](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

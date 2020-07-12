---
title: Azure Service Fabric Actors törlése
description: Megtudhatja, hogyan lehet manuálisan és teljes mértékben törölni Reliable Actors és azok állapotát egy Azure Service Fabric-alkalmazásban.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: d977b6fea71d246610b0e6ef2d9e17d587f1e9ad
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86253541"
---
# <a name="delete-reliable-actors-and-their-state"></a>Reliable Actors-szereplők és állapotuk törlése
A deaktivált szereplők szemét-gyűjteménye csak a Actor objektumot törli, de nem távolítja el a szereplő állapot-kezelőjében tárolt adatmennyiséget. A színész újraaktiválásakor a rendszer ismét elérhetővé teszi az adatforrást az állami kezelőn keresztül. Azokban az esetekben, amikor a szereplők az állapot-kezelőben tárolják az adattárolást, és inaktiválva vannak, de soha nem aktiválják őket, szükség lehet az Adattisztításra.

A [Actor szolgáltatás](service-fabric-reliable-actors-platform.md) egy függvényt biztosít a távoli hívóktól származó szereplők törléséhez:

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

A szereplők törlését a következő hatások határozzák meg attól függően, hogy a szereplő éppen aktív-e:

* **Aktív színész**
  * A színész el lett távolítva az aktív Actors listáról, és inaktiválva van.
  * Az állapota véglegesen törölve lett.
* **Inaktív színész**
  * Az állapota véglegesen törölve lett.

Egy színész nem tudja meghívni magát a törlést az egyik Actor metódusból, mert a színész nem törölhető egy színészi hívási kontextusban, amelyben a futtatókörnyezet zárolást kapott az egyszálas hozzáférés érvényesítéséhez.

Reliable Actorsról a következő témakörben olvashat bővebben:
* [Színészi időzítők és emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Színészi események](service-fabric-reliable-actors-events.md)
* [Actor újbóli belépés](service-fabric-reliable-actors-reentrancy.md)
* [Színészi diagnosztika és Teljesítményfigyelés](service-fabric-reliable-actors-diagnostics.md)
* [A Actor API-referenciájának dokumentációja](/previous-versions/azure/dn971626(v=azure.100))
* [C# mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

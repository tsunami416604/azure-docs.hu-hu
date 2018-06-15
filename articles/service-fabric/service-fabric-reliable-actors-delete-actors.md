---
title: Azure Service Fabric szereplője törlése |} Microsoft Docs
description: 'Útmutató: manuálisan törölje a Service Fabric Reliable Actors és azok állapota.'
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
ms.openlocfilehash: fa4fe018a9e6b32158f5bbd13c44ff57069cb1cf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34208335"
---
# <a name="delete-reliable-actors-and-their-state"></a>Törli a Reliable Actors és azok állapota
A deaktivált szereplője szemétgyűjtés csak a szükségtelenné vált az aktor objektum, de nem távolítja el egy szereplő állapotkezelője tárolt adatokat. Egy szereplő újraaktiválásakor adataik újra szeretné elérhetővé tenni azt az állapotkezelő segítségével. Olyan esetekben, ahol szereplője adat tárolása állapotkezelője és inaktiválása, de soha nem újra aktiválni lehet szükség az adatok törlése.

A [szereplő szolgáltatás](service-fabric-reliable-actors-platform.md) függvény biztosít távoli hívó szereplője törlése:

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

A következő hatásai vannak, attól függően, hogy-e a szereplő jelenleg aktív törlése egy szereplő rendelkezik:

* **Aktív szereplő**
  * Aktor aktív szereplője lista törlődik, és inaktív.
  * Állapotában véglegesen törlődik.
* **Inaktív szereplő**
  * Állapotában véglegesen törlődik.

Nem hívható meg egy szereplő magát az aktor módszerek egyikét a törölni, mert a szereplő nem lehet törölni, amelyben a futtatókörnyezet kapott a zárolási körül szereplő hívása egyszálas hozzáférés kényszerítésére az aktor hívás környezeten belül végrehajtása közben.

További információ a Reliable Actors, olvassa el az alábbiakat:
* [Aktor időzítők és az emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Szereplő események](service-fabric-reliable-actors-events.md)
* [Aktor rögzítve](service-fabric-reliable-actors-reentrancy.md)
* [Aktor diagnosztika és teljesítményfigyelés](service-fabric-reliable-actors-diagnostics.md)
* [Aktor API referenciadokumentációt](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java mintakód](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

---
title: Az Azure Service Fabric szereplőinek törlése
description: Ismerje meg, hogyan manuálisan és teljes mértékben törölheti a megbízható szereplők et és azok állapotát egy Azure Service Fabric-alkalmazásban.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: b90c5a10c64e273f1c8f48c7bf5713859796db65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645616"
---
# <a name="delete-reliable-actors-and-their-state"></a>Reliable Actors-szereplők és állapotuk törlése
Az inaktivált szereplők szemétgyűjtése csak az aktor objektumot tisztítja meg, de nem távolítja el az aktor állapotkezelőjében tárolt adatokat. Ha egy szereplő újra aktiválódik, az adatok ismét elérhetővé válnak számára az állapotkezelőn keresztül. Azokban az esetekben, ahol a szereplők adatokat tárolnak a State Manager ben, és inaktiválva vannak, de soha nem aktiválják újra, szükség lehet az adataik törlésére.

Az [aktor szolgáltatás](service-fabric-reliable-actors-platform.md) egy távoli hívó szereplőinek törléséhez biztosít funkciót:

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

Az aktor törlésének a következő hatásai vannak attól függően, hogy az aktor jelenleg aktív-e:

* **Aktív szereplő**
  * Az aktor törlődik az aktív szereplők listájáról, és inaktívvá válik.
  * Állapota véglegesen törlődik.
* **Inaktív színész**
  * Állapota véglegesen törlődik.

Az aktor nem hívhatja meg a törlést az egyik aktormetódusából, mert az aktor nem törölhető, miközben egy aktorhívási környezetben hajt végre, amelyben a futásidejű zárolást kapott az aktorhívás körül az egyszálas hozzáférés kényszerítéséhez.

A Megbízható szereplőkkel kapcsolatos további információkért olvassa el az alábbi információkat:
* [Aktor időzítők és emlékeztetők](service-fabric-reliable-actors-timers-reminders.md)
* [Színész események](service-fabric-reliable-actors-events.md)
* [Színész reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Aktor diagnosztikája és teljesítményfigyelése](service-fabric-reliable-actors-diagnostics.md)
* [Aktor API-referenciadokumentáció](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png

---
title: Az Azure Service Fabric szereplőinek számbavétele
description: Példák a Reliable Actors és azok metaadatainak enumerálására egy Azure Service Fabric-alkalmazásban példák használatával.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 8e462cc5fa82b8692304f58ef6cf0ea0e2db8725
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245976"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Service Fabric enumerálása Reliable Actors
A Reliable Actors szolgáltatás lehetővé teszi, hogy az ügyfél a szolgáltatás által üzemeltetett szereplők metaadatait enumerálja. Mivel a Actors szolgáltatás particionált állapot-nyilvántartó szolgáltatás, a számbavétel egy partíción történik. Mivel az egyes partíciók több résztvevőt is tartalmazhatnak, a számbavétel a lapozható eredmények halmazát adja vissza. A lapokat a rendszer az összes oldal beolvasása után összehurkolja. Az alábbi példa bemutatja, hogyan hozhatja létre az összes aktív szereplő listáját a Actors szolgáltatás egy partíciójában:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Következő lépések
* [Színészi állapot kezelése](service-fabric-reliable-actors-state-management.md)
* [A Actor életciklusa és a szemét gyűjtése](service-fabric-reliable-actors-lifecycle.md)
* [A Actors API-dokumentációja](/previous-versions/azure/dn971626(v=azure.100))
* [.NET-mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

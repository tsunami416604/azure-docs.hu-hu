---
title: Szereplők számbavétele az Azure Service Fabric-en
description: Példák használatával ismerje meg a megbízható szereplők és metaadataik felsorolását egy Azure Service Fabric-alkalmazásban.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 1516c9005a7c4dd0adcb279e9954e5f882c575c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645599"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Szolgáltatás-háló megbízható szereplőinek számbavétele
A Reliable Actors szolgáltatás lehetővé teszi, hogy az ügyfél a szolgáltatás üzemeltetése által üzemeltetett szereplők metaadatait számba veresse. Mivel az aktor szolgáltatás egy particionált állapotalapú szolgáltatás, a felsorolás partíciónként történik. Mivel minden partíció sok szereplőt tartalmazhat, a felsorolás lapozható eredmények halmazaként jelenik meg. Az oldalak végigvannak hurkolva, amíg az összes oldal el nem olvassa. A következő példa bemutatja, hogyan hozhat létre egy listát az összes aktív szereplőről egy aktorszolgáltatás egy partícióján:

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



## <a name="next-steps"></a>További lépések
* [Szereplő állapotkezelése](service-fabric-reliable-actors-state-management.md)
* [A szereplő életciklusa és a szemétgyűjtés](service-fabric-reliable-actors-lifecycle.md)
* [Actors API referenciadokumentáció](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

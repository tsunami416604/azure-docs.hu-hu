---
title: A Service Fabric a Reliable Actors |} Microsoft Docs
description: Útmutatás a Reliable Actors vannak réteges a Reliable Services és a Service Fabric-platformról szolgáltatásának használatához.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: amanbha
ms.assetid: 45839a7f-0536-46f1-ae2b-8ba3556407fb
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: b2369f9468c54f10d01203841b6d7ba44b7ba2de
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Így használja a Reliable Actors a Service Fabric platformot
Ez a cikk bemutatja, hogy Reliable Actors működik-e az Azure Service Fabric-platformon. Futtassa a keretrendszer, amely a egy állapotalapú szolgáltatás megvalósítását a Reliable Actors hívása a *szereplő szolgáltatás*. Az aktor szolgáltatás összes életciklusa és a a szereplőket terjesztéséhez üzenet kezeléséhez szükséges összetevőket tartalmazza:

* Az Aktor futásidejű életciklusát, szemétgyűjtés, felügyeli, és egyszálas vezérlésről.
* Egy szereplő szolgáltatás távoli eljáráshívási figyelő távelérési szereplőkkel hívásainak fogad, és elküldi azokat a kézbesítő útvonalat a megfelelő szereplő példány.
* Az Aktor Állapotszolgáltató becsomagolja állapotszolgáltatója (például a megbízható gyűjtemények állapotszolgáltató), és egy adapter biztosít szereplő állapotkezelés.

Ezek az összetevők együttesen alkotják a megbízható szereplő keretrendszer.

## <a name="service-layering"></a>A réteges szolgáltatás
Mert maga szereplő szolgáltatás egy megbízható szolgáltatás összes a [alkalmazásmodell](service-fabric-application-model.md), életciklusát, [csomagolási](service-fabric-package-apps.md), [telepítési](service-fabric-deploy-remove-applications.md), frissítési és méretezési Reliable Services elveit aktorszolgáltatások ugyanúgy alkalmazni.

![Aktor szolgáltatás réteges][1]

A fenti ábrán a Service Fabric-alkalmazás-keretrendszerbeli és a felhasználói kód közötti kapcsolatot mutatja be. Kék elemek határoz meg a Reliable Services alkalmazás-keretrendszer, narancssárga megbízható szereplő keretében, és zöld jelenti felhasználói kód.

A Reliable Services, a szolgáltatás örökli a `StatefulService` osztály. Ez az osztály maga a származó `StatefulServiceBase` (vagy `StatelessService` állapotmentes szolgáltatások). A Reliable Actors az aktor szolgáltatását használja. Az aktor szolgáltatás egy másik megvalósításában a `StatefulServiceBase` a szereplője futtatják szereplő mintát megvalósító osztály. Mivel a szereplő szolgáltatás megvalósítása csak `StatefulServiceBase`, saját szolgáltatás abból származó írhat `ActorService` és valósít meg a szolgáltatásszint-funkciókat az azonos módon történő örökléskor `StatefulService`, például:

* Szolgáltatás biztonsági mentése és visszaállítása.
* Összes szereplő, például egy áramköri megszakító megosztás funkciót.
* Távoli eljáráshívások a szereplő maga és a minden egyes szereplő.

> [!NOTE]
> Állapotalapú szolgáltatások jelenleg nem támogatottak a Java/Linux.

További információkért lásd: [végrehajtási szolgáltatásiszint-szolgáltatásokat az aktor szolgáltatásban](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Alkalmazásmodell
Aktorszolgáltatások Reliable Services, így az alkalmazásmodell megegyezik. Azonban a szereplő keretrendszer build tools készítése alkalmazás modell fájlok meg.

### <a name="service-manifest"></a>Szolgáltatás jegyzék
Az aktor framework összeállítási eszközök automatikus létrehozása a szereplő szolgáltatás ServiceManifest.xml fájl tartalmát. Ez a fájl tartalmazza:

* Aktor szolgáltatás típusa. A következő típusnév jön létre, a aktor projekt neve alapján. Az aktor az adatmegőrzési attribútumok alapján, a HasPersistedState jelző nincs beállítva megfelelően.
* A kódcsomag.
* A konfigurációs csomag.
* Erőforrások és a végpontok.

### <a name="application-manifest"></a>Az alkalmazásjegyzék
Az aktor framework összeállítási eszközök automatikus létrehozása az aktor szolgáltatás alapértelmezett szolgáltatásdefiníció. A build tools feltöltése az alapértelmezett szolgáltatás tulajdonságai:

* Az adatmegőrzési attribútumot a szereplő replikaszám beállítása határozza meg. Minden alkalommal, amikor az adatmegőrzési attribútumot a szereplő módosul, a replika készlet alapértelmezett szolgáltatásdefinícióban számolás ennek megfelelően.
* Partícióséma és a tartomány a teljes Int64-címtartománnyal rendelkező egységes Int64 beállítása.

## <a name="service-fabric-partition-concepts-for-actors"></a>A Service Fabric partícióazonosító fogalmak actors
Aktorszolgáltatások a particionált állapotalapú szolgáltatások. Mindegyik partíció szereplő szolgáltatási szereplője tartalmaz. Partíciók a rendszer automatikusan terjeszt a Service Fabric több csomópont feladatait. Ennek eredményeképpen szereplő példányok terjesztése.

![Aktor particionálás és terjesztési][5]

Megbízható szolgáltatások különböző partíciós séma és a partíció kulcstartományokkal hozhatja létre. Az aktor szolgáltatás Int64 particionálási sémát a teljes Int64-címtartománnyal rendelkező szereplője van leképezve partíciók használja.

### <a name="actor-id"></a>Aktor azonosítója
Minden, amely jön létre a szolgáltatásban szereplő van társítva, által képviselt egyedi azonosítója a `ActorId` osztály. `ActorId` érték nem átlátszó azonosítója, amely alkalmas szereplője egyenletes elosztása a partíciók közötti véletlenszerű azonosítók létrehozásával:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Minden `ActorId` Int64 kivonatolja. Ezért a szereplő szolgáltatás a teljes Int64-tartomány egy Int64 particionálási sémát kell használnia. Azonban használható egyéni értéket egy `ActorID`, beleértve a GUID/UUID-k, karakterláncok és Int64s.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

GUID azonosítók/UUID-k és karakterláncok használata, ha az értékek Int64 számára van kivonatolva. Azonban ha feltétlenül van explicit módon adja meg a Int64 egy `ActorId`, a Int64 felelteti meg közvetlenül egy partíció további kivonatoláshoz nélkül. Ez a módszer, mely partíció a szereplője kerülnek vezérlőre is használhatja.


## <a name="next-steps"></a>További lépések
* [Aktor állapotkezelés](service-fabric-reliable-actors-state-management.md)
* [Aktor életciklusának és szemétgyűjtési gyűjtése](service-fabric-reliable-actors-lifecycle.md)
* [Actors API referenciadokumentációt tartalmaz](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-példakód](http://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

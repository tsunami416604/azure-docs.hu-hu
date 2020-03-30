---
title: Megbízható szereplők a service fabric
description: Bemutatja, hogyan megbízható szereplők vannak rétegezve a megbízható szolgáltatások és a Service Fabric platform szolgáltatásainak használata.
author: vturecek
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: 92c717fa2c82dd147acd3c28333e37ccf8dd2e89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282301"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Hogyan használja a Megbízható szereplők a Service Fabric platformot?
Ez a cikk bemutatja, hogyan működik a Reliable Actors az Azure Service Fabric platformon. A Reliable Actors olyan keretrendszerben fut, amely az *aktorszolgáltatás*nak nevezett állapotalapú megbízható szolgáltatás megvalósításában található. Az aktor szolgáltatás tartalmazza az összes szükséges összetevőt az életciklus és az üzenetek feladása a szereplők:

* Az aktor futásidejű kezeli az életciklust, a szemétgyűjtést, és kényszeríti az egyszálas hozzáférést.
* Az aktor szolgáltatás átirányító figyelő elfogadja a távoli hozzáférés az inzsit, és elküldi őket a diszpécser a megfelelő akta példányútvonal.
* Az aktor állapotszolgáltató burkolja az állapotszolgáltatókat (például a Reliable Collections állapotszolgáltatót), és adaptert biztosít az aktor állapotkezeléséhez.

Ezek az összetevők együtt alkotják a Reliable Actor keret.

## <a name="service-layering"></a>Szolgáltatás rétegezése
Mivel az aktor szolgáltatás maga egy megbízható szolgáltatás, az összes [alkalmazásmodell,](service-fabric-application-model.md)életciklus, [csomagolás,](service-fabric-package-apps.md) [üzembe helyezés,](service-fabric-deploy-remove-applications.md)frissítés és skálázási fogalmak megbízható szolgáltatások ugyanúgy vonatkoznak az aktor szolgáltatások.

![Aktor szolgáltatás rétegezés][1]

Az előző ábra a Service Fabric alkalmazáskeretrendszerek és a felhasználói kód közötti kapcsolatot mutatja be. A kék elemek a Reliable Services alkalmazáskeretrendszert, a narancssárga a Reliable Actor keretrendszert, a zöld pedig a felhasználói kódot jelöli.

A Megbízható szolgáltatások, a `StatefulService` szolgáltatás örökli az osztályt. Ez az osztály `StatefulServiceBase` maga `StatelessService` származik (vagy állapotmentes szolgáltatások). A Megbízható szereplők, az aktor szolgáltatás használata. Az aktor szolgáltatás `StatefulServiceBase` egy másik megvalósítása az osztály, amely megvalósítja az aktor minta, ahol a szereplők futnak. Mivel maga az aktorszolgáltatás csak a implementációja, a saját szolgáltatását `StatefulServiceBase`is megírhatja, amely a szolgáltatásszintű funkciókból `ActorService` származik, és ugyanúgy valósítható meg, mint az örökléskor, `StatefulService`például:

* Szolgáltatás biztonsági mentése és visszaállítása.
* Megosztott funkciók az összes szereplő, például egy megszakító.
* A távoli eljárás magához az aktorszolgáltatáshoz és minden egyes szereplőhez szólítja meg.

További információ: [Implement service-level features in your Actor service](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Alkalmazásmodell
Az aktor szolgáltatások megbízható szolgáltatások, így az alkalmazásmodell ugyanaz. Azonban az aktor keretrendszer build eszközök generál néhány alkalmazásmodell-fájlokat az Ön számára.

### <a name="service-manifest"></a>Szolgáltatásjegyzék
Az aktor keretrendszer build eszközei automatikusan generálja az aktor szolgáltatás ServiceManifest.xml fájl tartalmát. Ez a fájl a következőket tartalmazza:

* Aktor szolgáltatás típusa. A típusnév az aktor projektneve alapján jön létre. Az aktor perzisztencia attribútuma alapján a HasPersistedState jelző is ennek megfelelően van beállítva.
* Kódcsomag.
* Konfigurációs csomag.
* Erőforrások és végpontok.

### <a name="application-manifest"></a>Alkalmazásjegyzék
Az aktor keretrendszer build eszközei automatikusan létrehoz egy alapértelmezett szolgáltatásdefiníciót az aktor szolgáltatáshoz. A buildeszközök feltöltik az alapértelmezett szolgáltatástulajdonságokat:

* A replikakészlet-számlálót az aktor adatmegőrzési attribútumhatározza meg. Minden alkalommal, amikor a perzisztencia attribútum a szereplő módosítása, a replika készlet száma az alapértelmezett szolgáltatásdefiníció ennek megfelelően alaphelyzetbe.
* A partícióséma és tartománya egységes Int64-re van állítva a teljes Int64 kulcstartománysal.

## <a name="service-fabric-partition-concepts-for-actors"></a>A Service Fabric partíciós fogalmai a szereplők számára
Az aktor szolgáltatások particionált állapotalapú szolgáltatások. Az aktorszolgáltatás minden partíciója egy szereplőkészletet tartalmaz. A szolgáltatáspartíciók automatikusan elvannak osztva a Service Fabric több csomópontján. Ennek eredményeképpen az aktorpéldányok kerülnek elosztásra.

![Aktor particionálása és elosztása][5]

Megbízható szolgáltatások különböző partíciósémákkal és partíciókulcs-tartományokkal hozhatók létre. Az aktor szolgáltatás az Int64 particionálási sémát használja a teljes Int64 kulcstartománysal a szereplők partíciókra való leképezéséhez.

### <a name="actor-id"></a>Aktor azonosítója
A szolgáltatásban létrehozott minden egyes aktor hoz létre egy `ActorId` egyedi azonosítót társított, az osztály által képviselt. `ActorId`egy átlátszatlan azonosítóérték, amely a szereplők egységes elosztására használható a szolgáltatáspartíciók között véletlenszerű azonosítók létrehozásával:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Minden `ActorId` kivonatolt egy Int64. Ez az oka annak, hogy az aktor szolgáltatásnak egy Int64 particionálási sémát kell használnia a teljes Int64 kulcstartománysal. Azonban egyéni azonosító értékek et használhat `ActorID`egy , beleértve a GUID/UUID-k, karakterláncok és Int64s.

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

GUIDs/UUID azonosítók és karakterláncok használata esetén az értékek egy Int64-re kerülnek. Ha azonban explicit módon int64-et ad `ActorId`meg egy, az Int64 további kivonatolás nélkül közvetlenül egy partícióra lesz leképezve. Ezzel a technikával szabályozhatja, hogy mely partíciók vannak a szereplők kerülnek.


## <a name="next-steps"></a>További lépések
* [Szereplő állapotkezelése](service-fabric-reliable-actors-state-management.md)
* [A szereplő életciklusa és a szemétgyűjtés](service-fabric-reliable-actors-lifecycle.md)
* [Actors API referenciadokumentáció](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [.NET mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

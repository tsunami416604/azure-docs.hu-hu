---
title: A Reliable Actors a Service Fabric |} A Microsoft Docs
description: Útmutatás a Reliable Actors vannak szintekre épülő a Reliable Services és a Service Fabric platformot szolgáltatásának használatához.
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
ms.openlocfilehash: f6594ccaa4d11361c3aacb8f774327600d3ffdde
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889250"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Így használja a Reliable Actors a Service Fabric platformot
Ez a cikk bemutatja, hogyan Reliable Actors működnek az Azure Service Fabric-platformon. A Reliable Actors futtatása egy keretrendszer, amely egy állapotalapú reliable Services megvalósítását üzemeltetett nevű a *aktorszolgáltatás*. Aktorszolgáltatás életciklus és a szereplők az elküldési üzenet kezeléséhez szükséges összes összetevőt tartalmazza:

* Az Actor-futtatókörnyezetben életciklusát, szemétgyűjtés, kezeli, és hozzáférést egyszálas.
* Egy aktor szolgáltatás távelérésének lehetővé tétele figyelő actors távelérési hívásokat fogadja, és elküldi őket egy, a megfelelő aktor irányíthatja dispatcher.
* Az Aktor Állapotszolgáltató állapotszolgáltatója (például a Reliable Collections állapotszolgáltató) burkolja, és egy adapter biztosít az aktor állapot-felügyeleti.

Ezek az összetevők együttesen alkotják a Reliable Actors keretrendszerben.

## <a name="service-layering"></a>Rétegezett szolgáltatás
Mivel az aktorszolgáltatás magát egy megbízható szolgáltatás összes a [alkalmazásmodell](service-fabric-application-model.md), életciklusának [csomagolási](service-fabric-package-apps.md), [üzembe helyezési](service-fabric-deploy-remove-applications.md), frissítés és skálázás fogalmait, a Reliable Szolgáltatások ugyanúgy aktorszolgáltatások a alkalmazni.

![Aktor service rétegezése][1]

A fentebbi ábra bemutatja azokat a kapcsolatot a Service Fabric-alkalmazás-keretrendszerek és a felhasználói kód között. Kék elemek képviselik a Reliable Services alkalmazás-keretrendszer, narancssárga jelöli a Reliable Actors keretrendszerben, és zöld felhasználói kódját jelöli.

A Reliable Services szolgáltatásban a szolgáltatás örökli a `StatefulService` osztály. Ez az osztály maga a osztályból származtatott `StatefulServiceBase` (vagy `StatelessService` állapotmentes szolgáltatások esetében). Reliable actors az aktor szolgáltatást használja. Aktorszolgáltatás egy másik megvalósítását a `StatefulServiceBase` az aktor minta, ahol egyidejűleg futtatják az actors implementáló osztályt. Mivel az aktorszolgáltatás maga implementációja csak `StatefulServiceBase`, írhat saját szolgáltatás származó `ActorService` és szolgáltatásszintű funkciók megvalósítása örökléskor ugyanúgy ugyanúgy `StatefulService`, például:

* Szolgáltatás biztonsági mentése és visszaállítása.
* Megosztott funkció az összes szereplő, például egy áramkör-megszakító.
* Távoli eljáráshívások az aktorszolgáltatás magát és minden egyes színész.

További információkért lásd: [szolgáltatásszintű funkciók megvalósítása az aktorszolgáltatás az](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Alkalmazásmodell
Aktor szolgáltatásokat Reliable Services, így a az alkalmazásmodell megegyezik. Azonban az aktor keretrendszer build tools gyűjtsön össze néhányat ezekből az alkalmazásfájlok modell az Ön számára.

### <a name="service-manifest"></a>Adatszolgáltatási jegyzékfájl
Az aktor keretrendszer build tools automatikus létrehozása az aktorszolgáltatás ServiceManifest.xml fájl tartalmát. Ez a fájl tartalmazza:

* Szereplőtípus szolgáltatás. Název typu jön létre az aktor projekt neve alapján. Az aktor az adatmegőrzés attribútum alapján, a HasPersistedState jelző nincs beállítva ennek megfelelően.
* Kódcsomag.
* Konfigurációs csomag.
* Erőforrások és a végpontok.

### <a name="application-manifest"></a>Alkalmazásjegyzék
Az aktor keretrendszer build tools automatikusan létrehoz egy alapértelmezett szolgáltatás definíciója az aktorszolgáltatás. Nástroje sestavení Pro töltse ki az alapértelmezett szolgáltatás tulajdonságai:

* Az adatmegőrzés attribútumot az aktor set száma határozza meg. Minden alkalommal, amikor megváltozik az adatmegőrzés attribútumot az aktor, a replika beállítása a alapértelmezett szolgáltatásdefiníció számolás ennek megfelelően.
* Partícióséma és a tartomány összes Int64 kulcs adathoz egységes Int64 beállítása.

## <a name="service-fabric-partition-concepts-for-actors"></a>Actors a Service Fabric-partíció fogalmai
Aktorszolgáltatások a particionált állapotalapú szolgáltatások. Mindegyik partíció az aktor Service actors készletét tartalmazza. Szolgáltatás partícióinak a rendszer automatikusan terjeszt a Service Fabric több csomópont feletti. Ennek eredményeképpen aktorpéldányok oszlanak meg.

![Particionálás aktor és terjesztése][5]

A Reliable Services partíció kulcstartományokkal és különböző partíciósémák hozható létre. Aktorszolgáltatás actors leképezése partíciók Int64 particionálási sémát használ a teljes Int64 kulcstartományhoz.

### <a name="actor-id"></a>Szereplő azonosítója
A szolgáltatásban létrehozott minden egyes színész rendelkezik egy egyedi azonosító tartozik, képviseli a `ActorId` osztály. `ActorId` érték nem átlátszó azonosítója, amely használható az actors egyenletes elosztása a szolgáltatáspartíciók véletlenszerű azonosítók létrehozásával:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Minden `ActorId` Int64, ezzel. Ezért az aktorszolgáltatás-Int64 particionálási sémát kell használnia, a teljes Int64 kulcstartományhoz. Azonban egyedi azonosító értékek használható egy `ActorID`, beleértve a GUID-ok/az UUID azonosítók, karakterláncok és Int64s.

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

GUID-ok/az UUID azonosítók és karakterláncokat használ, az értékek Int64 ezzel a rendszer. Azonban ha, explicit módon adja-e az Int64 egy `ActorId`, az Int64 lesz leképezve közvetlenül egy partíció további kivonatoláshoz nélkül. Ezzel a technikával szabályozhatja, hogy melyik partíciót az actors kerüljenek a is használhatja.


## <a name="next-steps"></a>További lépések
* [Aktor állapotkezelés](service-fabric-reliable-actors-state-management.md)
* [Actors-életciklus-kezelés és szemétgyűjtés gyűjtemény](service-fabric-reliable-actors-lifecycle.md)
* [Aktorok API dokumentációja](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [.NET mintakód](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-mintakód](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png

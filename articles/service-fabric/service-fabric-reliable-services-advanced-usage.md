---
title: "Speciális Reliable Services használata |} Microsoft Docs"
description: "Ismerje meg a szolgáltatások a hozzáadott rugalmasságot biztosít a Service Fabric Reliable Services speciális használatát."
services: Service-Fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: masnider
ms.assetid: f2942871-863d-47c3-b14a-7cdad9a742c7
ms.service: Service-Fabric
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: 48504f258b13a7ff5f4c91db2d9de09269e92424
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="advanced-usage-of-the-reliable-services-programming-model"></a>Speciális programozási modell a Reliable Services használata
Azure Service Fabric leegyszerűsíti az írást, és megbízható állapotmentes és állapotalapú szolgáltatások kezelésére. Ez az útmutató bemutat speciális Reliable Services ahhoz, hogy több irányítást és rugalmasságot a szolgáltatások keresztül is érvényesek. Jelen útmutató elolvasásával előtt ismerje meg a [a Reliable Services programozási modell](service-fabric-reliable-services-introduction.md).

Állapot-nyilvántartó és a állapotmentes szolgáltatások felhasználói kód a két elsődleges belépési pontok rendelkezik:

* `RunAsync(C#) / runAsync(Java)` egy általános célú belépési ponthoz, a szolgáltatás kódban van.
* `CreateServiceReplicaListeners(C#)` és `CreateServiceInstanceListeners(C#) / createServiceInstanceListeners(Java)` van a kommunikációs figyelőket az ügyféli kérelmek részére.

A legtöbb szolgáltatásokra ezek két belépési pontok elegendőek. Ritka esetekben a szolgáltatási életciklus teljesebb körű vezérlése szükség, ha további életciklus események állnak rendelkezésre.

## <a name="stateless-service-instance-lifecycle"></a>Állapotmentes szolgáltatások példány életciklusa
Egy állapotmentes szolgáltatások élettartama nagyon egyszerű. Egy állapotmentes szolgáltatások csak megnyitható, zárva, vagy megszakadt. `RunAsync` az állapotmentes szolgáltatások végrehajtása egy szolgáltatáspéldány megnyitásakor, és vonja vissza, ha egy szolgáltatáspéldány van zárva, vagy megszakadt.

Bár a `RunAsync` elegendőnek kell lennie a szinte minden esetben a meg van nyitva, zárja be, és a megszakítási események állapotmentes szolgáltatások is elérhetők:

* `Task OnOpenAsync(IStatelessServicePartition, CancellationToken) - C# / CompletableFuture<String> onOpenAsync(CancellationToken) - Java` OnOpenAsync nevezzük, amikor az állapot nélküli szolgáltatáspéldány használható. Kiterjesztett szolgáltatás inicializálási feladatokat jelenleg indítható.
* `Task OnCloseAsync(CancellationToken) - C# / CompletableFuture onCloseAsync(CancellationToken) - Java` OnCloseAsync nevezzük, amikor az állapot nélküli szolgáltatáspéldány érintetlen szabályosan le kell állítani. Ez akkor fordulhat elő, ha a szolgáltatás kód frissítés alatt áll, a szolgáltatáspéldány áthelyezik a terheléselosztás vagy egy átmeneti hiba észlelhető. OnCloseAsync segítségével biztonságosan zárjon be minden olyan erőforrásnál, állítsa le a háttérben történő feldolgozás, külső állapotmentést Befejezés vagy meglévő kapcsolatok le.
* `void OnAbort() - C# / void onAbort() - Java` Megszakításkor van meghívva, amikor az állapot nélküli szolgáltatáspéldány leállítása folyamatban van kényszerítve. Ez általában nevezzük, amikor egy állandó hiba lép fel a csomópont, vagy amikor a Service Fabric megbízhatóan nem tudja kezelni a szolgáltatáspéldány életciklus belső hiba miatt.

## <a name="stateful-service-replica-lifecycle"></a>Az állapotalapú szolgáltatás replika életciklusa

Az állapotalapú szolgáltatás replika élettartama sokkal bonyolultabb, mint egy állapot nélküli szolgáltatáspéldány. Ezen kívül nyissa meg, zárja be, és megszakítja az események, állapotalapú szolgáltatási replika megy keresztül szerepkör módosítások teljes élettartama alatt. Az állapotalapú szolgáltatás replika megváltozásakor szerepkör, a `OnChangeRoleAsync` esemény akkor váltódik ki:

* `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync módosításakor az állapotalapú szolgáltatási replika van szerepkör, például az elsődleges vagy másodlagos nevezik. Elsődleges replikára változott adta írási állapota (engedélyezettek létrehozásához és írásához megbízható gyűjteményeket). Másodlagos replikák adta olvasási állapota (csak Olvasás meglévő megbízható gyűjtemények). A legtöbb munkaelem egy állapotalapú szolgáltatás az elsődleges másodpéldány kellett végezni. Másodlagos replikák írásvédett érvényesítési, jelentéskészítésre, adatbányászat vagy más írásvédett feladatokat hajthat végre.

Az állapotalapú service-ben csak az elsődleges másodpéldány állapota legyen írási hozzáférése, és így nem általában amikor a szolgáltatás működik-e valódi munkát. A `RunAsync` az állapotalapú service metódus végrehajtása csak akkor, amikor a az állapotalapú szolgáltatási replika nem elsődleges. A `RunAsync` metódus megszűnik, ha egy elsődleges replika szerepkör vált elsődleges többet, valamint a zárja be és a megszakítási esemény során.

Használja a `OnChangeRoleAsync` esemény lehetővé teszi, attól függően, hogy a replika szerepkör, valamint hogy szerepkör módosítása feladatok végrehajtására.

Állapot-nyilvántartó szolgáltatás is biztosít a azonos szemantikáját, és a használati esetek állapotmentes szolgáltatásként azonos négy életciklus események:

```csharp
* Task OnOpenAsync(IStatefulServicePartition, CancellationToken)
* Task OnCloseAsync(CancellationToken)
* void OnAbort()
```

## <a name="next-steps"></a>További lépések
A Service Fabric kapcsolódó összetettebb témákra tekintse meg a következő cikkeket:

* [Állapotalapú Reliable Services konfigurálása](service-fabric-reliable-services-configuration.md)
* [A Service Fabric állapotának bemutatása](service-fabric-health-introduction.md)
* [Rendszerállapot-jelentések használata a hibaelhárításhoz](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
* [Szolgáltatások és a Service Fabric fürt erőforrás-kezelő konfigurálása](service-fabric-cluster-resource-manager-configure-services.md)

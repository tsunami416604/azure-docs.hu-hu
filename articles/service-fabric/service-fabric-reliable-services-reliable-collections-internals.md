---
title: "Azure Service Fabric megbízható állapotkezelője és megbízható gyűjtemény internals |} Microsoft Docs"
description: "Részletes bemutatója a megbízható gyűjtemény fogalmakat és az Azure Service Fabric Tervező."
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: rajak
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 5/1/2017
ms.author: mcoskun
ms.openlocfilehash: d607449a16e886337ab1bd96213fbb4231124353
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-service-fabric-reliable-state-manager-and-reliable-collection-internals"></a>Azure Service Fabric megbízható állapotkezelője és megbízható gyűjtemény belső funkciói
Ez a dokumentum delves belül megbízható állapotkezelője és megbízható gyűjtemények megjelenítéséhez kiadásának alapvető összetevői működése a háttérben.

> [!NOTE]
> Ez a dokumentum munkahelyi folyamatban. Ez a cikk adja meg, milyen további információ szeretné témakör megjegyzések hozzáadása.
>

##  <a name="local-persistence-model-log-and-checkpoint"></a>Helyi adatmegőrzési modell: naplóhoz és ellenőrzőpont
A megbízható állapotkezelője és megbízható hajtsa végre az adatmegőrzési modell, amely a naplóhoz és ellenőrzőpont nevezik.
Ebben a modellben minden állapotváltozás először jelentkezik be lemezt, és alkalmazza a memóriában.
A kész állapot maga is megőrződjenek csak esetenként (más néven Ellenőrzőpont).
Az az előnye, hogy eltérések szekvenciális csak írási műveleteket a lemezen a jobb teljesítmény be vannak kapcsolva.

Jobb megértése érdekében a naplóhoz és ellenőrzőpont modell, először nézzük a végtelen lemez forgatókönyv.
A megbízható állapotkezelője naplózza a minden műveletet a replikálás előtt.
Naplózás lehetővé teszi, hogy a megbízható gyűjtemény a művelet csak a memóriában.
Naplók megmaradnak, még akkor is, ha a replika nem sikerül, és újra kell indítani, mert a megbízható állapotkezelője elegendő információ áll rendelkezésre a naplót a műveleteket a replika elvesztette visszajátszani van.
Amennyiben a lemez végtelen, naplórekordokat soha nem kell őket távolítani, és csak a memórián belüli állapot kezelése szükség a megbízható gyűjtemény.

Most már a véges lemez forgatókönyv vizsgáljuk meg.
Naplórekordok gyűlik össze, mivel a megbízható állapotkezelője nincs elég szabad lemezterület fog futni.
Mielőtt ebben az esetben a megbízható állapotkezelője kell csonkolja a naplót, hogy helyet biztosítsanak az újabb bejegyzéseket.
Megbízható állapotkezelője kérelmek ellenőrzőpont megbízható gyűjtemények azok a memóriában lemezre.
Ezen a ponton a megbízható gyűjtemények szeretné megőrizni a memórián belüli állapotában.
Ha a megbízható gyűjtemények végezze el az ellenőrzőpontokat, a megbízható állapotkezelő is csonkolja a naplót, szabadítson fel megfelelő mennyiségű lemezterületet.
Ha a replika újra kell indítani, megbízható gyűjtemények helyreállításához alkulcsaihoz állapotukra és megbízható állapotkezelő állítja helyre, és az utolsó ellenőrzőpont óta történt összes állapotváltozások lejátssza.

Ellenőrzőpontok hozzáadása egy másik értéket, hogy ez növeli a gyakori forgatókönyvek helyreállítási alkalommal. A napló tartalmazza az összes művelet történt, mert az utolsó ellenőrzőpont.
Így például a megadott sor több értéket elem több verziója tartalmazhat megbízható szótárban.
Ezzel szemben, a megbízható gyűjtemény ellenőrzőpontokat kulcsok minden egyes érték csak a legújabb verzióra.

## <a name="next-steps"></a>Következő lépések
* [Tranzakciók és a zárolásokat](service-fabric-reliable-services-reliable-collections-transactions-locks.md)


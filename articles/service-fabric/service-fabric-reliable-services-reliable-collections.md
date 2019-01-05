---
title: Az állapotalapú szolgáltatások az Azure Service Fabric megbízható gyűjtemények bemutatása |} A Microsoft Docs
description: A Service Fabric állapotalapú szolgáltatások adja meg a megbízható gyűjtemények, amelyek lehetővé teszik, hogy magas rendelkezésre álló, skálázható és közel valós idejű felhőbeli alkalmazásokat készíthet.
services: service-fabric
documentationcenter: .net
author: tylermsft
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: twhitney
ms.openlocfilehash: 422b4bbcfc6811cdc6bbf1649e2c660d04d95776
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039673"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>A Reliable Collections Azure Service Fabric állapotalapú szolgáltatások bemutatása

A Reliable Collections lehetővé teszi magas rendelkezésre álló, skálázható és közel valós idejű felhőbeli alkalmazások írása, mintha egyetlen számítógépekhez készült alkalmazások írása közben. Az osztályok a **Microsoft.ServiceFabric.Data.Collections** névtér biztosítanak a gyűjteményeket, amelyek automatikusan az állapot magas rendelkezésre állásúvá tenni. A fejlesztők csak a megbízható gyűjtemény API-k programot, és hagyhatja, hogy a replikált és helyi állapot kezelése a Reliable Collections kell.

A Reliable Collections és egyéb magas rendelkezésre állású technológiák (például a Redis, az Azure Table Storage-szolgáltatás és az Azure Queue szolgáltatás) közötti fő különbség az, hogy az állapot maradjanak helyi szolgáltatáspéldány közben is magas rendelkezésre állású kerül sor. Ez azt jelenti, hogy:

* Olvasások helyi, aminek eredményeképpen közel valós idejű és nagy átviteli sebességű olvassa be.
* Az összes írási művelet merülnek fel a lehető legkevesebb hálózati IOS-, ami alacsony késést eredményez, és nagy átviteli sebességű írja.

![A gyűjtemények a fejlődést szem előtt tartva képe.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

A Reliable Collections is természetes továbbfejlődése értelmezhetők, a **System.Collections** osztályok: gyűjtemények, amelyek a felhő és a több számítógépes alkalmazások nélkül egyre összetettebb, az új készletét a fejlesztői. A Reliable Collections mint ilyen, a következők:

* Replikált: Változások lesznek replikálva, magas rendelkezésre állás érdekében.
* Megőrzött: Adatait a rendszer megőrzi a nagy méretű valamilyen okból kimaradás lép (például egy adatközpont áramkimaradás) ellen tartósság lemezre.
* Írási műveletek vannak tárolása és replikálása, mert egy ideiglenes ReliableDictionary, ReliableQueue vagy más megbízható gyűjteményt használ, amely csak a memóriában lévő adatot nem hozható létre.
* Aszinkron: API-k aszinkron, győződjön meg arról, hogy szál nem blokkolják a felmerülő IO.
* Tranzakciós: API-k használatára elvonása, tranzakciók, így az egy szolgáltatásban több a Reliable Collections könnyen kezelhetők.

A Reliable Collections adja meg a konzisztenciát garantál beépített könnyebbé tenni az indoklást alkalmazás állapotával kapcsolatos információkat.
Erős konzisztencia úgy, hogy a tranzakció-véglegesítések Befejezés csak azt követően a teljes tranzakció bejelentkezett a replikákat, beleértve az elsődleges kvóruma érhető el.
Gyengébb egységességének biztosítása érdekében, alkalmazások is igazolja vissza az ügyfél/kérelmező előtt az aszinkron véglegesítésű adja vissza.

A megbízható gyűjtemények API-k API-k egyidejű gyűjtemények továbbfejlesztett változata (megtalálható a **System.Collections.Concurrent** névtér):

* Aszinkron: Adja vissza egy feladatot, mivel egyidejű gyűjtemények, ellentétben a műveletek replikációja és megőrzi.
* Nem meghatározott paraméterek: Használja a `ConditionalValue<T>` vissza egy `bool` és paraméterek meg helyett egy értéket. `ConditionalValue<T>` hasonló `Nullable<T>` , de nincs szükség egy struct kell T.
* Tranzakciók: Ahhoz, hogy a felhasználót, hogy a csoport műveletek egy tranzakció több a Reliable Collections tranzakció objektumot használ.

Még ma **Microsoft.ServiceFabric.Data.Collections** tartalmaz a három gyűjteményben:

* [Megbízható szótárban](https://msdn.microsoft.com/library/azure/dn971511.aspx): Egy kulcs/érték párok replikált, tranzakciós és aszinkron gyűjteményét képviseli. Hasonló **ConcurrentDictionary**, a kulcs és az érték is bármilyen típusú lehet.
* [Megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx): A replikált, tranzakciós és aszinkron szigorú sorrendben érkezési idősorrendben történő (FIFO) várólistáját jelenti. Hasonló **ConcurrentQueue**, típustól függetlenül az érték lehet.
* [Megbízható egyidejű várólista](service-fabric-reliable-services-reliable-concurrent-queue.md): Egy replikált, tranzakciós és aszinkron ajánlott beavatkozást rendezése várólista magas átviteli sebességet jelöl. Hasonló a **ConcurrentQueue**, típustól függetlenül az érték lehet.

## <a name="next-steps"></a>További lépések

* [A Reliable Collections – irányelvek és javaslatok](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Adatok kezelése
  * [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
  * [Értesítések](service-fabric-reliable-services-notifications.md)
  * [Reliable Collections-szerializáció](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Szerializálási és frissítése](service-fabric-application-upgrade-data-serialization.md)
  * [A Reliable State Manager konfigurálása](service-fabric-reliable-services-configuration.md)
* Egyéb
  * [A Reliable Services – gyorsútmutató](service-fabric-reliable-services-quick-start.md)
  * [A Reliable Collections – fejlesztői referencia](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

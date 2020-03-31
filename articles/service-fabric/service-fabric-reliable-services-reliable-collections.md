---
title: Bevezetés a megbízható gyűjtemények
description: A Service Fabric állapotalapú szolgáltatásai megbízható gyűjteményeket biztosítanak, amelyek lehetővé teszik magas rendelkezésre állású, méretezhető és alacsony késésű felhőalkalmazások írását.
ms.topic: conceptual
ms.date: 1/3/2019
ms.openlocfilehash: 48fa682f4c017f66911729e1f581f3aa91cdc28d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75609723"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Bevezetés a megbízható gyűjtemények az Azure Service Fabric állapotalapú szolgáltatások

A Megbízható gyűjtemények lehetővé teszik, hogy magas rendelkezésre állású, méretezhető és alacsony késleltetésű felhőalapú alkalmazásokat írjon, mintha egyetlen számítógépes alkalmazásokat írna. A **Microsoft.ServiceFabric.Data.Collections** névtér osztályai olyan gyűjtemények készletét biztosítják, amelyek automatikusan magas rendelkezésre állásúvá teszik az állapotot. A fejlesztőknek csak a Megbízható gyűjtemény API-kat kell programozniuk, és lehetővé kell tenniük, hogy a Reliable Collections kezelje a replikált és a helyi állapotot.

A megbízható gyűjtemények és más magas rendelkezésre állású technológiák (például a Redis, az Azure Table szolgáltatás és az Azure Queue szolgáltatás) közötti legfontosabb különbség az, hogy az állapot helyileg a szolgáltatáspéldányban van tárolva, miközben magas rendelkezésre állású váválik. Ez azt jelenti, hogy:

* Minden olvasás helyi, ami alacsony késést és nagy átviteli sebességű olvasások.
* Minden írási műveletek a hálózati IOs minimális száma, ami alacsony késést és nagy átviteli sebességű írási.

![A gyűjtemények evolúciójának képe.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Megbízható gyűjtemények lehet tekinteni, mint a természetes fejlődése **system.Collections** osztályok: egy új gyűjtemények, amelyek célja a felhő és a több számítógépes alkalmazások növelése nélkül bonyolultak a fejlesztő számára. Mint ilyen, megbízható gyűjtemények a következők:

* Replikált: Az állapotmódosítások replikálódnak a magas rendelkezésre állás érdekében.
* Tartós: Az adatok megmaradnak a lemezen a nagy méretű kimaradások (például egy adatközpont áramkimaradások) tartóssága érdekében.
* Mivel az írási műveletek megmaradnak és replikálódnak, nem hozhat létre olyan felejtő ReliableDictionary, ReliableQueue vagy más megbízható gyűjteményt, amely csak a memóriában lévő adatokat őrizi meg.
* Aszinkron: Az API-k aszinkron, hogy a szálak nem blokkolja, ha az IO felmerülő.
* Tranzakciós: API-k a tranzakciók absztrakcióját használják, így egy szolgáltatáson belül több megbízható gyűjtemény tkezelhet.

A Megbízható gyűjtemények erős konzisztenciagaranciákat biztosítanak a dobozból, hogy megkönnyítsék az alkalmazásállapotával kapcsolatos érvelést.
Az erős konzisztencia csak akkor érhető el, ha a tranzakcióvéglegesítések csak akkor fejeződnek be, ha a teljes tranzakció a replikák többségi kvórumába lett bejelentkezve, beleértve az elsődlegest is.
A gyengébb konzisztencia elérése érdekében az alkalmazások nyugtázhatják vissza az ügyfél/kérelmező előtt az aszinkron véglegesítési hozamok.

A Megbízható gyűjtemények API-k az egyidejű gyűjtemények API-jainak (a **System.Collections.Concurrent** névtérben található) evolúciója:

* Aszinkron: Egy feladatot ad vissza, mivel az egyidejű gyűjteményekkel ellentétben a műveletek replikálódnak és megmaradnak.
* Nincs kimenő paraméter: `ConditionalValue<T>` A `bool` és egy érték visszaadása a paraméterek helyett. `ConditionalValue<T>`olyan, `Nullable<T>` mint, de nem követeli meg, hogy T egy struct legyen.
* Tranzakciók: Tranzakcióobjektum segítségével lehetővé teszi a felhasználó számára, hogy műveleteket csoportosítson egy tranzakcióban több megbízható gyűjteményen.

Ma a **Microsoft.ServiceFabric.Data.Collections** három gyűjteményt tartalmaz:

* [Megbízható szótár:](https://msdn.microsoft.com/library/azure/dn971511.aspx)A kulcs-/értékpárok replikált, tranzakciós és aszinkron gyűjteményét jelöli. A **ConcurrentDictionary**függvényhez hasonlóan a kulcs és az érték is lehet bármilyen típusú.
* [Megbízható várólista:](https://msdn.microsoft.com/library/azure/dn971527.aspx)Replikált, tranzakciós és aszinkron szigorú első be- és első kimenő (FIFO) várólistát jelöl. A **ConcurrentQueue**függvényhez hasonlóan az érték bármilyen típusú lehet.
* [Megbízható egyidejű várólista:](service-fabric-reliable-services-reliable-concurrent-queue.md)Replikált, tranzakciós és aszinkron legjobb erőfeszítés rendelési várólistát jelöl a nagy átviteli sebességű érdekében. A **ConcurrentQueue függvényhez**hasonlóan az érték bármilyen típusú lehet.

## <a name="next-steps"></a>További lépések

* [Megbízható gyűjtési irányelvek & ajánlások](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Adatok kezelése
  * [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
  * [Értesítések](service-fabric-reliable-services-notifications.md)
  * [Reliable Collections-szerializáció](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Szerializálás és frissítés](service-fabric-application-upgrade-data-serialization.md)
  * [Megbízható állapotkezelő-konfiguráció](service-fabric-reliable-services-configuration.md)
* Egyéb
  * [Megbízható szolgáltatások – gyorsindítás](service-fabric-reliable-services-quick-start.md)
  * [Fejlesztői referencia a megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

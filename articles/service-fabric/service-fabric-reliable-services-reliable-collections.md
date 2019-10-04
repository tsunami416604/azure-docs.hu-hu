---
title: A megbízható gyűjtemények bemutatása az Azure Service Fabric állapot-nyilvántartó szolgáltatásokban | Microsoft Docs
description: Service Fabric állapot-nyilvántartó szolgáltatások megbízható gyűjteményeket biztosítanak, amelyek lehetővé teszik a nagyfokú rendelkezésre állású, méretezhető és kis késleltetésű felhőalapú alkalmazások írását.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 1/3/2019
ms.author: atsenthi
ms.openlocfilehash: a7b30003fd02f8ab2e367311cdb3f56c80dbb4b2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599269"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>A megbízható gyűjtemények bemutatása az Azure Service Fabric állapot-nyilvántartó szolgáltatásokban

A megbízható gyűjtemények lehetővé teszik a kiválóan elérhető, méretezhető és kis késleltetésű felhőalapú alkalmazások írását, mintha egyetlen számítógépes alkalmazást írna. A **Microsoft. ServiceFabric.** Rea. Collections névtér osztályai olyan gyűjteményeket biztosítanak, amelyek automatikusan teszik elérhetővé az állapotot. A fejlesztőknek csak a megbízható gyűjtemény API-khoz kell programozniuk, és lehetővé kell tenniük a megbízható gyűjtemények számára a replikált és a helyi állapot kezelését.

A megbízható gyűjtemények és más, magas rendelkezésre állású technológiák (például a Redis, az Azure Table service és az Azure Queue szolgáltatás) közötti fő különbség az, hogy az állam a szolgáltatási példányon helyileg marad, és magas rendelkezésre állású. Ez azt jelenti, hogy:

* Minden olvasás helyi, ami alacsony késést és nagy adatátviteli sebességet eredményez.
* Minden írás a hálózati IOs minimális számát vonja maga után, ami alacsony késést és nagy adatátviteli sebességet eredményez.

![Gyűjtemények alakulásának képe.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

A megbízható gyűjtemények olyanok, mint a rendszer természetes fejlődése **. gyűjtemények** osztályok: új gyűjtemények, amelyek a Felhőbeli és a többszámítógépes alkalmazások számára lettek kialakítva, a fejlesztő összetettségének növelése nélkül. Így a megbízható gyűjtemények a következők:

* Replikált Az állapot módosításait a rendszer replikálja a magas rendelkezésre állás érdekében.
* Kitartott Az adatmennyiség a nagy léptékű leállások (például egy adatközpont-áramkimaradás) miatt tartósan tartós lemez.
* Mivel az írások megmaradnak és replikálódnak, nem hozhat létre olyan illékony ReliableDictionary, ReliableQueue vagy más megbízható gyűjteményt, amely csak a memóriában tárolt adatmennyiséget őrzi meg.
* Aszinkron Az API-k aszinkron módon biztosítják, hogy a szálak ne legyenek letiltva az i/o-műveletek során.
* Tranzakciós Az API-k a tranzakciók absztrakcióját használják, így egyszerűen kezelhet több megbízható gyűjteményt egy szolgáltatáson belül.

A megbízható gyűjtemények erős konzisztencia-garanciát biztosítanak az alkalmazás állapotának megkönnyítése érdekében.
Az erős konzisztencia úgy érhető el, hogy a tranzakciók véglegesítését csak akkor hajtja végre, ha a teljes tranzakciót a replikák többségi kvórumán naplózták, beleértve az elsődlegest is.
A gyengébb konzisztencia elérése érdekében az alkalmazások visszaigazolják az ügyfél/kérelmező számára az aszinkron véglegesítés visszatérését.

A megbízható gyűjtemények API-k az egyidejű gyűjtemények API-k (a **System. Collections. párhuzamos** névtér) fejlődése:

* Aszinkron Egy feladatot ad vissza, mivel a párhuzamos gyűjteményektől eltérően a rendszer replikálja és megőrzi a műveleteket.
* Nincsenek paraméterek: `ConditionalValue<T>` A egy`bool` és egy értéket ad vissza paraméterek helyett. `ConditionalValue<T>`hasonló `Nullable<T>` , de nem igényel T-t struct-ként.
* Tranzakciók Egy tranzakciós objektumot használ, amely lehetővé teszi a felhasználó számára, hogy egy tranzakcióban több megbízható gyűjteménybe csoportosítsa a műveleteket.

Ma a **Microsoft. ServiceFabric.** Recollections. Collections három gyűjteményt tartalmaz:

* [Megbízható szótár](https://msdn.microsoft.com/library/azure/dn971511.aspx): A kulcs/érték párok replikált, tranzakciós és aszinkron gyűjteményét jelöli. A **ConcurrentDictionary**hasonlóan a kulcs és az érték is bármilyen típusú lehet.
* [Reliable Queue](https://msdn.microsoft.com/library/azure/dn971527.aspx): A replikált, tranzakciós és aszinkron, szigorú első-be, első kimenő (FIFO) várólistát jelöli. A **ConcurrentQueue**hasonlóan az érték bármilyen típusú lehet.
* [Megbízható párhuzamos üzenetsor](service-fabric-reliable-services-reliable-concurrent-queue.md): Egy replikált, tranzakciós és aszinkron, a legjobb teljesítményt biztosító rendezési várakozási sort jelöl a nagy átviteli sebesség érdekében. A **ConcurrentQueue**hasonlóan az érték bármilyen típusú lehet.

## <a name="next-steps"></a>További lépések

* [Megbízható gyűjteményekre vonatkozó irányelvek & javaslatok](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Tranzakciók és zárolások](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Az adatkezelés
  * [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
  * [Értesítések](service-fabric-reliable-services-notifications.md)
  * [Reliable Collections-szerializáció](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Szerializálás és frissítés](service-fabric-application-upgrade-data-serialization.md)
  * [Megbízható állapot-kezelő konfigurációja](service-fabric-reliable-services-configuration.md)
* Továbbiak
  * [Reliable Services – első lépések](service-fabric-reliable-services-quick-start.md)
  * [Fejlesztői referenciák megbízható gyűjteményekhez](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

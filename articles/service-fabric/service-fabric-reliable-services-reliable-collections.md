---
title: Az Azure Service Fabric állapotalapú szolgáltatások megbízható gyűjteményeinek bemutatása |} Microsoft Docs
description: A Service Fabric állapotalapú szolgáltatások adja meg a megbízható gyűjteményeket, amelyek lehetővé teszik a magas rendelkezésre álló, méretezhető és alacsony késésű felhőalapú alkalmazásokhoz írni.
services: service-fabric
documentationcenter: .net
author: mcoskun
manager: timlt
editor: masnider,rajak,zhol
ms.assetid: 62857523-604b-434e-bd1c-2141ea4b00d1
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/6/2017
ms.author: mcoskun
ms.openlocfilehash: 2876d90c02995394104009d1b2d62d5b3ed6a8d9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212925"
---
# <a name="introduction-to-reliable-collections-in-azure-service-fabric-stateful-services"></a>Az Azure Service Fabric állapotalapú szolgáltatások megbízható gyűjteményeinek bemutatása
Megbízható gyűjtemények lehetővé teszik a magas rendelkezésre álló, méretezhető és alacsony késésű felhőalapú alkalmazások írását, mintha egyetlen számítógép alkalmazások írása. Az osztályok a **Microsoft.ServiceFabric.Data.Collections** névtér kínálnak gyűjteményeket, amelyek automatikusan a állapot magas rendelkezésre állású legyen. A fejlesztők csak a megbízható gyűjtemény API-k programot, és lehetővé teszik a replikált és helyi állapot kezelése megbízható gyűjteményeket kell.

A fő különbség megbízható gyűjtemények és egyéb magas rendelkezésre állású technológiák (például a Redis, Azure-tábla és Azure Queue szolgáltatás), hogy az állapot maradjanak helyileg a szolgáltatáspéldány közben is alatt álló magas rendelkezésre állású. Ez azt jelenti, hogy:

* Minden olvasási helyi, ennek eredményeképpen a kis késés, és nagy átviteli olvassa be.
* Összes írt fel Önnek a minimális száma hálózati IOs, amely kis késleltetésű eredményez, és nagy átviteli írja.

![A gyűjtemények alakulása képe.](media/service-fabric-reliable-services-reliable-collections/ReliableCollectionsEvolution.png)

Megbízható gyűjtemények-re, a természetes alakulása a **System.Collections** osztályok: új készletét gyűjteményeket, amelyek a felhőalapú és több számítógép alkalmazásokkal összetettsége növelése a fejlesztő nélkül. Megbízható gyűjteményeket, a következők:

* Replikált: Állapot történt változások lesznek replikálva a magas rendelkezésre állás érdekében.
* Megőrzött: Adatok megőrzött lemezre az adatok tartóssága elleni nagyméretű kimaradások (például egy adatközpont áramkimaradás esetén).
* Aszinkron: API-jainak aszinkron annak érdekében, hogy szálak nem blokkolják a IO kiléphessen.
* Tranzakciós: API-k használata elvonása tranzakciók, így könnyedén kezelhetők a szolgáltatáson belül több megbízható gyűjtemény.

Megbízható gyűjtemények adja meg az erős konzisztencia biztosítja, hogy az alkalmazás állapotával kapcsolatos információkat indoklást megkönnyíti jelölőnégyzetet kívül.
Az erős konzisztencia biztosítja a tranzakció véglegesítése Befejezés csak a teljes tranzakció bejelentkezve replikákat, ideértve a elsődleges többsége másodlagosak után érhető el.
Gyengébb konzisztencia eléréséhez alkalmazások is igazolja vissza az ügyfél/igénylő előtt az aszinkron véglegesítésű adja vissza.

A megbízható gyűjtemények API-jainak egy alakulása egyidejű gyűjtemények API-k (megtalálható a **System.Collections.Concurrent** névtér):

* Aszinkron: Ad vissza egy feladatot, mivel a műveletek replikálásra, megőrzött eltérően egyidejű gyűjtemények.
* Nincs kimeneti paramétere: használ `ConditionalValue<T>` adhatja vissza a logikai és a kimenő paraméterek ahelyett, hogy egy érték. `ConditionalValue<T>` mint `Nullable<T>` , de nincs szükség a Struct típusnak kell T.
* Tranzakciók: Egy transaction objektumnak használatával lehetővé teszik a felhasználónak a tranzakción belül több megbízható gyűjtemény műveleteit.

Napjainkban **Microsoft.ServiceFabric.Data.Collections** három gyűjteményeket tartalmazza:

* [Megbízható szótár](https://msdn.microsoft.com/library/azure/dn971511.aspx): a kulcs/érték párok replikált tranzakciós és aszinkron gyűjteményét képviseli. Hasonló **ConcurrentDictionary**, mind a kulcs és az érték bármilyen típusúak lehetnek.
* [Megbízható várólista](https://msdn.microsoft.com/library/azure/dn971527.aspx): a replikált tranzakciós és aszinkron szigorú érkezési sorrendben történő (FIFO) tárolását jelöli. Hasonló **ConcurrentQueue**, értéke lehet bármely típusú.
* [Megbízható egyidejű várólista](service-fabric-reliable-services-reliable-concurrent-queue.md): nagy átviteli sebesség eléréséhez várólista rendelési replikált tranzakciós és aszinkron legkedvezőbb jelöli. Hasonló a **ConcurrentQueue**, értéke lehet bármely típusú.

## <a name="next-steps"></a>További lépések
* [Megbízható gyűjtemény irányelvek és javaslatok](service-fabric-reliable-services-reliable-collections-guidelines.md)
* [A Reliable Collections használata](service-fabric-work-with-reliable-collections.md)
* [Tranzakciók és a zárolásokat](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
* Adatok kezelése
  * [Biztonsági mentés és visszaállítás](service-fabric-reliable-services-backup-restore.md)
  * [Értesítések](service-fabric-reliable-services-notifications.md)
  * [Reliable Collections-szerializáció](service-fabric-reliable-services-reliable-collections-serialization.md)
  * [Szerializálási és frissítése](service-fabric-application-upgrade-data-serialization.md)
  * [Megbízható állapot Manager konfigurálása](service-fabric-reliable-services-configuration.md)
* Egyéb
  * [Megbízható szolgáltatások – első lépések](service-fabric-reliable-services-quick-start.md)
  * [Fejlesztői leírás megbízható gyűjtemények](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

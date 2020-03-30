---
title: A Service Fabric-szolgáltatások elérhetősége
description: Hibaészlelés, feladatátvétel és egy szolgáltatás helyreállítása egy Azure Service Fabric-alkalmazásban.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 5306439184561e8dec8303a7b149f51d6c2f6e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551862"
---
# <a name="availability-of-service-fabric-services"></a>A Service Fabric-szolgáltatások elérhetősége
Ez a cikk áttekintést nyújt arról, hogy az Azure Service Fabric hogyan tartja fenn egy szolgáltatás rendelkezésre állását.

## <a name="availability-of-service-fabric-stateless-services"></a>A Service Fabric állapotmentes szolgáltatásainak elérhetősége
A Service Fabric-szolgáltatások lehetnek állapotalapúak vagy állapotnélküliek. Az állapotmentes szolgáltatás olyan alkalmazásszolgáltatás, amely nem rendelkezik [olyan helyi állapottal,](service-fabric-concepts-state.md) amelynek magas rendelkezésre állásúnak vagy megbízhatónak kell lennie.

Állapotnélküli szolgáltatás létrehozásához meg `InstanceCount`kell határozni a. A példányok száma határozza meg az állapotmentes szolgáltatás alkalmazáslogikájának a fürtben futó példányainak számát. A példányok számának növelése az ajánlott módja az állapotmentes szolgáltatás horizontális felskálázása.

Ha egy állapotnélküli nevű szolgáltatás példánya meghibásodik, egy új példány jön létre a fürt egy jogosult csomópontján. Előfordulhat például, hogy egy állapotmentes szolgáltatáspéldány meghibásodik a Node1-en, és újra létrejön az5-ös csomóponton.

## <a name="availability-of-service-fabric-stateful-services"></a>A Service Fabric állapotalapú szolgáltatásainak elérhetősége
Az állapotalapú szolgáltatáshoz egy állapot van társítva. A Service Fabric egy állapotalapú szolgáltatás replikák egy replikák modellezése. Minden replika a szolgáltatás kódjának futó példánya. A replika is rendelkezik egy másolatot az állam, hogy a szolgáltatás. Az olvasási és írási műveleteket egy kópián, az *Elsődleges*néven hajtják végre. Az írási műveletek állapotának módosításait a rendszer *replikálja* a replikakészlet többi replikájára, az *úgynevezett Aktív másodlagos állapotra,* és alkalmazza őket. 

Csak egy elsődleges replika lehet, de több aktív másodlagos replika is lehet. Az aktív másodlagos replikák száma konfigurálható, és a replikák nagyobb számú egyidejű szoftver- és hardverhiba is elviselhető.

Ha az elsődleges replika leáll, a Service Fabric az aktív másodlagos replikák egyikét az új elsődleges kópiát hozza. Ez az aktív másodlagos replika már rendelkezik az állapot frissített verziójával *replikáció*útján, és folytathatja a további olvasási/írási műveletek feldolgozását. Ezt a folyamatot *újrakonfigurálásnak nevezzük,* és az [újrakonfigurálási](service-fabric-concepts-reconfiguration.md) cikk további ismerteti.

A kópia elsődleges vagy aktív másodlagos fogalma a *replikaszerepkör*néven ismert. Ezeket a replikákat a [replikák és példányok](service-fabric-concepts-replica-lifecycle.md) című cikk ismerteti. 

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmairól az alábbi cikkekben talál további információt:

- [Szolgáltatásháló-szolgáltatások méretezése](service-fabric-concepts-scalability.md)
- [Particionálási szolgáltatás fabric szolgáltatások](service-fabric-concepts-partitioning.md)
- [Állapot meghatározása és kezelése](service-fabric-concepts-state.md)
- [Reliable Services](service-fabric-reliable-services-introduction.md)


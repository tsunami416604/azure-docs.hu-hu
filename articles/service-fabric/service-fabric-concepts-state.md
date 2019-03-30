---
title: Az Azure Service Fabric-szolgáltatások állapot kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan definiálása és kezelése a Service Fabric-szolgáltatások.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: e3ab36def2d210bd763f3ce2dc5df155e37e2dba
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670353"
---
# <a name="service-state"></a>Szolgáltatás állapota
**Szolgáltatás állapota** a memóriában vagy a lemez adatait, amely a függvény feltétele, hogy egy szolgáltatás hivatkozik. Tartalmaz, például az adattárakon és tagváltozók, amely a szolgáltatás olvassa be és ír működnek. Attól függően, hogy a szolgáltatás lett tervezve is tartalmazhat fájlokat vagy más erőforrásokat, amelyek tárolása a lemezen. Például a fájlok egy adatbázist használja adatok és a tranzakciós naplók tárolására.

Példa szolgáltatásként vegyünk számológépet. Egy egyszerű számológép szolgáltatás két számot vesz igénybe, és az összegük adja vissza. A számítást magában foglalja a nincs tagváltozók és egyéb adatait.

Tegyük fel, az azonos Számológép, de egy további módszerrel tárolására és az utolsó összeg visszaadó számított rendelkezik. Ez a szolgáltatás jelenleg állapot-nyilvántartó. Állapotalapú azt jelenti, hogy bizonyos állapot, amely kiszámítja az új összeg, és beolvassa a, amikor Ön feltesz, hogy az utolsó számított összegének visszaadása ír tartalmaz.

Az első szolgáltatás az Azure Service Fabric állapotmentes szolgáltatás neve. A második szolgáltatás egy állapotalapú szolgáltatás neve.

## <a name="storing-service-state"></a>Szolgáltatás állapotának tárolására.
Állam vagy externalized vagy a kóddal, amely az állapot akkor kezelésére szolgáló közös elhelyezésű. Állapot externalizációja általában egy külső adatbázisok vagy más adattárolóbeli tárolására, a hálózaton keresztül, vagy ugyanazon a gépen folyamaton kívül különböző gépeken futó használatával történik. A kalkulátor példánkban az adattár lehet egy SQL database vagy az Azure Table Store példányát. Minden kérelmet a sum számítási frissítést végez az adatokon, és a szolgáltatás arra kéri, hogy a store-ból beolvasott folyamatban van a jelenlegi érték eredmény érték visszaadása. 

Állapot elhelyezve a kódot, amely kezeli a állapotot is. A Service Fabric állapotalapú szolgáltatások általában beépített modell használatával. Győződjön meg arról, hogy ebben az állapotban magas rendelkezésre állású, egységes és tartós, és, hogy a szolgáltatások beépített így egyszerűen méretezhető infrastruktúrát biztosít a Service Fabric.

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmakról további információkért tekintse meg a következő cikkeket:

* [Service Fabric-szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
* [Service Fabric-szolgáltatások méretezhetősége](service-fabric-concepts-scalability.md)
* [Service Fabric-szolgáltatások particionálása](service-fabric-concepts-partitioning.md)
* [A Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md)

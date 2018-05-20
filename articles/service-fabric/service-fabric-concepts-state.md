---
title: Definine és az Azure mikroszolgáltatások állapot kezelése |} Microsoft Docs
description: Hogyan határozhatja meg és kezelheti a Service Fabric szolgáltatás állapota
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: f5e618a5-3ea3-4404-94af-122278f91652
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 46d2e27b9cdcb03213648982c7e9a0576838bc92
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="service-state"></a>Szolgáltatás állapota
**Szolgáltatás állapota** a memóriában vagy lemez-adatokat a szolgáltatás működéséhez szükséges. Ez magában foglalja, például adatstruktúrák és tagváltozók, amely a szolgáltatás olvas és ír működnek. Attól függően, hogy a szolgáltatás tervezett azt is tartalmazhatnak fájlokat vagy más erőforrásokat, amelyek tárolása a lemezen. Például a fájlok egy adatbázis használna adatok és a tranzakciós naplók tárolásához.

Példa szolgáltatásként Mérlegeljük, egy Számológép. Egy egyszerű számológép szolgáltatás időt vesz igénybe a két szám, és az összegét adja vissza. Magában foglalja a számítást, nem tagváltozók és egyéb adatait.

Most pedig nézzük meg az azonos Számológép, de egy további módszer tárolásához és az utolsó összeg visszaadó kiszámította. Ez a szolgáltatás jelenleg állapot-nyilvántartó. Állapotalapú alkalmazások és szolgáltatások azt jelenti, hogy néhány állapotba, ha kiszámítja új összege, és amikor kérje meg, hogy térjen vissza a legutóbbi számított összege olvas ír tartalmaz.

Az Azure Service Fabric az első szolgáltatás állapotmentes szolgáltatások neve. A második szolgáltatás egy állapotalapú szolgáltatás neve.

## <a name="storing-service-state"></a>Tárolja a szolgáltatás állapota
Állam vagy externalized vagy közösen elhelyezett a kódot, amely az az állapot kezelésére. Állapot externalization általában egy külső adatbázis vagy más adattárolóbeli tárolására, a hálózaton keresztül, vagy ugyanazon a számítógépen a folyamaton kívüli különböző gépeken futó használatával hajtható végre. A Számológép példánkban az adattár lehet egy SQL database vagy az Azure Table-tároló példányát. Az összeg kiszámításához kérelmek frissítést végez ezeken az adatokon, és a szolgáltatás arra kéri, hogy az érték eredményt az áruházból lehívott éppen aktuális értéke. 

Lehet, hogy állapota is együtt a kódot, amely kezeli a állapotát. Ez a modell használatával a Service Fabric állapotalapú szolgáltatások általában készített. Győződjön meg arról, hogy a magas rendelkezésre áll, következetes és tartós ebben az állapotban, és, hogy a szolgáltatások beépített ily módon egyszerűen méretezhető infrastruktúrát biztosít a Service Fabric.

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmakat további információkért tekintse meg a következő cikkeket:

* [A Service Fabric-szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
* [Méretezhetőséget biztosít a Service Fabric-szolgáltatások](service-fabric-concepts-scalability.md)
* [A Service Fabric szolgáltatások particionálás](service-fabric-concepts-partitioning.md)
* [Service Fabric megbízható szolgáltatások](service-fabric-reliable-services-introduction.md)

---
title: Állapot kezelése az Azure Service Fabric-szolgáltatásokban
description: Ismerje meg az Azure Service Fabric állapotát, többek között a Service Fabric-szolgáltatások szolgáltatásállapotának meghatározását és kezelését.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9acd3031d1071d1822791b333976aaf76161600f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614570"
---
# <a name="service-state"></a>Szolgáltatás állapota
**A szolgáltatás állapota** a szolgáltatás működéséhez szükséges memórián belüli vagy lemezadatokra utal. Ez magában foglalja például az adatstruktúrák és tag változók, amelyek a szolgáltatás beolvassa és írja a munkát. A szolgáltatás megtervezett állapotától függően tartalmazhat fájlokat vagy más, lemezen tárolt erőforrásokat is. Az adatbázis például az adatok és a tranzakciós naplók tárolására szolgáló fájlokat használja.

Példaként szolgáltatás, nézzük meg a számológép. Egy alapszámológép szolgáltatás két számot vesz igénybe, és visszaadja az összeget. A számítás végrehajtása nem tartalmaz tagváltozókat vagy egyéb információkat.

Most fontolja meg ugyanazt a számológépet, de egy további módszerrel az utolsó általa kiszámított összeg tárolására és visszaküldésére. Ez a szolgáltatás most állapotalapú. Állapotalapú azt jelenti, hogy tartalmaz néhány állapot, hogy azt írja, hogy amikor kiszámítja az új összeget, és olvas, amikor megkéri, hogy adja vissza az utolsó számított összeget.

Az Azure Service Fabric az első szolgáltatás neve állapotmentes szolgáltatás. A második szolgáltatás neve állapotalapú szolgáltatás.

## <a name="storing-service-state"></a>Szolgáltatási állapot tárolása
Az állapot kiszervezhető vagy az állapotot módosító kóddal együtt elhelyezhető. Az állapot kiszervezése általában egy külső adatbázis vagy más adattár használatával történik, amely különböző gépeken fut a hálózaton keresztül vagy ugyanazon a gépen folyamaton kívül. A számológép példában az adattár lehet egy SQL-adatbázis vagy az Azure Table Store példánya. Az összeg kiszámítására irányuló minden kérés frissítést hajt végre az adatokon, és kéri a szolgáltatástól az érték visszaadását, és az aktuális érték beolvasása az üzletből. 

Állam is lehet társ-elhelyezett a kódot, amely manipulálja az állam. Állapotalapú szolgáltatások a Service Fabric általában ezzel a modellel készült. A Service Fabric biztosítja az infrastruktúrát annak biztosítására, hogy ez az állapot magas rendelkezésre állású, konzisztens és tartós legyen, és hogy az így épített szolgáltatások könnyen méretezhetők legyenek.

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmairól az alábbi cikkekben talál további információt:

* [A Service Fabric-szolgáltatások elérhetősége](service-fabric-availability-services.md)
* [A Service Fabric-szolgáltatások méretezhetősége](service-fabric-concepts-scalability.md)
* [Particionálási szolgáltatás fabric szolgáltatások](service-fabric-concepts-partitioning.md)
* [Szolgáltatásháló megbízható szolgáltatásai](service-fabric-reliable-services-introduction.md)

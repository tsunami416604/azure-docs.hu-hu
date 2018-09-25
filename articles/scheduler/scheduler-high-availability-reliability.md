---
title: Magas rendelkezésre állás és megbízhatóság – Azure Scheduler szolgáltatásával
description: További tudnivalók a magas rendelkezésre állás és megbízhatóság az Azure Schedulerben
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: d647de379972bac317a213e2f8925c0ff8c3372c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947924"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Magas rendelkezésre állás és megbízhatóság az Azure Scheduler

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) kivezetjük, az Azure Scheduler lecseréli. Feladatok ütemezése [helyette próbálkozzon az Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Az Azure Scheduler biztosít a [magas rendelkezésre állású](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) és a megbízhatóság a feladatokhoz. További információkért lásd: [SLA-t a Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Magas rendelkezésre állás

Az Azure Scheduler [magas rendelkezésre állású] és a georedundáns szolgáltatás üzembe helyezésének és földrajzi régióhoz kötött feladat replikációt is használ.

### <a name="geo-redundant-service-deployment"></a>Georedundáns szolgáltatások üzembe helyezése

Az Azure Scheduler érhető el az Azure Portalon keresztül szinte [minden egyes földrajzi régióban, az Azure által jelenleg támogatott](https://azure.microsoft.com/global-infrastructure/regions/#services). Így ha egy üzemeltetett régióban az Azure-adatközpont elérhetetlenné válik, továbbra is használhatja az Azure Scheduler, mert a szolgáltatás feladatátvételt képességek elérhetővé a Scheduler egy másik adatközpontból.

### <a name="geo-regional-job-replication"></a>Replikációs feladat földrajzi régióhoz kötött

Az Azure Schedulerben a saját feladatok Azure-régióban replikálódnak. Így ha egy adott régióban leállás, az Azure Scheduler átadja a feladatokat, és gondoskodik arról, hogy a feladat fut egy másik adatközpontba a párosított földrajzi régióban.

Például ha egy feladatot hoz létre az USA déli középső Régiója, az Azure Scheduler automatikusan replikálja a feladat az USA északi középső Régiója. Ha hiba történik, az USA déli középső Régiója, az Azure Scheduler a feladatot futtat az USA északi középső Régiója. 

![Replikációs feladat földrajzi régióhoz kötött](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Az Azure Scheduler is biztosítja, hogy az adatok azonos, de szélesebb körű földrajzi régión belül marad, arra az esetre, ha hiba történik, az Azure-ban. Tehát nem rendelkezik ismétlődő feladatok, ha magas rendelkezésre állást szeretne. Az Azure Scheduler automatikusan magas rendelkezésre állást biztosít a feladatok.

## <a name="reliability"></a>Megbízhatóság

Az Azure Scheduler saját magas rendelkezésre állást garantál, de a felhasználó által létrehozott feladatok más módszer szükséges. Tegyük fel, hogy a feladat meghívja egy HTTP-végpontot, amely nem érhető el. Az Azure Scheduler továbbra is megkísérli a feladat sikeresen futott, így Ön alternatív módszerekkel hibák: 

* Állítsa be az újrapróbálkozási szabályzatok.
* Alternatív végpontokat beállítani.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Újrapróbálkozási szabályzatok

Az Azure Scheduler segítségével olyan újrapróbálkozási szabályzatok beállítása. Ha egy feladat sikertelen volt, majd alapértelmezés szerint a Scheduler a feladat négy még többször, 30 másodperces időközönként újrapróbálkozik. Az újrapróbálkozási szabályzat agresszívabb, például 10 alkalommal 30 másodperces időközönként, illetve a kevésbé agresszív, mint például két alkalommal a napi időközök teheti.

Tegyük fel például, létrehozhat egy feladatot, amely meghívja ezt egy HTTP-végpontot. Ha néhány órán keresztül, a feladat futtatásakor a HTTP-végpont nem érhető el, előfordulhat, hogy nem szeretné újra futtatni a feladatot, amely akkor fordul elő, mert az alapértelmezett újrapróbálkozási szabályzatát nem fognak működni ebben az esetben egy másik hét várjon. Így előfordulhat, hogy módosítani szeretné a szokásos újrapróbálkozási szabályzatot úgy, hogy az újrapróbálkozások fordulhat elő, ha például három óránként ahelyett, hogy ez lehet 30 másodperc. 

Ez az újrapróbálkozási szabályzat beállításával kapcsolatban lásd: [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Alternatív végpontokat

Ha az Azure Scheduler-feladat egy végpontot, amely nem érhető el, az újrapróbálkozási szabályzat a következő után is, a Feladatütemező áll vissza egy másik végpontot, amely képes kezelni az ilyen hibák. Tehát ha beállította ezt a végpontot, ütemező meghívja a, hogy a végpont, ami lehetővé teszi a saját feladatok magas rendelkezésre állású hibák bekövetkezése esetén.

Például ez az ábra bemutatja, hogyan Scheduler követi-e az újrapróbálkozási szabályzat a győri webszolgáltatás hívásakor. Ha az újrapróbálkozásokat meghibásodik, a Feladatütemező ellenőrzi egy másik végpontot. Ha már létezik a végpont, Scheduler elindítja a kérések küldését a másodlagos végpontot. Az azonos újrapróbálkozási szabályzat az eredeti művelet és a más műveletet is vonatkozik.

![A Scheduler viselkedését az újrapróbálkozási szabályzat és a másodlagos végpont](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

A művelet típusa, a másik művelet eltérhetnek az eredeti művelet. Például bár az eredeti művelet egy HTTP-végpontot hív meg, a másik művelet előfordulhat, hogy jelentkezzen hibák egy tárolási üzenetsort, Service Bus-üzenetsor vagy Service Bus témakör-műveletre.

Ismerje meg, hogyan állítható be egy másik végpontot, lásd: [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="see-also"></a>Lásd még

* [Mi az Azure Scheduler?](scheduler-intro.md)
* [Alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Komplex és speciális, ismétlődő ütemezések létrehozása](scheduler-advanced-complexity.md)
* [Korlátok, kvóták, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)

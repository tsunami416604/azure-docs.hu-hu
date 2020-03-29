---
title: Magas rendelkezésre állás és megbízhatóság
description: Ismerje meg a magas rendelkezésre állást és megbízhatóságot az Azure Scheduler ben
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 20c2054e168a9b17d9b4ab159cfefbf607ab6d11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898566"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Magas rendelkezésre állás és megbízhatóság az Azure Scheduler számára

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) felváltja az Azure Scheduler programot, [amelyet megszüntetnek.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Ha továbbra is szeretne dolgozni az Ütemezőben beállított feladatokkal, a lehető leghamarabb [telepítse át az Azure Logic Apps-alkalmazásokba.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Az Ütemező már nem érhető el az Azure Portalon, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagok](scheduler-powershell-reference.md) jelenleg elérhetők maradnak, így kezelheti a feladatokat és a feladatgyűjteményeket.

Az Azure Scheduler [magas rendelkezésre állást](https://docs.microsoft.com/azure/architecture/framework/#resiliency) és megbízhatóságot biztosít a feladatokhoz. További információ: [SLA for Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Magas rendelkezésre állás

Az Azure Scheduler [magas rendelkezésre állású], és georedundáns szolgáltatás telepítését és georegionális feladatreplikációt is használ.

### <a name="geo-redundant-service-deployment"></a>Georedundáns szolgáltatás telepítése

Az Azure Scheduler ma már szinte minden földrajzi régióban elérhető, [amelyet az Azure támogat.](https://azure.microsoft.com/global-infrastructure/regions/#services) Így ha egy üzemeltetett régióban egy Azure-adatközpont elérhetetlenné válik, továbbra is használhatja az Azure Scheduler-t, mert a szolgáltatás feladatátvételi képességei egy másik adatközpontból teszik elérhetővé a Scheduler-t.

### <a name="geo-regional-job-replication"></a>Területi alapú feladatreplikáció

Saját feladatok az Azure Scheduler replikálódik az Azure-régiókban. Így ha egy régió ban van egy kimaradás, az Azure Scheduler átadja a feladatát, és gondoskodik arról, hogy a feladat fut egy másik adatközponta a párosított földrajzi régióban.

Ha például létrehoz egy feladatot az USA déli középső részén, az Azure Scheduler automatikusan replikálja a feladatot az USA északi középső részén. Ha hiba történik az USA déli középső részén, az Azure Scheduler futtatja a feladatot az USA északi középső részén. 

![Területi alapú feladatreplikáció](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Az Azure Scheduler azt is gondoskodik arról, hogy az adatok ugyanabban a, de szélesebb földrajzi régióban maradnak, csak abban az esetben, ha hiba történik az Azure-ban. Így nem kell lemásolnia a munkáját, ha csak magas rendelkezésre állást szeretne. Az Azure Scheduler automatikusan magas rendelkezésre állást biztosít a feladatokhoz.

## <a name="reliability"></a>Megbízhatóság

Az Azure Scheduler garantálja a saját magas rendelkezésre állású, de más megközelítést alkalmaz a felhasználó által létrehozott feladatokhoz. Tegyük fel például, hogy a feladat meghívja a HTTP-végpont, amely nem érhető el. Az Azure Scheduler továbbra is megpróbálja sikeresen futtatni a feladatot azáltal, hogy alternatív módszereket biztosít a hibák kezelésére: 

* Állítsa be az újrapróbálkozási házirendeket.
* Alternatív végpontok beállítása.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Házirendek újrapróbálkozása

Az Azure Scheduler lehetővé teszi az újrapróbálkozási szabályzatok beállítását. Ha egy feladat sikertelen, akkor alapértelmezés szerint az Ütemező újrapróbálkozik a feladattal még négyszer 30 másodperces időközönként. Ezt az újrapróbálkozási házirendet agresszívebbé teheti, például 10-szer 30 másodperces időközönként, vagy kevésbé agresszív, például napi időközönként kétszer.

Tegyük fel például, hogy létrehoz egy heti feladatot, amely http-végpontot hív meg. Ha a HTTP-végpont néhány óráig elérhetetlenné válik a feladat futtatásakor, előfordulhat, hogy nem szeretne várni még egy hetet a feladat újbóli futtatására, ami azért történik, mert ebben az esetben az alapértelmezett újrapróbálkozási házirend nem fog működni. Ezért érdemes lehet módosítani a szabványos újrapróbálkozási házirendet, hogy az újrapróbálkozások például háromóránként történjenek, ne pedig 30 másodpercenként. 

Az újrapróbálkozási házirend beállításáról az [Újrapróbálkozási házirend című](scheduler-concepts-terms.md#retrypolicy)témakörben olvashat.

### <a name="alternate-endpoints"></a>Alternatív végpontok

Ha az Azure Scheduler-feladat olyan végpontot hív meg, amely nem érhető el, még az újrapróbálkozási szabályzat követése után is, az ütemező egy másik végpontra kerül, amely képes kezelni az ilyen hibákat. Tehát ha ezt a végpontot állítja be, az Ütemező meghívja azt a végpontot, ami a saját feladatok at magas rendelkezésre állású, ha hibák történnek.

Ez az ábra például azt mutatja be, hogy a Scheduler hogyan követi az újrapróbálkozási házirendet, amikor new yorki webszolgáltatást hív fel. Ha az újrapróbálkozások sikertelenek, az ütemező egy alternatív végpontot keres. Ha a végpont létezik, az Ütemező megkezdi a kérelmek küldését a másodlagos végpontra. Ugyanaz az újrapróbálkozási házirend az eredeti és az alternatív műveletre is vonatkozik.

![Az ütemező viselkedése újrapróbálkozási házirenddel és alternatív végponttal](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

Az alternatív művelet művelettípusa eltérhet az eredeti művelettől. Például, bár az eredeti művelet HTTP-végpontot hív meg, az alternatív művelet naplózhatja a hibákat egy Storage-várólista, a Service Bus-várólista vagy a Service Bus témakör művelet használatával.

Az alternatív végpontok beállításáról az [errorAction (Hibaművelet)](scheduler-concepts-terms.md#error-action)témakörben olvashat.

## <a name="next-steps"></a>További lépések

* [Alapfogalmak, terminológia és entitáshierarchia](scheduler-concepts-terms.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)
* [Korlátok, kvóták, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)

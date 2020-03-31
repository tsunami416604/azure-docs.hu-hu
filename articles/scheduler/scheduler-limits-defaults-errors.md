---
title: Korlátok, kvóták és küszöbértékek az Azure Schedulerben
description: Tudnivalók az Azure Scheduler korlátozásairól, kvótáiról, alapértelmezett értékeiről és szabályozási küszöbértékeiről
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 610232dab776648bb3dcc7c301ec292e9acad9fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898527"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Korlátok, kvóták és sávszélesség-szabályozási küszöbértékek az Azure Schedulerben

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) felváltja az Azure Scheduler programot, [amelyet megszüntetnek.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Ha továbbra is szeretne dolgozni az Ütemezőben beállított feladatokkal, a lehető leghamarabb [telepítse át az Azure Logic Apps-alkalmazásokba.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Az Ütemező már nem érhető el az Azure Portalon, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagok](scheduler-powershell-reference.md) jelenleg elérhetők maradnak, így kezelheti a feladatokat és a feladatgyűjteményeket.

## <a name="limits-quotas-and-thresholds"></a>Korlátok, kvóták és küszöbértékek

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id fejléc

Az Ütemező szolgáltatással szemben minden kérés **x-ms-request-id**nevű válaszfejlécet ad vissza. Ez a fejléc egy átlátszatlan értéket tartalmaz, amely egyedileg azonosítja a kérelmet. Ha tehát egy kérés következetesen sikertelen, és ön megerősítette, hogy a kérelem megfelelően van formázva, jelentheti a hibát a Microsoftnak az **x-ms-request-id** válasz fejlécértékének megadásával, és tartalmazza az alábbi adatokat: 

* Az **x-ms-request-id** érték
* A kérelem benyújtásának hozzávetőleges időpontja 
* Az Azure-előfizetés, a feladatgyűjtemény és a feladat azonosítói 
* A kérelem által megkísérelt művelet típusa

## <a name="next-steps"></a>További lépések

* [Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Csomagok és számlázás az Azure Scheduler számára](scheduler-plans-billing.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)
---
title: Korlátok, kvóták és küszöbértékek az Azure Schedulerben
description: Az Azure Scheduler korlátainak, kvótáinak, alapértelmezett értékeinek és szabályozási küszöbértékének megismerése
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: 293cd956f8270a4863fcc657f58c970096cec1e3
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300925"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Korlátok, kvóták és szabályozási küszöbértékek az Azure Schedulerben

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) az Azure Scheduler cseréje [folyamatban](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)van. Ha továbbra is szeretne dolgozni a Feladatütemezőben beállított feladatokkal, akkor a lehető leghamarabb [telepítse át Azure Logic apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) .

## <a name="limits-quotas-and-thresholds"></a>Korlátok, kvóták és küszöbértékek

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-MS-Request-ID fejléc

Minden, az ütemező szolgáltatással kapcsolatban benyújtott kérelem egy **x-MS-Request-ID**nevű válasz fejlécet ad vissza. Ez a fejléc egy átlátszatlan értéket tartalmaz, amely egyedileg azonosítja a kérést. Ha tehát a kérést a rendszer következetesen meghibásodik, és Ön megerősítette, hogy a kérés megfelelően formázott, a hibát a Microsoftnak az **x-MS-Request-ID** válasz fejlécének megadásával, valamint a következő részletekkel is jelezheti: 

* Az **x-MS-Request-ID** érték
* A kérelem teljesítésének becsült időpontja 
* Az Azure-előfizetéshez, a feladathoz és a feladathoz tartozó azonosítók 
* A kérelem által megkísérelt művelet típusa

## <a name="see-also"></a>Lásd még

* [Mi az Azure Scheduler?](scheduler-intro.md)
* [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)

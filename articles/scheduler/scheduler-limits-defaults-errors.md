---
title: Korlátok, kvóták és az Azure Schedulerben küszöbértékek
description: Információ a korlátok, kvóták, alapértelmezett értékeket, és az Azure Scheduler sávszélesség-szabályozási küszöbök
services: scheduler
ms.service: scheduler
author: WenJason
ms.author: v-jay
ms.reviewer: klam
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.topic: article
origin.date: 08/18/2016
ms.date: 11/12/2018
ms.openlocfilehash: 478afb20f3dabec74d66d00bec325408ce6604fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60531292"
---
# <a name="limits-quotas-and-throttle-thresholds-in-azure-scheduler"></a>Korlátok, kvóták és szabályozás küszöbértékek az Azure Schedulerben

> [!IMPORTANT]
> A kivezetésre kerülő Azure Scheduler helyébe az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) lép. Feladatok ütemezéséhez [próbálja ki inkább az Azure Logic Apps szolgáltatást](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

## <a name="limits-quotas-and-thresholds"></a>Korlátok, kvóták és küszöbértékek

[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="x-ms-request-id-header"></a>x-ms-request-id fejléccel

Felé irányuló a Scheduler szolgáltatás összes kérelmet adja vissza egy nevű válaszfejléc **x-ms-request-id**. Ezt a fejlécet, amely egyedileg azonosítja a kérés nem átlátszó értéket tartalmaz. Tehát egy kérelem folyamatosan meghiúsul, és Ön megerősítik, a kérelem megfelelően van formázva, jelentheti a hiba a Microsoft azáltal, hogy a **x-ms-request-id** válasz fejléc értéke, és ezeket az adatokat, beleértve: 

* A **x-ms-request-id** érték
* A becsült időt, ha a kérés érkezett 
* Az Azure-előfizetéssel, a feladatgyűjtemény és a feladat azonosítói 
* A kérelem kísérletet tevő művelet típusa

## <a name="see-also"></a>Lásd még

* [Mi az Azure Scheduler?](scheduler-intro.md)
* [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)

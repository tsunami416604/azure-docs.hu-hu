---
title: A Feladatütemező korlátozásai és alapértelmezett értéke
description: A Feladatütemező korlátozásai és alapértelmezett értéke
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 88f4a3e9-6dbd-4943-8543-f0649d423061
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: db6b1c196cb468f41c7a7ce34758de346b522abb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23867814"
---
# <a name="scheduler-limits-and-defaults"></a>A Feladatütemező korlátozásai és alapértelmezett értéke
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>A Feladatütemező kvóták, korlátok, alapértelmezett és szabályozások
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Az x-ms-request-id fejléc
Felé irányuló a Feladatütemező szolgáltatás összes kérelmet adja vissza egy nevű válaszfejléc**az x-ms-request-id**. Ezt a fejlécet, amely egyedileg azonosítja a kérelem nem átlátszó értéket tartalmaz.

Ha ellenőrizte, hogy a kérelem megfelelően formázott kérelmet következetesen sikertelen, jelentse a hibát a Microsoft ezt az értéket segítségével. A jelentés tartalmazza x-ms-request-id, a becsült időt, amely a kérés érkezett értékét, az előfizetés, feladatgyűjtemény, és/vagy feladat és a kérelem kísérletet tevő művelet típusú azonosítóját.

## <a name="see-also"></a>Lásd még:
 [A Scheduler ismertetése](scheduler-intro.md)

 [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)

 [Ismerkedés a Scheduler szolgáltatás Azure Portalon való használatával](scheduler-get-started-portal.md)

 [Csomagok és számlázás az Azure Schedulerben](scheduler-plans-billing.md)

 [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)

 [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)

 [Azure Scheduler – magas fokú rendelkezésre állás és megbízhatóság](scheduler-high-availability-reliability.md)

 [Kimenő hitelesítés az Azure Schedulerben](scheduler-outbound-authentication.md)


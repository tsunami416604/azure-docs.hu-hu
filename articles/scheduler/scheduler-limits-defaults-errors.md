---
title: Scheduler – korlátozások, és az alapértelmezett érték
description: Scheduler – korlátozások, és az alapértelmezett érték
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
ms.openlocfilehash: 4be0695402b66fdb2a027bfbada0e0b26e02d36f
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44378720"
---
# <a name="scheduler-limits-and-defaults"></a>Scheduler – korlátozások, és az alapértelmezett érték
## <a name="scheduler-quotas-limits-defaults-and-throttles"></a>A Scheduler kvótái, korlátai, alapértelmezései és szabályozások
[!INCLUDE [scheduler-limits-table](../../includes/scheduler-limits-table.md)]

## <a name="the-x-ms-request-id-header"></a>Az x-ms-request-id fejléc
Felé irányuló a Scheduler szolgáltatás összes kérelmet adja vissza egy nevű válaszfejléc **x-ms-request-id**. Ezt a fejlécet, amely egyedileg azonosítja a kérés nem átlátszó értéket tartalmaz.

Ha folyamatosan sikertelen kérelem, és ellenőrizte, hogy a kérés megfelelően megfogalmazása, jelentse a hibát a Microsoft felhasználhatja ezt az értéket. A jelentés tartalmazza az x-ms-request-id, azt a becsült időt, amely a kérés érkezett értékét, az előfizetés, a feladatgyűjteményt, és/vagy a feladatokat és a művelet, amely a kérés megpróbálta azonosítóját.

## <a name="see-also"></a>Lásd még:
 [A Scheduler ismertetése](scheduler-intro.md)

 [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)

 [Ismerkedés a Scheduler szolgáltatás Azure Portalon való használatával](scheduler-get-started-portal.md)

 [Csomagok és számlázás az Azure Schedulerben](scheduler-plans-billing.md)

 [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)

 [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)

 [Azure Scheduler – magas fokú rendelkezésre állás és megbízhatóság](scheduler-high-availability-reliability.md)

 [Kimenő hitelesítés az Azure Schedulerben](scheduler-outbound-authentication.md)


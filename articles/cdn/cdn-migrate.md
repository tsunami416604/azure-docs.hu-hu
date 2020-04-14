---
title: Az Azure CDN-profiljának áttelepítése a Verizon Standardról a Verizon Premium szolgáltatásra
description: Ismerje meg a profil Verizon Standard ról Verizon Premium ra való áttelepítésének részleteit.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: 61c472281c64fba451bb9b05f69ebee09bc763fd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260446"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Azure CDN-profil áttelepítése standard Verizonról prémium Verizonra

Amikor létrehoz egy Azure Content Delivery Network (CDN) profilt a végpontok kezeléséhez, az Azure CDN négy különböző terméket kínál, amelyek közül választhat. A különböző termékekről és azok elérhető funkcióiról az [Azure CDN termékfunkcióinak összehasonlítása](cdn-features.md)című témakörben talál további információt.

Ha létrehozott egy **Azure CDN Standard ot a Verizon-profilból,** és a CDN-végpontok kezelésére használja, lehetősége van arra, hogy frissítse azt egy **Azure CDN Premium-ra a Verizon-profilból.** A frissítés során a CDN-végpontok és az összes adat megőrződnek. 

> [!IMPORTANT]
> Miután frissített egy **Azure CDN Premium-ra a Verizon-profilból,** később nem konvertálhatja vissza **egy Azure CDN Standard-ra a Verizon-profilból.**
> 

Ha **egy Azure CDN-szabványt szeretne frissíteni a Verizon-profilból,** forduljon [a Microsoft támogatási szolgálatához.](https://azure.microsoft.com/support/options/)

## <a name="profile-comparison"></a>Profil összehasonlítása
**A Verizon-profilokból származó Azure CDN Premium** a következő kulcsbeli eltérésekkel rendelkezik a **Verizon-profilokból származó Azure CDN Standard szolgáltatástól:**
- Bizonyos Azure CDN-szolgáltatások, például [a tömörítés,](cdn-improve-performance.md) [a gyorsítótárazási szabályok](cdn-caching-rules.md)és a [geoszűrés,](cdn-restrict-access-by-country.md)nem használhatja az Azure CDN felületet, a **Kezelés** gombon keresztül kell használnia a Verizon portált.
- API: A Standard Verizonnal ellentétben az API nem szabályozhatja azokat a funkciókat, amelyek a Prémium Verizon portálról érhetők el. Az API-val azonban más gyakori funkciókat is szabályozhat, például végpont létrehozását/törlését, a gyorsítótárazott eszközök kiürítését/betöltését, valamint az egyéni tartomány engedélyezését/letiltását.
- Árképzés: A Premium Verizon más árképzési struktúrával rendelkezik az adatátvitelre, mint a Standard Verizon. További információt a [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/)című témakörben talál.

**A Verizon-profilokból származó Azure CDN Premium** a következő további funkciókkal rendelkezik:
- [Token hitelesítés:](cdn-token-auth.md)Lehetővé teszi a felhasználók számára, hogy szerezzenek be és használjanak egy jogkivonatot a biztonságos erőforrások beolvasásához.
- [Szabálymotor](cdn-rules-engine.md): Lehetővé teszi a HTTP-kérelmek kezelésének testreszabását.
- Fejlett elemzési eszközök:
   - [Részletes HTTP-elemzés](cdn-advanced-http-reports.md)
   - [Peremhálózati teljesítményelemzés](cdn-edge-performance.md)
   - [Valós idejű elemzés](cdn-real-time-alerts.md)


## <a name="next-steps"></a>További lépések
Ha többet szeretne megtudni a szabálymotorról, olvassa el az [Azure CDN-szabályok motorjának hivatkozását.](cdn-rules-engine-reference.md)


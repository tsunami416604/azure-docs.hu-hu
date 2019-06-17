---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66165464"
---
Fontos vegye figyelembe, hogy egy rendelkezésre állási csoport kérésfigyelőjének konfigurálása az Azure-ban két módja van. A módszer a figyelő létrehozásakor használja az Azure load balancer típusú térnek el egymástól. A következő táblázat ismerteti a különbségeket:

| Terheléselosztó típusa | Megvalósítás | Használat: |
| --- | --- | --- |
| **External** |Használja a *nyilvános virtuális IP-cím* a felhőalapú szolgáltatás, amely a virtuális gépek (VM) üzemelteti. |A figyelő a többek között az internetről, a virtuális hálózaton kívül eléréséhez szükséges. |
| **Belső** |Használja az *belső load balancer* a figyelő a magán-címmel. |A figyelő csak az adott virtuális hálózaton belül is elérheti. A site-to-site VPN hozzátartozik a hibrid környezetek. |

> [!IMPORTANT]
> Egy figyelő, amely a felhőszolgáltatás nyilvános virtuális IP-cím (külső terheléselosztó), az ügyfél lehető leghosszabbak, a figyelőt és adatbázisokat ugyanazon Azure-régióban, kimenő forgalmi díjat nem számolunk. Ellenkező esetben a figyelőt keresztül visszaadott adatok kimenő forgalomra számít, és azt, a normál adatátviteli díjszabás szerint számlázzuk. 
> 
> 

ILB csak a regionális hatókörrel rendelkező virtuális hálózatok használatával konfigurálható. Az affinitáscsoporthoz konfigurált meglévő virtuális hálózatok ILB nem használható. További információkért lásd: [belső load balancer áttekintése](../articles/load-balancer/load-balancer-internal-overview.md).


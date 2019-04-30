---
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097677"
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

<!-- Update_Description: update meta properties -->
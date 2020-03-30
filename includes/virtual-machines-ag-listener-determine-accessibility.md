---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179072"
---
Fontos felismerni, hogy két módon konfigurálhatja a rendelkezésre állási csoport figyelő az Azure-ban. A figyelő létrehozásakor használt Azure-terheléselosztó típusában különböznek. Az alábbi táblázat a különbségeket ismerteti:

| Terheléselosztó típusa | Megvalósítás | A következő esetekben használja: |
| --- | --- | --- |
| **Külső** |A virtuális gépeket (Virtuális gépeket) kiszolgáló felhőszolgáltatás *nyilvános virtuális IP-címét* használja. |A figyelőt a virtuális hálózaton kívülről kell elérnie, többek között az internetről is. |
| **Belső** |Belső *terheléselosztót* használ a figyelő privát címmel. |A figyelő csak ugyanabból a virtuális hálózatból érhető el. Ez a hozzáférés hibrid forgatókönyvekben tartalmazza a helyek közötti VPN-t. |

> [!IMPORTANT]
> A figyelő, amely a felhőszolgáltatás nyilvános VIP (külső terheléselosztó), mindaddig, amíg az ügyfél, a figyelő és az adatbázisok ugyanabban az Azure-régióban, nem merülnek fel kiterhelési díjakat. Ellenkező esetben a figyelőn keresztül visszaadott adatok kifelé, és normál adatátviteli díjakat számítunk fel. 
> 
> 

Az ILB csak regionális hatókörű virtuális hálózatokon konfigurálható. Az affinitáscsoporthoz konfigurált meglévő virtuális hálózatok nem használhatnak ILB-t. További információt a [Belső terheléselosztó áttekintéscímű témakörben](../articles/load-balancer/load-balancer-internal-overview.md)talál.


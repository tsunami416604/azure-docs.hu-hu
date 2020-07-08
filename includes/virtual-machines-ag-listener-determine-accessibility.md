---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8861396db6f6b680ddb55ce020e5579dc25b118e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67179072"
---
Fontos tisztában lenni azzal, hogy a rendelkezésre állási csoport figyelője kétféleképpen konfigurálható az Azure-ban. A módszer különbözik a figyelő létrehozásakor használt Azure Load Balancer típusával. Az alábbi táblázat a különbségeket ismerteti:

| Terheléselosztó típusa | Megvalósítás | A következő esetekben használja: |
| --- | --- | --- |
| **Külső** |A a virtuális gépeket üzemeltető felhőalapú szolgáltatás *nyilvános virtuális IP-címét* használja. |A figyelőt a virtuális hálózaton kívülről kell elérnie, beleértve az internetről is. |
| **Belső** |*Belső Load balancert* használ a figyelő magánhálózati címeivel. |A figyelőt csak ugyanazon a virtuális hálózaton belül érheti el. Ez a hozzáférés helyek közötti VPN-t tartalmaz hibrid forgatókönyvekben. |

> [!IMPORTANT]
> A Cloud Service nyilvános virtuális IP-címén (külső terheléselosztó) használó figyelő esetén, ha az ügyfél, a figyelő és az adatbázis ugyanabban az Azure-régióban található, nem számítunk fel kimenő forgalmi díjat. Ellenkező esetben a figyelő által visszaadott adatok a kimenő forgalomnak számítanak, és a normál adatátviteli díjszabás szerint számítjuk fel. 
> 
> 

Egy ILB csak regionális hatókörű virtuális hálózatokon lehet konfigurálni. Az affinitási csoporthoz konfigurált meglévő virtuális hálózatok nem használhatnak ILB. További információ: [belső Load Balancer – áttekintés](../articles/load-balancer/load-balancer-internal-overview.md).


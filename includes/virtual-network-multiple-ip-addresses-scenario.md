---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52270376"
---
## <a name="scenario"></a>Forgatókönyv
Egy virtuális Gépet egyetlen hálózati adapterrel létrehozott, és a egy virtuális hálózathoz csatlakozik. A virtuális gép van szükség, három különböző *privát* IP-címek és a két *nyilvános* IP-címeket. Az IP-címek vannak rendelve az alábbi IP-konfigurációk:

* **IP-konfiguráció – 1:** hozzárendel egy *statikus* magánhálózati IP-cím és a egy *statikus* nyilvános IP-címet.
* **IP-konfiguráció – 2:** hozzárendel egy *statikus* magánhálózati IP-cím és a egy *statikus* nyilvános IP-címet.
* **IP-konfiguráció – 3:** hozzárendel egy *statikus* magánhálózati IP-cím és a nyilvános IP-cím.
  
    ![Több IP-cím](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

Az IP-konfiguráció társítva, a hálózati adapterhez, ha a hálózati adapter létrehozása és a hálózati Adaptert a virtuális Géphez van csatolva a virtuális gép létrehozásakor. A forgatókönyvhöz használt IP-címek vannak az ábrán látható. Bármilyen IP cím és a hozzárendelés-típusok szükséges rendelhet hozzá.

> [!NOTE]
> A jelen cikkben ismertetett lépések rendeli IP-konfigurációk mindegyike egyetlen hálózati adapterre, bár minden hálózati adapternek a virtuális gép több hálózati Adapterrel is hozzárendelheti a több IP-konfiguráció. Több hálózati adapterrel rendelkező virtuális gép létrehozása, olvassa el a [több hálózati adapterrel rendelkező virtuális gép létrehozása](../articles/virtual-machines/windows/multiple-nics.md) cikk.
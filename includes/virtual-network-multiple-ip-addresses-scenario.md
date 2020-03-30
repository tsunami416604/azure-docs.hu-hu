---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179041"
---
## <a name="scenario"></a>Forgatókönyv
Egyetlen hálózati adapterrel rendelkező virtuális gép jön létre, és egy virtuális hálózathoz csatlakozik. A virtuális gép három különböző *privát* IP-címet és két *nyilvános* IP-címet igényel. Az IP-címek a következő IP-konfigurációkhoz vannak rendelve:

* **IPConfig-1:** Statikus *privát* IP-címet és *statikus* nyilvános IP-címet rendel hozzá.
* **IPConfig-2:** Statikus *privát* IP-címet és *statikus* nyilvános IP-címet rendel hozzá.
* **IPConfig-3:** *Statikus* privát IP-címet rendel hozzá, nyilvános IP-címet nem.
  
    ![Több IP-cím](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

Az IP-konfigurációk vannak társítva a hálózati adapter, amikor a hálózati adapter jön létre, és a hálózati adapter csatlakozik a virtuális gép, amikor a virtuális gép jön létre. A forgatókönyvhöz használt IP-címek típusai illusztrációk. Bármilyen IP-címet és hozzárendeléstípust hozzárendelhet.

> [!NOTE]
> Bár a jelen cikkben leírt lépések az összes IP-konfigurációt egyetlen hálózati adapterhez rendelik hozzá, több IP-konfigurációt is hozzárendelhet bármely hálózati adapterhez egy több hálózati adapterben egy több hálózati adapterben. Ha meg szeretné tudni, hogyan hozhat létre virtuális gép több hálózati adapterek, olvassa el a [virtuális gép létrehozása több hálózati adapterek](../articles/virtual-machines/windows/multiple-nics.md) cikket.
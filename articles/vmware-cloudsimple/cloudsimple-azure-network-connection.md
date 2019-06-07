---
title: VMware megoldást, CloudSimple – az Azure hálózati kapcsolatok
description: További tudnivalók az Azure virtuális hálózat csatlakoztatása az CloudSimple régió hálózathoz
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f2ab82b6c1b4b373c186019eaf96f9864861b9d9
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66497638"
---
# <a name="azure-network-connections-overview"></a>Azure-beli hálózati kapcsolatok áttekintése

Egy régióban egy CloudSimple szolgáltatás létrehozásakor azt:

* Létrehoz egy Azure ExpressRoute-kapcsolatcsoportot, és csatlakoztatja a szolgáltatás az adott régióban
* Lehetővé teszi, hogy a kapcsolat CloudSimple terület hálózat az Azure virtuális hálózat vagy a helyszíni hálózat az Azure ExpressRoute használatával
* Az Azure-előfizetés vagy a helyszíni hálózat, a Magánfelhő környezetből hozzáférési szolgáltatásokat nyújt

A kapcsolat állapota:

* Biztonságos
* Magánjellegű
* Nagy sávszélesség
* Közel valós idejű adatelérés

## <a name="benefits"></a>Előnyök

Az Azure hálózati kapcsolat teszi lehetővé:

* Az Azure-t a biztonsági mentési cél virtuális gépeknél a Private Cloud.
* A KMS-kiszolgálókat a Magánfelhő vSAN adattároló titkosítása az Azure-előfizetés telepítheti.
* Hibrid alkalmazások, amelyben a webes szint, az alkalmazás fut, amíg az alkalmazás a nyilvános felhőben, és a Magánfelhő futtathat adatbázisrétegeknek használja.

## <a name="azure-virtual-network-connection"></a>Az Azure virtuális hálózati kapcsolat

Magánfelhők létrehozása az Azure-erőforrások ExpressRoute-tal is csatlakoztathatók.  Használhatja ezt a kapcsolatot az Azure-előfizetésben a privát felhőben futó különböző erőforrások eléréséhez.  Ehhez a kapcsolathoz, a Magánfelhő-hálózatot az Azure virtuális hálózat kiterjesztése teszi lehetővé.

![Az Azure ExpressRoute-kapcsolat virtuális hálózathoz](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>A helyszíni hálózat az ExpressRoute-kapcsolat

A meglévő Azure ExpressRoute-kapcsolatcsoporttal csatlakozhat a CloudSimple régió. Globális elérhetőségű az ExpressRoute szolgáltatás használható egymással a két kapcsolatcsoporttal csatlakozhat.  A helyszíni és a CloudSimple ExpressRoute-Kapcsolatcsoportok között létrejön a kapcsolat.  Ez a kapcsolat kiterjesztheti helyszíni hálózatait a Magánfelhő hálózati teszi lehetővé.

![A helyi ExpressRoute-kapcsolat – globális elérhetőséggel](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>További lépések

* [Azure virtuális hálózat CloudSimple kapcsolatra társviszony-létesítési megszerzése](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [CloudSimple ExpressRoute-tal tudjon csatlakozni a helyszíni](https://docs.azure.cloudsimple.com/on-premises-connection)

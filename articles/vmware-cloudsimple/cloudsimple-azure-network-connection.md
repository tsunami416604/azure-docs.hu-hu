---
title: VMware-megoldás CloudSimple szerint – Azure hálózati kapcsolatok
description: Ismerje meg, hogyan csatlakoztathatja Azure-beli virtuális hálózatát a CloudSimple-régió hálózatához
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a8e99da05f71cb01744111b41c301b11a0969057
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812707"
---
# <a name="azure-network-connections-overview"></a>Az Azure hálózati kapcsolatainak áttekintése

Ha CloudSimple-szolgáltatást hoz létre egy régióban, akkor:

* Létrehoz egy Azure ExpressRoute áramkört, és csatolja az adott régióban található szolgáltatáshoz
* Lehetővé teszi a CloudSimple-régió hálózatról az Azure-beli virtuális hálózatra vagy a helyszíni hálózatra való kapcsolódást az Azure ExpressRoute használatával
* Hozzáférést biztosít az Azure-előfizetésben vagy a helyszíni hálózaton futó hozzáférési szolgáltatásokhoz a saját felhőalapú környezetből

A kapcsolatok a következőket jelentik:

* Biztonságos
* Magánjellegű
* Nagy sávszélesség
* Közel valós idejű adatelérés

## <a name="benefits"></a>Előnyök

Az Azure-beli hálózati kapcsolatok a következőket teszik lehetővé:

* Használja az Azure-t biztonsági mentési célként a saját felhőben lévő virtuális gépekhez.
* KMS-kiszolgálók üzembe helyezése az Azure-előfizetésben a saját felhőalapú vSAN-adattár titkosításához.
* Használjon olyan hibrid alkalmazásokat, amelyekben az alkalmazás webes szintje a nyilvános felhőben fut, miközben az alkalmazás és az adatbázis szintjei a saját felhőben futnak.

## <a name="azure-virtual-network-connection"></a>Azure-beli virtuális hálózati kapcsolatok

A privát felhők a ExpressRoute használatával csatlakoztathatók az Azure-erőforrásokhoz.  Ezzel a kapcsolattal elérheti az Azure-előfizetésében futó különböző erőforrásokat a privát felhőből.  Ez a kapcsolódás lehetővé teszi, hogy saját felhőalapú hálózatot terjesszen az Azure-beli virtuális hálózatra.

![Azure ExpressRoute-kapcsolódás virtuális hálózathoz](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute-kapcsolódás a helyszíni hálózathoz

A meglévő Azure ExpressRoute-áramkört a CloudSimple-régióhoz is összekapcsolhatja. A ExpressRoute Global Reach funkció a két áramkör egymással való összekapcsolására szolgál.  Létrejön egy kapcsolat a helyszíni és a CloudSimple ExpressRoute-áramkörök között.  Ez a kapcsolódás lehetővé teszi a helyszíni hálózatok kibővítését a saját felhőalapú hálózatra.

![Helyszíni ExpressRoute-kapcsolatok – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>További lépések

* [Az Azure Virtual Network CloudSimple-hez való kapcsolódási információinak beszerzése](https://docs.azure.cloudsimple.com/virtual-network-connection)
* [Kapcsolódás a helyszínről a CloudSimple a ExpressRoute használatával](https://docs.azure.cloudsimple.com/on-premises-connection)

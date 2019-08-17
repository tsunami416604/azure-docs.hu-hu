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
ms.openlocfilehash: eca3e316d866814f6727dd8ef2c3fa490a551383
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563166"
---
# <a name="azure-network-connections-overview"></a>Az Azure hálózati kapcsolatainak áttekintése

Ha CloudSimple-szolgáltatást hoz létre egy régióban, akkor:

* Létrehoz egy Azure ExpressRoute áramkört, és csatolja az adott régióban található szolgáltatáshoz.
* Az Azure ExpressRoute használatával összekapcsolja az CloudSimple-régió hálózatát az Azure-beli virtuális hálózattal vagy a helyszíni hálózattal.
* Hozzáférést biztosít az Azure-előfizetésben vagy a helyszíni hálózaton futó szolgáltatásokhoz a saját felhőalapú környezetből.

A ExpressRoute-kapcsolat nagy sávszélességű, kis késleltetésű.

## <a name="benefits"></a>Előnyök

Az Azure-beli hálózati kapcsolatok a következőket teszik lehetővé:

* Használja az Azure-t biztonsági mentési célként a saját felhőben lévő virtuális gépekhez.
* KMS-kiszolgálók üzembe helyezése az Azure-előfizetésben a saját felhőalapú vSAN-adattár titkosításához.
* Használjon olyan hibrid alkalmazásokat, amelyekben az alkalmazás webes szintje a nyilvános felhőben fut, miközben az alkalmazás és az adatbázis szintjei a saját felhőben futnak.

## <a name="azure-virtual-network-connection"></a>Azure-beli virtuális hálózati kapcsolatok

A privát felhők a ExpressRoute használatával csatlakoztathatók az Azure-erőforrásokhoz.  A ExpressRoute-kapcsolat lehetővé teszi, hogy hozzáférjen az Azure-előfizetésében futó erőforrásokhoz a privát felhőből.  Ezzel a kapcsolódással kiterjesztheti saját felhőalapú hálózatát az Azure-beli virtuális hálózatra.

[![Azure ExpressRoute-kapcsolódás virtuális hálózathoz](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute-kapcsolódás a helyszíni hálózathoz

A meglévő Azure ExpressRoute-áramkört a CloudSimple-régióhoz is összekapcsolhatja. A ExpressRoute Global Reach funkció a két áramkör egymással való összekapcsolására szolgál.  Létrejön egy kapcsolat a helyszíni és a CloudSimple ExpressRoute-áramkörök között.  Ez a kapcsolódás lehetővé teszi a helyszíni hálózatok kibővítését a saját felhőalapú hálózatra.

![Helyszíni ExpressRoute-kapcsolatok – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="next-steps"></a>További lépések

* [Azure-beli virtuális hálózat összekötése a CloudSimple a ExpressRoute használatával](virtual-network-connection.md)
* [Kapcsolódás a helyszínről a CloudSimple a ExpressRoute használatával](on-premises-connection.md)

---
title: VMware-megoldás a CloudSimple által – Azure hálózati kapcsolatok
description: További információ az Azure virtuális hálózatának a CloudSimple régióhálózathoz való csatlakoztatásáról
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025078"
---
# <a name="azure-network-connections-overview"></a>Az Azure hálózati kapcsolatainak áttekintése

Amikor létrehoz egy CloudSimple szolgáltatást egy régióban, és csomópontokat hoz létre, a következőket teheti:

* Kérjen egy Azure ExpressRoute-kapcsolati hálózatot, és csatolja az adott régióban lévő CloudSimple-hálózathoz.
* Az Azure ExpressRoute használatával csatlakoztathatja A CloudSimple régióhálózatát az Azure virtuális hálózatához vagy a helyszíni hálózathoz.
* Hozzáférést biztosít az Azure-előfizetésében vagy a helyszíni hálózatban futó szolgáltatásokhoz a privát felhőbeli környezetből.

Az ExpressRoute-kapcsolat nagy sávszélességű, alacsony késleltetéssel.

## <a name="benefits"></a>Előnyök

Az Azure hálózati kapcsolat lehetővé teszi, hogy:

* Az Azure-t biztonsági mentési célként használhatja a privát felhőben lévő virtuális gépekhez.
* KmS-kiszolgálók üzembe helyezése az Azure-előfizetésben a private cloud vSAN-adattár titkosításához.
* Olyan hibrid alkalmazásokat használjon, amelyeken az alkalmazás webes szintje a nyilvános felhőben fut, míg az alkalmazás- és adatbázisszintek a privát felhőben futnak.

## <a name="azure-virtual-network-connection"></a>Azure virtuális hálózati kapcsolat

A privát felhők az ExpressRoute használatával kapcsolódhatnak az Azure-erőforrásokhoz.  Az ExpressRoute-kapcsolat lehetővé teszi az Azure-előfizetésében futó erőforrások elérését a privát felhőből.  Ez a kapcsolat lehetővé teszi, hogy kiterjessze a magánfelhő-hálózatot az Azure virtuális hálózatára.  A CloudSimple hálózatról származó útvonalakat az Azure virtuális hálózatával a BGP-n keresztül cseréli ki.  Ha konfigurált a virtuális hálózati társviszony-létesítés, az összes társviszony-létesített virtuális hálózat elérhető lesz a CloudSimple hálózatról.

![Azure ExpressRoute-kapcsolat a virtuális hálózattal](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute-kapcsolat a helyszíni hálózattal

Meglévő Azure ExpressRoute-kapcsolata csatlakoztatható a CloudSimple-régióhoz. Az ExpressRoute Globális elérés szolgáltatás a két kapcsolat összekapcsolására szolgál.  A helyszíni és a CloudSimple ExpressRoute-áramkörök között létrejön a kapcsolat.  Ez a kapcsolat lehetővé teszi, hogy a helyszíni hálózatokat a magánfelhő-hálózatra is kiterjessze. A CloudSimple-hálózatról a BGP-n keresztül a helyszíni hálózattal váltunk át útvonalakat.

![Helyszíni ExpressRoute-kapcsolat – Globális elérés](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Csatlakozás helyszíni hálózathoz és Azure virtuális hálózathoz

A helyszíni hálózattal és az Azure virtuális hálózatával létesített kapcsolatok a CloudSimple-hálózatról is létezhetnek.  A kapcsolat bgp-t használ a helyszíni hálózat, az Azure virtuális hálózat és a CloudSimple-hálózat közötti útvonalak cseréjéhez.  Amikor globális elérésű kapcsolat jelenlétében csatlakoztatja a CloudSimple-hálózatot az Azure virtuális hálózatához, az Azure virtuális hálózati útvonalai láthatók lesznek a helyszíni hálózaton.  Útvonalcsere történik az Azure-ban a peremhálózati útválasztók között.

![Helyszíni ExpressRoute-kapcsolat az Azure virtuális hálózati kapcsolatával](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Fontos szempontok

A CloudSimple hálózathoz való csatlakozás a helyszíni hálózatról és az Azure virtuális hálózatáról lehetővé teszi az összes hálózat közötti útvonalcserét.

* Az Azure virtuális hálózat a helyszíni és a CloudSimple-hálózatról is látható lesz.
* Ha helyszíni hálózatról csatlakozott az Azure virtuális hálózatához, a Global Reach használatával a CloudSimple hálózathoz való csatlakozás lehetővé teszi a hozzáférést a CloudSimple hálózat virtuális hálózatához.
* Az alhálózati címek **nem fedhetik** át a csatlakoztatott hálózatokat.
* A CloudSimple **nem** hirdeti az alapértelmezett útvonalat az ExpressRoute-kapcsolatokhoz
* Ha a helyszíni útválasztó hirdeti az alapértelmezett útvonalat, a CloudSimple-hálózatról és az Azure virtuális hálózatról érkező forgalom a hirdetett alapértelmezett útvonalat fogja használni.  Ennek eredményeképpen az Azure virtuális gépei nem érhetők el nyilvános IP-címekkel.

## <a name="next-steps"></a>További lépések

* [Az Azure virtuális hálózatának csatlakoztatása a CloudSimple szolgáltatáshoz az ExpressRoute használatával](virtual-network-connection.md)
* [Csatlakozás a helyszíni és a CloudSimple szolgáltatáshoz az ExpressRoute használatával](on-premises-connection.md)

---
title: VMware-megoldások (AVS) – Azure hálózati kapcsolatok
description: Ismerje meg, hogyan csatlakoztathatja Azure-beli virtuális hálózatát az AVS region networkhez
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: bf11d4e2676179e8b71d3a03f8ed3cbcb4cfba9d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025078"
---
# <a name="azure-network-connections-overview"></a>Az Azure hálózati kapcsolatainak áttekintése

Ha egy régióban létrehoz egy AVS-szolgáltatást, és csomópontokat hoz létre, a következőket teheti:

* Igényeljen egy Azure ExpressRoute-áramkört, és csatolja az adott régióban található AVS-hálózathoz.
* Az Azure ExpressRoute használatával összekapcsolhatja az AVS-régió hálózatát az Azure-beli virtuális hálózattal vagy a helyszíni hálózattal.
* Hozzáférés biztosítása az Azure-előfizetésben vagy a helyszíni hálózaton futó szolgáltatásokhoz a saját felhőalapú környezetből.

A ExpressRoute-kapcsolat nagy sávszélességű, kis késleltetésű.

## <a name="benefits"></a>Előnyök

Az Azure-beli hálózati kapcsolatok a következőket teszik lehetővé:

* Használja az Azure-t biztonsági mentési célként a saját felhőben lévő virtuális gépekhez.
* KMS-kiszolgálók üzembe helyezése az Azure-előfizetésben a saját felhőalapú vSAN-adattár titkosításához.
* Használjon olyan hibrid alkalmazásokat, amelyekben az alkalmazás webes szintje az AVS nyilvános felhőben fut, miközben az alkalmazás és az adatbázis szintjei futnak a saját felhőben.

## <a name="azure-virtual-network-connection"></a>Azure-beli virtuális hálózati kapcsolatok

Az AVS privát felhők az ExpressRoute használatával csatlakoztathatók az Azure-erőforrásokhoz. Az ExpressRoute-kapcsolat lehetővé teszi az Azure-előfizetésében futó erőforrások elérését az AVS Private Cloud-ból. Ez a kapcsolódás lehetővé teszi az AVS Private Cloud Network kibővítését az Azure-beli virtuális hálózatra. Az AVS-hálózatról érkező útvonalakat a BGP-n keresztül az Azure-beli virtuális hálózattal cseréli ki a rendszer. Ha konfigurálta a virtuális hálózatokat, az összes egyenrangú virtuális hálózat elérhető lesz az AVS-hálózatról.

![Azure ExpressRoute-kapcsolódás virtuális hálózathoz](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute-kapcsolódás a helyszíni hálózathoz

A meglévő Azure ExpressRoute-áramkört az AVS-régióhoz is összekapcsolhatja. A ExpressRoute Global Reach funkció a két áramkör egymással való összekapcsolására szolgál. Kapcsolat létrejött a helyszíni és az AVS ExpressRoute áramkörök között. Ez a kapcsolódás lehetővé teszi, hogy a helyszíni hálózatait egy AVS Private Cloud Networkre terjessze ki. Az AVS-hálózatról érkező útvonalakat a rendszer a BGP és a helyszíni hálózat között cseréli ki.

![Helyszíni ExpressRoute-kapcsolatok – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Kapcsolódás helyszíni hálózathoz és Azure-beli virtuális hálózathoz

A helyszíni hálózat és az Azure virtuális hálózat kapcsolatai az AVS-hálózatról is létezhetnek. A kapcsolat BGP-t használ a helyszíni hálózat, az Azure Virtual Network és az AVS-hálózat közötti útvonalak cseréjéhez. Ha az AVS-hálózatot Global Reach kapcsolat jelenlétében csatlakoztatja az Azure-beli virtuális hálózathoz, az Azure-beli virtuális hálózati útvonalak láthatók lesznek a helyszíni hálózaton. Az útvonalak cseréje az Azure-ban az Edge-útválasztók között történik.

![Helyszíni ExpressRoute-és Azure-beli virtuális hálózati kapcsolatok](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Fontos szempontok

A helyszíni hálózatról és/vagy egy Azure-beli virtuális hálózatról érkező AVS-hálózathoz való csatlakozás lehetővé teszi az összes hálózat közötti váltást.

* Egy Azure-beli virtuális hálózat mind a helyszíni, mind az AVS-hálózatban látható lesz.
* Ha egy helyszíni hálózatról kapcsolódott az Azure-beli virtuális hálózathoz, az Global Reach-t használó AVS-hálózathoz való csatlakozás lehetővé teszi az AVS-hálózatról származó virtuális hálózatok elérését.
* Az alhálózati címek **nem** lehetnek átfedésben a csatlakoztatott hálózatok között.
* Az AVS **nem** tesz közzé alapértelmezett útvonalat a ExpressRoute-kapcsolatokhoz
* Ha a helyszíni útválasztó meghirdeti az alapértelmezett útvonalat, az AVS-hálózatról és az Azure Virtual networkről érkező forgalom a meghirdetett alapértelmezett útvonalat fogja használni. Ennek eredményeképpen az Azure-beli virtuális gépek nem érhetők el nyilvános IP-címek használatával.

## <a name="next-steps"></a>Következő lépések

* [Az Azure Virtual Network összekötése az AVS-lel a ExpressRoute használatával](virtual-network-connection.md)
* [Kapcsolódás a helyszínről az AVS-hez a ExpressRoute használatával](on-premises-connection.md)

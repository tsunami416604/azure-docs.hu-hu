---
title: Azure VMware megoldás Hálózatkezelés és kapcsolat
description: Azure VMware megoldás Hálózatkezelés és kapcsolat leírása.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2020
ms.locfileid: "92924971"
---
<!-- Used in introduction.md and concepts-networking.md -->

Az Azure VMware megoldás a helyszíni és az Azure-alapú környezetből vagy erőforrásokból elérhető privát felhőalapú környezetet kínál. Az Azure ExpressRoute és a VPN-kapcsolatok szolgáltatásai biztosítják a kapcsolatot. Ezeknek a szolgáltatásoknak konkrét hálózati címtartományok és tűzfal-portok szükségesek a szolgáltatások engedélyezéséhez.

Privát felhő üzembe helyezése esetén a rendszer létrehoz egy privát hálózatot a felügyelethez, a létesítéshez és a vMotion. Ezeket a magánhálózatok használatával érheti el a vCenter és a NSX-T kezelőt, illetve a virtuális gépek vMotion vagy üzembe helyezését.  A ExpressRoute Global Reach a privát felhők helyszíni környezetekhez való összekapcsolására szolgál. A kapcsolathoz egy ExpressRoute áramkörrel rendelkező virtuális hálózat szükséges az előfizetésben.

Az erőforrások, például a webkiszolgálók és a virtuális gépek elérhetők az interneten keresztül az Azure Virtual WAN nyilvános IP-funkcióival.  Alapértelmezés szerint az Internet-hozzáférés le van tiltva az új privát felhőknél. További információkért lásd: [a nyilvános IP-funkciók használata az Azure VMware megoldásban](../public-ip-usage.md).
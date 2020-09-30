---
title: Azure VMWare megoldás Hálózatkezelés és kapcsolat
description: Azure VMWare megoldás Hálózatkezelés és kapcsolat leírása.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574456"
---
<!-- Used in introduction.md and concepts-networking.md -->

Az Azure VMware megoldás a helyszíni és az Azure-alapú környezetből vagy erőforrásokból elérhető privát felhőalapú környezetet kínál. Az Azure ExpressRoute és a VPN-kapcsolatok szolgáltatásai biztosítják a kapcsolatot. Ezeknek a szolgáltatásoknak konkrét hálózati címtartományok és tűzfal-portok szükségesek a szolgáltatások engedélyezéséhez.

Privát felhő üzembe helyezése esetén a rendszer létrehoz egy privát hálózatot a felügyelethez, a létesítéshez és a vMotion. Ezeket a magánhálózatok használatával érheti el a vCenter és a NSX-T kezelőt, illetve a virtuális gépek vMotion vagy üzembe helyezését.  A ExpressRoute Global Reach a privát felhők helyszíni környezetekhez való összekapcsolására szolgál. A kapcsolathoz egy ExpressRoute áramkörrel rendelkező virtuális hálózat szükséges az előfizetésben.



>[!NOTE]
>Az internethez és az Azure-szolgáltatásokhoz való hozzáférés üzembe helyezése és biztosítása a virtuális gépek üzemi hálózatokon való felhasználásához a privát felhő telepítésekor.  Alapértelmezés szerint az Internet-hozzáférés le van tiltva az új privát felhőknél, és a szolgáltatás bármikor engedélyezhető vagy letiltható.
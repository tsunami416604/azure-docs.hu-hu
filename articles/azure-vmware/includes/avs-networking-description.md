---
title: Azure VMWare megoldás Hálózatkezelés és kapcsolat
description: Azure VMWare megoldás Hálózatkezelés és kapcsolat leírása.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 6c45043ebbbc5099f512a1d746e48d2a8a065e6f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316892"
---
<!-- Used in introduction.md and concepts-networking.md -->

Az Azure VMware megoldás a helyszíni és az Azure-alapú környezetből vagy erőforrásokból elérhető privát felhőalapú környezetet kínál. Az Azure ExpressRoute és a VPN-kapcsolatok szolgáltatásai biztosítják a kapcsolatot. Ezeknek a szolgáltatásoknak konkrét hálózati címtartományok és tűzfal-portok szükségesek a szolgáltatások engedélyezéséhez.

Privát felhő üzembe helyezése esetén a rendszer létrehoz egy privát hálózatot a felügyelethez, a létesítéshez és a vMotion. Ezeket a magánhálózatok használatával érheti el a vCenter és a NSX-T kezelőt, illetve a virtuális gépek vMotion vagy üzembe helyezését.  A ExpressRoute Global Reach a privát felhők helyszíni környezetekhez való összekapcsolására szolgál. A kapcsolathoz egy ExpressRoute áramkörrel rendelkező virtuális hálózat szükséges az előfizetésben.


>[!NOTE]
>Az internethez és az Azure-szolgáltatásokhoz való hozzáférés üzembe helyezése és biztosítása a virtuális gépek üzemi hálózatokon való felhasználásához a privát felhő telepítésekor.  Alapértelmezés szerint az Internet-hozzáférés le van tiltva az új privát felhőknél, és a szolgáltatás bármikor engedélyezhető vagy letiltható.
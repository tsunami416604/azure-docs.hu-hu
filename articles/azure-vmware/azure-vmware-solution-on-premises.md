---
title: Azure VMware-megoldás összekapcsolása a helyszíni környezettel
description: Ismerje meg, hogyan csatlakoztatható az Azure VMware-megoldás a helyszíni környezethez.
ms.topic: tutorial
ms.date: 10/02/2020
ms.openlocfilehash: 2a0cb641df00f3e580e87e38aff382d8e8101fc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91583308"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Azure VMware-megoldás összekapcsolása a helyszíni környezettel

Ebben a cikkben az Azure VMware-megoldás helyszíni környezethez való csatlakozásának [megtervezése során összegyűjtött információkat](production-ready-deployment-steps.md) fogja használni.

Mielőtt elkezdené, két előfeltétel van az Azure VMware-megoldás helyszíni környezethez való csatlakoztatásához:

- Egy ExpressRoute áramkör a helyszíni környezetből az Azure-ba.
- A/29 nem átfedésben lévő hálózati címterület a ExpressRoute Global Reach-társításhoz, amelyet a [tervezési fázis](production-ready-deployment-steps.md)részeként adott meg.

>[!NOTE]
> VPN-en keresztül is csatlakozhat, de ez a gyors üzembe helyezési dokumentum hatókörén kívül esik.

## <a name="establish-an-expressroute-global-reach-connection"></a>ExpressRoute-Global Reach kapcsolat létrehozása

Ha helyszíni kapcsolatot szeretne létesíteni az Azure VMware-megoldás privát felhővel a ExpressRoute Global Reach használatával, kövesse a [helyszíni környezeteket egy privát felhőalapú](tutorial-expressroute-global-reach-private-cloud.md) oktatóanyagban.



## <a name="verify-on-premises-network-connectivity"></a>Helyszíni hálózati kapcsolat ellenőrzése

Ekkor látnia kell a helyszíni **peremhálózati útválasztón** , ahol a ExpressRoute csatlakoztatja a NSX-T hálózati szegmenseket és az Azure VMware megoldás-felügyeleti szegmenseit.

>[!NOTE]
>Mindenki más környezettel rendelkezik, és néhánynak lehetővé kell tennie, hogy ezeket az útvonalakat vissza lehessen terjeszteni a helyszíni hálózatra.  

Egyes környezetek tűzfallal rendelkeznek a ExpressRoute áramkör védelméhez.  Ha nincs tűzfal, és nincs útvonal-metszés, az Azure VMware-megoldás vCenter-kiszolgálóját vagy egy [virtuális gépet](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) a helyszíni környezetből származó NSX-T szegmensen is pingelheti.

Emellett a NSX-T szegmensben lévő virtuális gépről elérheti a helyszíni környezetben lévő erőforrásokat is.

## <a name="next-steps"></a>További lépések

Folytassa a következő szakasszal a VMware HCX üzembe helyezéséhez és konfigurálásához

> [!div class="nextstepaction"]
> [VMware HCX üzembe helyezése és konfigurálása](tutorial-deploy-vmware-hcx.md)
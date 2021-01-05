---
title: Azure VMware-megoldás összekapcsolása a helyszíni környezettel
description: Ismerje meg, hogyan csatlakoztatható az Azure VMware-megoldás a helyszíni környezethez.
ms.topic: tutorial
ms.date: 12/28/2020
ms.openlocfilehash: 753835b0206d8bbabe42b057fa40a2d6c4c8c414
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/29/2020
ms.locfileid: "97809683"
---
# <a name="connect-azure-vmware-solution-to-your-on-premises-environment"></a>Azure VMware-megoldás összekapcsolása a helyszíni környezettel

Ebben a cikkben az Azure VMware-megoldás helyszíni környezethez való csatlakozásának [megtervezése során összegyűjtött információkat](production-ready-deployment-steps.md) fogja használni.

Mielőtt elkezdené, két előfeltétel van az Azure VMware-megoldás helyszíni környezethez való csatlakoztatásához:

- Egy ExpressRoute áramkör a helyszíni környezetből az Azure-ba.
- A/29 nem átfedésben lévő hálózati címterület a ExpressRoute Global Reach-társításhoz, amelyet a [tervezési fázis](production-ready-deployment-steps.md)részeként adott meg.

>[!NOTE]
> Csatlakozhat VPN-kapcsolaton keresztül, de ez a gyors üzembe helyezési dokumentum hatókörén kívül esik.

## <a name="establish-an-expressroute-global-reach-connection"></a>ExpressRoute-Global Reach kapcsolat létrehozása

Ha helyszíni kapcsolatot szeretne létesíteni az Azure VMware-megoldás privát felhővel a ExpressRoute Global Reach használatával, kövesse a [helyszíni környezeteket egy privát felhőalapú](tutorial-expressroute-global-reach-private-cloud.md) oktatóanyagban.

## <a name="verify-on-premises-network-connectivity"></a>Helyszíni hálózati kapcsolat ellenőrzése

Ekkor látnia kell a helyszíni **peremhálózati útválasztón** , ahol a ExpressRoute csatlakoztatja a NSX-T hálózati szegmenseket és az Azure VMware megoldás-felügyeleti szegmenseit.

>[!IMPORTANT]
>Mindenki más környezettel rendelkezik, és néhánynak lehetővé kell tennie, hogy ezeket az útvonalakat vissza lehessen terjeszteni a helyszíni hálózatra.  

Egyes környezetek tűzfallal védik a ExpressRoute áramkört.  Ha nincsenek tűzfalak, és nincs útvonal-metszés, Pingelje az Azure VMware-megoldás vCenter-kiszolgálóját vagy egy virtuális gépet a helyszíni környezetből származó [NSX-T szegmensen](deploy-azure-vmware-solution.md#add-a-vm-on-the-nsx-t-network-segment) . Emellett a NSX-T szegmensben lévő virtuális gépről elérheti a helyszíni környezetben lévő erőforrásokat is.

## <a name="next-steps"></a>További lépések

Folytassa a következő szakasszal a VMware HCX üzembe helyezéséhez és konfigurálásához

> [!div class="nextstepaction"]
> [A VMware HCX üzembe helyezése és konfigurálása](tutorial-deploy-vmware-hcx.md)
---
title: 'Forgatókönyv: útvonal a megosztott szolgáltatások virtuális hálózatok'
titleSuffix: Azure Virtual WAN
description: Útválasztási forgatókönyvek – az útvonalakat úgy állíthatja be, hogy a megosztott szolgáltatások VNet hozzáférjenek egy olyan munkaterheléssel, amelyet minden VNet és elágazáshoz el szeretne érni.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 64bb4c85399f811c0ab7ff84b297b64734efc491
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568822"
---
# <a name="scenario-route-to-shared-services-vnets"></a>Forgatókönyv: útvonal a megosztott szolgáltatások virtuális hálózatok

A virtuális WAN virtuális hub útválasztásával kapcsolatban igen sok lehetőség áll rendelkezésre. Ebben a forgatókönyvben az útvonalakat úgy kell beállítani, hogy hozzáférjenek egy **megosztott szolgáltatás** VNet olyan számítási feladatokhoz, amelyeket minden VNet és ág (VPN/er/P2S) számára szeretne elérni. További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

## <a name="scenario-workflow"></a><a name="workflow"></a>Forgatókönyv-munkafolyamat

Ennek a forgatókönyvnek a konfigurálásához a következő lépéseket kell figyelembe vennie:

1. Azonosítsa a **megosztott szolgáltatások** VNet.
2. Hozzon létre egy egyéni útválasztási táblázatot. A példában az útválasztási táblázatra **RT_SHARED**néven hivatkozunk. Az útválasztási táblázat létrehozásának lépéseiért lásd: [a virtuális központ útválasztásának konfigurálása](how-to-virtual-hub-routing.md). A következő értékeket használja útmutatóként:

   * **Társulási**
     * A **megosztott szolgáltatások VNet *kivételével* a virtuális hálózatok**válassza ki az elkülöníteni kívánt virtuális hálózatok. Ez azt jelenti, hogy az összes ilyen virtuális hálózatok (kivéve a megosztott szolgáltatások VNet) elérheti a célhelyet RT_SHARED útválasztási táblázat útvonalai alapján.

   * **Propagálás**
      * **Ágak**esetében az útvonalak ezen útválasztási táblázatba való propagálása a már kiválasztott útválasztási táblákon kívül is. Ennek a lépésnek köszönhetően RT_SHARED útválasztási táblázat az összes ág-kapcsolat (VPN/ER/User VPN) útvonalát fogja megtanulni.
      * A **virtuális hálózatok**területen válassza ki a **megosztott szolgáltatások VNet**. Ennek a lépésnek köszönhetően RT_SHARED útválasztási táblázat a megosztott szolgáltatások VNet-kapcsolataiból származó útvonalakat fogja megtanulni.

     Ez azt eredményezi, hogy az útválasztási konfiguráció megváltozik az alábbi ábrán látható módon.

   :::image type="content" source="./media/routing-scenarios/shared-service-vnet/shared-services.png" alt-text="Megosztott szolgáltatások VNet":::

## <a name="next-steps"></a>További lépések

* A virtuális WAN-ról további információt a [Gyakori kérdések](virtual-wan-faq.md)című témakörben talál.
* További információ a virtuális központ útválasztásáról: [Tudnivalók a virtuális központ útválasztásáról](about-virtual-hub-routing.md).

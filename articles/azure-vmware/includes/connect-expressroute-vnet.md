---
title: A ExpressRoute és a virtuális hálózati átjáró összekötése
description: A ExpressRoute virtuális hálózati átjáróhoz való kapcsolódásának lépései.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 5f9a565a7662041dbd85e61388129496fa376962
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861517"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Navigáljon a [vSphere-fürt üzembe helyezése az Azure](../tutorial-create-private-cloud.md) -ban című oktatóanyagban létrehozott privát felhőhöz. A **kezelés** területen válassza a **kapcsolat** lehetőséget, majd válassza a **ExpressRoute** lapot.

1. Másolja az engedélyezési kulcsot. Ha nincs engedélyezési kulcs, létre kell hoznia egyet, majd **az engedélyezési kulcs kérése** lehetőséget kell választania.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Másolja az engedélyezési kulcsot. Ha nincs engedélyezési kulcs, létre kell hoznia egyet, majd az engedélyezési kulcs kérése lehetőséget kell választania." border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::

1. Lépjen az előző lépésben létrehozott Virtual Network átjáróra, és a **Beállítások** területen válassza a **kapcsolatok** lehetőséget. A **kapcsolatok** lapon válassza a **+ Hozzáadás** lehetőséget.

1. A **kapcsolatok hozzáadása** lapon adja meg a mezők értékeit, majd kattintson az **OK gombra**. 

   | Mező | Érték |
   | --- | --- |
   | **Név**  | Adjon egy nevet a kapcsolatnak.  |
   | **Kapcsolat típusa**  | Válassza a **ExpressRoute** lehetőséget.  |
   | **Engedély beváltása**  | Győződjön meg arról, hogy a jelölőnégyzet be van jelölve.  |
   | **Virtuális hálózati átjáró** | A korábban létrehozott Virtual Network átjáró.  |
   | **Engedélyezési kulcs**  | Másolja és illessze be az engedélyezési kulcsot az erőforráscsoport ExpressRoute lapjáról. |
   | **Társ áramkör URI-ja**  | Másolja és illessze be a ExpressRoute-azonosítót az erőforráscsoport ExpressRoute lapjáról.  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="A kapcsolatok hozzáadása lapon adja meg a mezők értékeit, majd kattintson az OK gombra." border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

Létrejön a ExpressRoute-áramkör és a Virtual Network közötti kapcsolat.
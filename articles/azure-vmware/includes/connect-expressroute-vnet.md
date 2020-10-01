---
title: A ExpressRoute és a virtuális hálózati átjáró összekötése
description: A ExpressRoute virtuális hálózati átjáróhoz való kapcsolódásának lépései.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 214ef9c01193b238c8e456ef2809f7a2edbdb6c7
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91598156"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Navigáljon az előző oktatóanyagban létrehozott saját felhőhöz, és válassza a **kezelés**alatt a **kapcsolat** lehetőséget, és válassza a **ExpressRoute** lapot.

1. Másolja az engedélyezési kulcsot. Ha nincs engedélyezési kulcs, létre kell hoznia egyet, majd **az engedélyezési kulcs kérése**lehetőséget kell választania.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Másolja az engedélyezési kulcsot. Ha nincs engedélyezési kulcs, létre kell hoznia egyet, majd az engedélyezési kulcs kérése lehetőséget kell választania." border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::

1. Lépjen az előző lépésben létrehozott Virtual Network átjáróra, és a **Beállítások**területen válassza a **kapcsolatok**lehetőséget. A **kapcsolatok** lapon válassza a **+ Hozzáadás**lehetőséget.

1. A **kapcsolatok hozzáadása** lapon adja meg a mezők értékeit, majd kattintson az **OK gombra**. 

   | Mező | Érték |
   | --- | --- |
   | **Név**  | Adjon egy nevet a kapcsolatnak.  |
   | **Kapcsolat típusa**  | Válassza a **ExpressRoute**lehetőséget.  |
   | **Engedély beváltása**  | Győződjön meg arról, hogy a jelölőnégyzet be van jelölve.  |
   | **Virtuális hálózati átjáró** | A korábban létrehozott Virtual Network átjáró.  |
   | **Engedélyezési kulcs**  | Másolja és illessze be az engedélyezési kulcsot az erőforráscsoport ExpressRoute lapjáról. |
   | **Társ áramkör URI-ja**  | Másolja és illessze be a ExpressRoute-azonosítót az erőforráscsoport ExpressRoute lapjáról.  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Másolja az engedélyezési kulcsot. Ha nincs engedélyezési kulcs, létre kell hoznia egyet, majd az engedélyezési kulcs kérése lehetőséget kell választania." border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

Létrejön a ExpressRoute-áramkör és a Virtual Network közötti kapcsolat.
---
title: Az Azure-beli virtuális gépek karbantartási ellenőrzésének áttekintése a Azure Portal használatával
description: Megtudhatja, hogyan szabályozhatja, hogy az Azure-beli virtuális gépek karbantartását hogyan kell alkalmazni a karbantartási vezérlők használatával.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 4c5e30d0607db2d529ae41ebab6dc82e925ff2a8
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82139198"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>A platform frissítéseinek kezelése a karbantartási ellenőrzéssel 

A karbantartási vezérlő használatával olyan platform-frissítéseket kezelhet, amelyek nem igényelnek újraindítást. Az Azure gyakran frissíti az infrastruktúráját a megbízhatóság, a teljesítmény, a biztonság és az új funkciók elindításának javítása érdekében. A legtöbb frissítés átlátható a felhasználók számára. Bizonyos érzékeny munkaterhelések, például a játékok, a médiaadatfolyam-továbbítás és a pénzügyi tranzakciók nem tolerálják a virtuális gépek lefagyasztásának vagy leválasztásának néhány másodpercét. A karbantartási ellenőrzés lehetőséget biztosít a platform frissítéseinek megvárni, és egy 35 napos időszakon belül alkalmazza őket. 

A karbantartási ellenőrzéssel eldöntheti, hogy mikor alkalmazza a frissítéseket az elkülönített virtuális gépekre és az Azure dedikált gazdagépekre.

A karbantartási ellenőrzéssel a következőket teheti:
- A Batch frissítése egyetlen frissítési csomagba.
- Várjon akár 35 napra a frissítések alkalmazásához. 
- A karbantartási időszak platform-frissítéseinek automatizálása Azure Functions használatával.
- A karbantartási konfigurációk az előfizetések és az erőforráscsoportok között működnek. 

## <a name="limitations"></a>Korlátozások

- A virtuális gépeknek [dedikált gazdagépen](./linux/dedicated-hosts.md)kell lenniük, vagy egy elkülönített virtuálisgép- [mérettel](./linux/isolation.md)kell létrehozni.
- 35 nap elteltével a rendszer automatikusan alkalmazza a frissítést.
- A felhasználónak **erőforrás-közreműködői** hozzáféréssel kell rendelkeznie.

## <a name="management-options"></a>Felügyeleti beállítások

A karbantartási konfigurációkat a következő lehetőségek bármelyikével hozhatja létre és kezelheti:

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure Portal](maintenance-control-portal.md)

## <a name="next-steps"></a>További lépések

További információ: [karbantartás és frissítések](maintenance-and-updates.md).

---
title: Csatlakozás Azure-beli szinapszis-munkaterülethez privát hivatkozások használatával
description: Ez a cikk bemutatja, hogyan csatlakozhat az Azure szinapszis-munkaterülethez privát hivatkozások használatával
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432189"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Csatlakozás az Azure szinapszis-munkaterülethez privát hivatkozások használatával (előzetes verzió)

Ez a cikk bemutatja, hogyan hozhat létre privát végpontot az Azure szinapszis-munkaterülethez. További információért tekintse meg a [privát hivatkozások és a privát végpontok](https://docs.microsoft.com/azure/private-link/) című témakört.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>1. lépés: az Azure szinapszis-munkaterület megnyitása Azure Portal

Válassza a **privát végponti kapcsolatok** lehetőséget a **Biztonság** területen, majd válassza a **+ privát végpont**lehetőséget.
![Az Azure szinapszis munkaterületének megnyitása Azure Portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>2. lépés: válassza ki az előfizetését és a régió részleteit

A **privát végpont létrehozása** ablak **alapismeretek** lapján válassza ki az **előfizetést** és az **erőforráscsoportot**. Adjon **nevet** a létrehozni kívánt privát végpontnak. Válassza ki azt a **régiót** , ahol létre szeretné hozni a privát végpontot.

A magánhálózati végpontok egy alhálózaton jönnek létre. A kiválasztott előfizetés, erőforráscsoport és régió szűri a magánhálózati végpontok alhálózatait. Válassza a **tovább lehetőséget: erőforrás->** , ha elkészült.
![Előfizetés és régió részleteinek kiválasztása](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>3. lépés: az Azure szinapszis-munkaterület részleteinek kiválasztása

Válassza a **Kapcsolódás egy Azure-erőforráshoz a saját címtárban** az **erőforrás** lapon. Válassza ki azt az **előfizetést** , amely tartalmazza az Azure szinapszis-munkaterületét. A privát végpontok Azure-beli szinapszis-munkaterületre való létrehozására szolgáló **erőforrástípus** *Microsoft. szinapszis/munkaterület*.

Válassza ki az Azure szinapszis-munkaterületet **erőforrásként**. Minden Azure szinapszis-munkaterület három **cél alerőforrással** rendelkezik, amelyekhez létrehozhat egy privát végpontot az SQL, a SqlOnDemand és a dev szolgáltatáshoz.

Válassza a Next (tovább) lehetőséget **: a konfiguráció>** a telepítés következő részére való továbblépés előtt.
![Előfizetés és régió részleteinek kiválasztása](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

A **konfiguráció** lapon válassza ki azt a **virtuális hálózatot** és **alhálózatot** , amelyben létre szeretné hozni a magánhálózati végpontot. Létre kell hoznia egy DNS-rekordot is, amely a privát végponthoz van leképezve.

Válassza az **Igen** lehetőséget a privát **DNS-zónával való integráláshoz** , hogy a privát VÉGPONTOT egy privát DNS-zónába integrálja. Ha nem rendelkezik a VNet társított saját DNS-zónával, a rendszer létrehoz egy új privát DNS-zónát. Válassza a **felülvizsgálat + létrehozás** lehetőséget, ha elkészült.

![Előfizetés és régió részleteinek kiválasztása](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Ha a telepítés befejeződött, nyissa meg az Azure szinapszis munkaterületét Azure Portal, és válassza a **privát végponti kapcsolatok**lehetőséget. Megjelenik a privát végponthoz társított új privát végpont és privát végpont-kapcsolódási név.

![Előfizetés és régió részleteinek kiválasztása](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>További lépések

További információ a [felügyelt munkaterület VNet](./synapse-workspace-managed-vnet.md)

További információ a [felügyelt privát végpontokról](./synapse-workspace-managed-private-endpoints.md)

[Felügyelt privát végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)

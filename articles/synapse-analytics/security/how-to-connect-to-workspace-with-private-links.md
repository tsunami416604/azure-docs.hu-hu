---
title: Csatlakozás Azure Synapse-munkaterülethez privát hivatkozások használatával
description: Ebből a cikkből megtudhatja, hogyan csatlakozhat az Azure Synapse-munkaterülethez privát hivatkozások használatával
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432189"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Csatlakozás az Azure Synapse-munkaterülethez privát hivatkozásokkal (előzetes verzió)

Ez a cikk bemutatja, hogyan hozhat létre privát végpontot az Azure Synapse-munkaterülethez. További információért tekintse meg a privát hivatkozásokat és a [privát végpontokat.](https://docs.microsoft.com/azure/private-link/)

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>1. lépés: Nyissa meg az Azure Synapse-munkaterületet az Azure Portalon

Válassza a **Privát végpontkapcsolat** lehetőséget a **Biztonság csoportban,** majd a + Privát **végpont lehetőséget.**
![Az Azure Synapse-munkaterület megnyitása az Azure Portalon](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>2. lépés: Az előfizetés és a régió részleteinek kiválasztása

A **Saját végpont létrehozása** ablak **Alapjai** lapján válassza ki az **Előfizetés** és **erőforráscsoport lehetőséget.** Adjon **nevet** a létrehozni kívánt privát végpontnak. Válassza ki azt a **régiót,** ahol a privát végpontot létre szeretné hozni.

A privát végpontok egy alhálózatban jönnek létre. Az előfizetés, az erőforráscsoport és a régió kijelölt szűrés a magánvégpont-alhálózatok. Válassza a **Tovább: Erőforrás >,** ha elkészült.
![Előfizetés és régió részleteinek kiválasztása](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>3. lépés: Válassza ki az Azure Synapse munkaterületének részleteit

Válassza a **Csatlakozás egy Azure-erőforráshoz a címtárban** az **Erőforrás** lapon. **Subscription** Az Azure Synapse-munkaterülethez való privát végpontok létrehozásához **szükséges erőforrástípus** a *Microsoft.Synapse/workspaces.*

Válassza ki az Azure Synapse munkaterületet **erőforrásként.** Minden Azure Synapse-munkaterület három **cél alerőforrással** rendelkezik, amelyekhez létrehozhat egy privát végpontot: sql, SqlOnDemand és Dev.

Válassza a Tovább: A beállítás következő részébe való ugráshoz **>konfigurációs>** lehetőséget.
![Előfizetés és régió részleteinek kiválasztása](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

A **Konfiguráció** lapon válassza ki a **virtuális hálózatot** és az **alhálózatot,** amelyben a privát végpontot létre kell hozni. Létre kell hoznia egy DNS-rekordot is, amely leképezi a privát végpontot.

Válassza az **Igen** lehetőséget a **privát DNS-zónával való integrációhoz,** ha a privát végpontot egy privát DNS-zónával integrálhatja. Ha nem rendelkezik a virtuális hálózathoz társított privát DNS-zónával, akkor létrejön egy új privát DNS-zóna. Válassza **a Véleményezés + Létrehozás lehetőséget,** ha végzett.

![Előfizetés és régió részleteinek kiválasztása](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Ha a központi telepítés befejeződött, nyissa meg az Azure Synapse-munkaterületet az Azure Portalon, és válassza a **Privát végpontkapcsolatok lehetőséget.** A privát végponthoz társított új magánvégpont- és magánvégpont-kapcsolat neve látható.

![Előfizetés és régió részleteinek kiválasztása](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>További lépések

További információ [a felügyelt munkaterület virtuális hálózatáról](./synapse-workspace-managed-vnet.md)

További információ a [felügyelt magánvégpontokról](./synapse-workspace-managed-private-endpoints.md)

[Felügyelt személyes végpontok létrehozása az adatforrásokhoz](./how-to-create-managed-private-endpoints.md)

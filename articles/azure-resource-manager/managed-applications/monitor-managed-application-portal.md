---
title: Felügyelt alkalmazások figyelése az Azure Portalon
description: Bemutatja, hogyan használhatja az Azure Portalon a felügyelt alkalmazások rendelkezésre állásának és riasztásainak figyelésére.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: afe78dd00ecebdc54b6d73c4c8324729e117d95b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651747"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Felügyelt alkalmazás telepített példányának figyelése

Miután telepítette a felügyelt alkalmazást az Azure-előfizetésében, érdemes ellenőrizni az alkalmazás állapotát. Ez a cikk az Azure Portalon az állapot ellenőrzésére szolgáló beállításokat mutatja be. Figyelheti az erőforrások rendelkezésre állását a felügyelt alkalmazásban. A riasztásokat beállíthatja és megtekintheti is.

## <a name="view-resource-health"></a>Erőforrás állapotának megtekintése

1. Válassza ki a felügyelt alkalmazáspéldányt.

   ![Felügyelt alkalmazás kiválasztása](./media/monitor-managed-application-portal/select-managed-application.png)

1. Válassza az **Erőforrás állapota**lehetőséget.

   ![Erőforrás állapotának kiválasztása](./media/monitor-managed-application-portal/select-resource-health.png)

1. Tekintse meg az erőforrások rendelkezésre állását a felügyelt alkalmazásban.

   ![Erőforrás állapotának megtekintése](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Riasztások megtekintése

1. Válassza **a Riasztások lehetőséget.**

   ![Riasztások kiválasztása](./media/monitor-managed-application-portal/select-alerts.png)

1. Ha a riasztási szabályok konfigurálva vannak, a kiszivárgott riasztásokra vonatkozó információk jelennek meg.

   ![Riasztások megtekintése](./media/monitor-managed-application-portal/view-alerts.png)

1. Riasztási szabályok hozzáadásához válassza **a + Új riasztási szabály**lehetőséget.

   ![Riasztás létrehozása](./media/monitor-managed-application-portal/create-new-alert.png)

Riasztásokat hozhat létre a felügyelt alkalmazáspéldányhoz vagy a felügyelt alkalmazás erőforrásaihoz. A riasztások létrehozásáról a [Microsoft Azure-ban a riasztások áttekintése](../../azure-monitor/platform/alerts-overview.md)című témakörben olvashat.

## <a name="next-steps"></a>További lépések

* Felügyelt alkalmazáspéldákért olvassa el [a Mintaprojektek az Azure által felügyelt alkalmazásokhoz.](sample-projects.md)
* Felügyelt alkalmazás üzembe helyezéséről a [Szolgáltatáskatalógus-alkalmazás üzembe helyezése az Azure Portalon keresztül.](deploy-service-catalog-quickstart.md)
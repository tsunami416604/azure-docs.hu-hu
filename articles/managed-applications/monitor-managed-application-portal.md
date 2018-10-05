---
title: Az Azure portal használatával figyeli egy felügyelt alkalmazást |} A Microsoft Docs
description: Bemutatja, hogyan rendelkezésre állás és a egy felügyelt alkalmazás kapcsolatos riasztások figyelése az Azure portal használatával.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: ea9f55d7c6002aaba2fd4fdc2a76b93f98e1d6b0
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809905"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>A figyelő egy felügyelt alkalmazás telepített példánya

Miután telepítette az Azure-előfizetéshez egy felügyelt alkalmazást, érdemes lehet, hogy az alkalmazás állapotának ellenőrzéséhez. Ez a cikk bemutatja a beállításokat az az állapot ellenőrzése az Azure Portalon. A felügyelt alkalmazás segítségével figyelheti az erőforrások rendelkezésre állását. Állítsa be, és megtekintheti a riasztásokat is.

## <a name="view-resource-health"></a>Erőforrás állapotának megtekintése

1. Válassza ki a felügyelt alkalmazás példányát.

   ![Felügyelt alkalmazás kiválasztása](./media/monitor-managed-application-portal/select-managed-application.png)

1. Válassza ki **a Resource Health**.

   ![Válassza ki a resource health](./media/monitor-managed-application-portal/select-resource-health.png)

1. Tekintse meg az erőforrások rendelkezésre állásának a felügyelt alkalmazás.

   ![Erőforrás állapotának megtekintése](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Riasztások megtekintése

1. Válassza ki **riasztások**.

   ![A riasztások kijelölése](./media/monitor-managed-application-portal/select-alerts.png)

1. Ha riasztási szabályok konfigurálva van, lásd: információ a riasztásokat, amelyek merült fel.

   ![Riasztások megtekintése](./media/monitor-managed-application-portal/view-alerts.png)

1. Riasztási szabályok hozzáadásához válassza **+ Új riasztási szabály**.

   ![Riasztás létrehozása](./media/monitor-managed-application-portal/create-new-alert.png)

A felügyelt alkalmazás példányát, vagy a felügyelt alkalmazás erőforrása riasztásokat is létrehozhat. Riasztások létrehozásával kapcsolatos információkért lásd: [áttekintése a Microsoft Azure-ban riasztások](../monitoring-and-diagnostics/monitoring-overview-alerts.md).

## <a name="next-steps"></a>További lépések

* Felügyelt alkalmazás példákért lásd [mintaprojektekkel az Azure által felügyelt alkalmazások](sample-projects.md).
* Felügyelt alkalmazás üzembe helyezéséhez lásd: [üzembe helyezés szolgáltatás szolgáltatáskatalógusban elérhető alkalmazás Azure Portalon keresztül](deploy-service-catalog-quickstart.md).
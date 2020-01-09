---
title: A Azure Portal használata a Service Catalog alkalmazás üzembe helyezéséhez
description: Megjeleníti a felügyelt alkalmazások felhasználóit, hogyan helyezhet üzembe egy Service Catalog-alkalmazást a Azure Portalon keresztül.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 3fa9709e096e908907772c940fc5e2f2895b7eb3
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650785"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Service Catalog-alkalmazás üzembe helyezése Azure Portal

Az [előző](publish-managed-app-definition-quickstart.md)rövid útmutatóban közzétett egy felügyelt alkalmazás definícióját. Ebben a rövid útmutatóban létrehoz egy Service Catalog-alkalmazást az adott definícióból.

## <a name="create-service-catalog-app"></a>Service Catalog-alkalmazás létrehozása

A Azure Portal hajtsa végre a következő lépéseket:

1. Válassza az **Erőforrás létrehozása** lehetőséget.

   ![Erőforrás létrehozása](./media/deploy-service-catalog-quickstart/create-new.png)

1. Keressen rá a **Service Catalog által felügyelt alkalmazásra** , és válassza ki az elérhető lehetőségek közül.

   ![Service Catalog-alkalmazás keresése](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Ekkor megjelenik a felügyelt alkalmazás szolgáltatás leírása. Kattintson a **Létrehozás** gombra.

   ![Kattintás a Létrehozás gombra](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. A portálon a felügyelt alkalmazás-definíciók láthatók, amelyekhez hozzáférése van. Az elérhető definíciók közül válassza ki azt, amelyet telepíteni kíván. Ebben a rövid útmutatóban az előző rövid útmutatóban létrehozott **felügyelt Storage-fiók** definícióját használja. Kattintson a **Létrehozás** gombra.

   ![Telepítendő definíció kiválasztása](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Adja meg az **alapok** lap értékeit. Válassza ki azt az Azure-előfizetést, amelybe telepíteni szeretné a Service Catalog alkalmazást a alkalmazásban. Hozzon létre egy **alkalmazáscsoport**nevű új erőforráscsoportot. Válassza ki az alkalmazás helyét. Ha elkészült, kattintson **az OK gombra**.

   ![Alapszintű értékek megadása](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Adja meg a Storage-fiók nevének előtagját. Válassza ki a létrehozandó Storage-fiók típusát. Ha elkészült, kattintson **az OK gombra**.

   ![Értékek megadása a tároláshoz](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Tekintse át az összegzést. Az érvényesítés sikeres befejezését követően kattintson **az OK gombra** az üzembe helyezés megkezdéséhez.

   ![Összefoglalás megtekintése](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Eredmények megtekintése

A Service Catalog alkalmazás üzembe helyezését követően két új erőforráscsoport van. Egy erőforráscsoport tartalmazza a Service Catalog alkalmazást. A másik erőforráscsoport tárolja a Service Catalog alkalmazás erőforrásait.

1. Tekintse meg a **alkalmazáscsoport** nevű erőforráscsoportot a Service Catalog alkalmazás megtekintéséhez.

   ![Alkalmazás megtekintése](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Tekintse meg a **alkalmazáscsoport {hash-characters}** nevű erőforráscsoportot a Service Catalog alkalmazás erőforrásainak megtekintéséhez.

   ![Erőforrások megtekintése](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazások definíciós fájljainak létrehozásával kapcsolatos további információkért lásd: [felügyelt alkalmazás definíciójának létrehozása és közzététele](publish-service-catalog-app.md).
* Az Azure CLI esetében lásd: [a Service Catalog alkalmazás üzembe helyezése az Azure CLI-vel](./scripts/managed-application-cli-sample-create-application.md).
* A PowerShell esetében lásd: [a Service Catalog alkalmazás üzembe helyezése a PowerShell](./scripts/managed-application-poweshell-sample-create-application.md)-lel.
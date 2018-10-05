---
title: Szolgáltatási katalógus-alkalmazás üzembe helyezése az Azure portal használatával |} A Microsoft Docs
description: Felügyelt alkalmazások felhasználóinak bemutatja, hogyan service catalog-alkalmazás üzembe helyezése az Azure Portalon keresztül.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 4d2e8b442f70ee791fe65a32402e5272eda3f209
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809774"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Az Azure Portalon keresztül service catalog-alkalmazás üzembe helyezése

Az a [rövid megelőző](publish-managed-app-definition-quickstart.md), felügyelt alkalmazás definíciójának közzétett. Ez a rövid útmutatóban a definícióból hoz létre egy szolgáltatás szolgáltatáskatalógusban elérhető alkalmazás.

## <a name="create-service-catalog-app"></a>Katalógus alkalmazás létrehozása

Az Azure Portalon kövesse az alábbi lépéseket:

1. Válassza ki **erőforrás létrehozása**.

   ![Erőforrás létrehozása](./media/deploy-service-catalog-quickstart/create-new.png)

1. Keresse meg **Szolgáltatáskatalógusbeli felügyelt alkalmazás** , és jelölje ki az elérhető lehetőségek közül.

   ![Szolgáltatáskatalógusban elérhető alkalmazás keresése](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Láthatja, hogy az alkalmazás felügyelt szolgáltatás leírását. Kattintson a **Létrehozás** gombra.

   ![Kattintás a Létrehozás gombra](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. A portál megjeleníti a felügyelt alkalmazásdefiníciók, amely hozzáféréssel rendelkezik. A rendelkezésre álló definíciók, válassza ki a telepíteni kívánt. Ez a rövid útmutatóban használni a **felügyelt Tárfiók** definíciót, amely az előző rövid útmutatóban létrehozott. Kattintson a **Létrehozás** gombra.

   ![Válassza ki a definíció telepítése](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Adja meg az értékeket a **alapjai** fülre. Válassza ki az Azure-előfizetés üzembe helyezése a service catalog alkalmazást. Hozzon létre egy új erőforráscsoportot **applicationgroup csoportban**. Válassza ki az alkalmazás helyét. Amikor végzett, válassza ki a **OK**.

   ![Alapszintű értékeinek megadása](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Adja meg a tárfiók neve előtag. Válassza ki a létrehozni kívánt tárfiók típusát. Amikor végzett, válassza ki a **OK**.

   ![Tároló értékeinek megadása](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Tekintse át az összegzést. Miután az érvényesítés sikeres, válassza ki a **OK** való központi telepítésének megkezdése.

   ![Összefoglalás megtekintése](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Eredmények megtekintése

A service catalog alkalmazás már telepítve lett, után két új erőforráscsoport. Egy erőforráscsoport tartalmazza a szolgáltatás szolgáltatáskatalógusban elérhető alkalmazás. Az erőforráscsoport a szolgáltatás szolgáltatáskatalógusban elérhető alkalmazás az erőforrásokat tárol.

1. Megtekintheti az erőforráscsoport neve **applicationgroup csoportban** a service catalog alkalmazás megtekintéséhez.

   ![Alkalmazás megtekintése](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Megtekintheti az erőforráscsoport neve **applicationgroup csoportban {kettőskereszt karakteret}** az erőforrásokat a service catalog-alkalmazás megtekintéséhez.

   ![Erőforrások megtekintése](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan hozhat létre egy felügyelt alkalmazás a definíciós fájlokat, tekintse meg a [létrehozása és közzététele a felügyelt alkalmazás definíciójának](publish-service-catalog-app.md).
* Azure CLI-hez, lásd: [üzembe helyezés szolgáltatás szolgáltatáskatalógusban elérhető alkalmazás az Azure CLI-vel](./scripts/managed-application-cli-sample-create-application.md).
* PowerShell esetén lásd: [üzembe helyezés szolgáltatás szolgáltatáskatalógusban elérhető alkalmazás, a PowerShell-lel](./scripts/managed-application-poweshell-sample-create-application.md).
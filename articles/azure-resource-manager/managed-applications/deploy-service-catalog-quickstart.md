---
title: A szolgáltatáskatalógus-alkalmazás üzembe helyezése az Azure Portal használatával
description: Megmutatja a felügyelt alkalmazások fogyasztóinak, hogyan telepíthetik a szolgáltatáskatalógus-alkalmazást az Azure Portalon keresztül.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 9a69296ddfc93fd7e8a6650df91876829631f5d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473064"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Rövid útmutató: Szolgáltatáskatalógus-alkalmazás üzembe helyezése az Azure Portalon keresztül

Az [előző rövid útmutatóban](publish-managed-app-definition-quickstart.md)felügyelt alkalmazásdefiníciót tett közzé. Ebben a rövid útmutatóban hozzon létre egy szolgáltatáskatalógus-alkalmazást ebből a definícióból.

## <a name="create-service-catalog-app"></a>Szolgáltatáskatalógus-alkalmazás létrehozása

Az Azure Portalon kövesse az alábbi lépéseket:

1. Válassza az **Erőforrás létrehozása** lehetőséget.

   ![Erőforrás létrehozása](./media/deploy-service-catalog-quickstart/create-new.png)

1. Keresse meg a **Szolgáltatáskatalógus felügyelt alkalmazását,** és válassza ki a rendelkezésre álló lehetőségek közül.

   ![Szolgáltatáskatalógus-alkalmazás keresése](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. A felügyelt alkalmazás szolgáltatás leírása látható. Kattintson a **Létrehozás** gombra.

   ![Kattintás a Létrehozás gombra](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. A portál megjeleníti a felügyelt alkalmazásdefiníciókat, amelyekhez hozzáféréssel rendelkezik. A rendelkezésre álló definíciók közül válassza ki a telepíteni kívánt definíciót. Ebben a rövid útmutatóban használja az előző rövid útmutatóban létrehozott **felügyelt tárfiók-definíciót.** Kattintson a **Létrehozás** gombra.

   ![Az üzembe helyezve kívánt definíció kiválasztása](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Adja meg az **alapszintű** lapot. Hozzon létre egy **új erőforráscsoportot applicationGroup**néven. Válassza ki az alkalmazás helyét. Ha végzett, válassza **az OK gombot.**

   ![Értékek megadásáaz alapszintű alapanyaghoz](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Adjon meg egy előtagot a tárfiók nevéhez. Válassza ki a létrehozandó tárfiók típusát. Ha végzett, válassza **az OK gombot.**

   ![A tárolás értékeinek megadása](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Tekintse át az összegzést. Az ellenőrzés sikeres befejezése után a telepítés megkezdéséhez válassza az **OK gombot.**

   ![Összegzés megtekintése](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Eredmények megtekintése

A szolgáltatáskatalógus-alkalmazás üzembe helyezése után két új erőforráscsoporttal rendelkezik. Egy erőforráscsoport rendelkezik a szolgáltatáskatalógus-alkalmazással. A másik erőforráscsoport rendelkezik a szolgáltatáskatalógus-alkalmazás erőforrásaival.

1. Tekintse meg az **applicationGroup** nevű erőforráscsoportot a szolgáltatáskatalógus-alkalmazás megtekintéséhez.

   ![Alkalmazás megtekintése](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Tekintse meg az **applicationGroup{hash-characters}** nevű erőforráscsoportot a szolgáltatáskatalógus-alkalmazás erőforrásainak megtekintéséhez.

   ![Erőforrások megtekintése](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazások definíciós fájljainak létrehozásáról a [Felügyelt alkalmazásdefiníció létrehozása és közzététele](publish-service-catalog-app.md)című témakörben olvashat.
* Az Azure CLI-ről a [Szolgáltatáskatalógus-alkalmazás üzembe helyezése az Azure CLI-vel.](./scripts/managed-application-cli-sample-create-application.md)
* A PowerShell esetében [olvassa el a Szolgáltatáskatalógus-alkalmazás központi telepítése a PowerShell használatával.](./scripts/managed-application-poweshell-sample-create-application.md)

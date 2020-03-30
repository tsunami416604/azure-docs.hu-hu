---
title: Logikaialkalmazás-sablonok üzembe helyezése
description: Megtudhatja, hogyan telepítheti az Azure Logic Apps-alkalmazásokhoz létrehozott Azure Resource Manager-sablonokat
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 95bfa7b1918da09e4f5913eeb2b57c290b093efe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270445"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Azure Resource Manager-sablonok üzembe helyezése az Azure Logic Apps alkalmazásokhoz

Miután létrehozott egy Azure Resource Manager-sablont a logikai alkalmazáshoz, az alábbi módokon telepítheti a sablont:

* [Azure-portál](#portal)
* [Vizuális stúdió](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Üzembe helyezés az Azure Portalon keresztül

Ha automatikusan üzembe szeretne helyezni egy logikai alkalmazássablont az Azure-ba, a következő Üzembe helyezés az **Azure-ba** gombot választhatja, amely bejelentkezik az Azure Portalra, és tájékoztatást kér a logikai alkalmazásról. Ezután elláthatja a szükséges módosításokat a logikai alkalmazássablonon vagy -paramétereken.

[![Üzembe helyezés az Azure-ban](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Például az Azure Portalra való bejelentkezés után a következő adatokat kéri a rendszer:

* Az Azure-előfizetés neve
* A használni kívánt erőforráscsoport
* A logikai alkalmazás helye
* A logikai alkalmazás neve
* Teszt URI
* A meghatározott feltételek elfogadása

További információt az alábbi témakörökben talál:

* [Áttekintés: A logikai alkalmazások üzembe helyezésének automatizálása az Azure Resource Manager-sablonokkal](logic-apps-azure-resource-manager-templates-overview.md)
* [Erőforrások üzembe helyezése az Azure Resource Manager-sablonokkal és az Azure Portalon](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Üzembe helyezés a Visual Studióval

Ha egy logikai alkalmazássablont szeretne üzembe helyezni egy Azure Resource Group-projektből, amelyet a Visual Studio használatával hozott létre, kövesse az alábbi [lépéseket a logikai alkalmazás azure-ba való manuális üzembe helyezéséhez.](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure)

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

Ha egy adott *Azure-erőforráscsoportra*szeretne telepíteni, használja a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

További információt az alábbi témakörökben talál:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül

Ha egy adott *Azure-erőforráscsoportra*szeretne telepíteni, használja a következő parancsot:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

További információt az alábbi témakörökben talál:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével](../azure-resource-manager/templates/deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Üzembe helyezés az Azure DevOps-szal

A logikai alkalmazássablonok üzembe helyezéséhez és a környezetek kezeléséhez a csapatok gyakran használnak egy eszközt, például az [Azure Folyamatok az](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) Azure [DevOps-ban.](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services) Az Azure Pipelines egy [Azure Resource Group telepítési feladatot](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) biztosít, amelyet bármely build- vagy kiadási folyamathoz hozzáadhat. A kiadási folyamat üzembe helyezéséhez és létrehozásához egy Azure Active Directory (AD) [egyszerű szolgáltatásra](../active-directory/develop/app-objects-and-service-principals.md)is szüksége van. További információ a [szolgáltatástagok Azure Pipelines használatával kapcsolatos további tudnivalókról.](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure)

Az Azure Resource Manager-sablonok folyamatos integrációjáról és folyamatos üzembe helyezéséről (CI/CD) az Azure Pipelines használatával kapcsolatos további információkért tekintse meg az alábbi témaköröket és mintákat:

* [Az Erőforrás-kezelő sablonjainak integrálása az Azure-folyamatokba](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Oktatóanyag: Folyamatos integráció az Azure Resource Manager-sablonokhoz az Azure-folyamatokhoz](../azure-resource-manager/templates/template-tutorial-use-azure-pipelines.md)
* [Példa: Csatlakozás az Azure Service Bus-várólistákhoz az Azure Logic Apps alkalmazásból, és üzembe helyezés az Azure-folyamatok használatával az Azure DevOps-ban](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Példa: Csatlakozás Azure Storage-fiókokhoz az Azure Logic Apps alkalmazásból, és üzembe helyezés az Azure-folyamatok használatával az Azure DevOps-ban](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Példa: Hozzon létre egy függvényalkalmazás-műveletet az Azure Logic Apps alkalmazáshoz, és telepítse az Azure-folyamatokat az Azure DevOps-ban](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Csatlakozzon egy integrációs fiókhoz az Azure Logic Apps alkalmazásból, és telepítse az Azure-folyamatokat az Azure DevOps-ban](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)
* [Minta: Az Azure-folyamatok koordinálása az Azure Logic Apps használatával](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-pipeline-orchestration/azure-devops-orchestration-with-logic-apps/)

Az Azure Pipelines használatának általános magas szintű lépései:

1. Az Azure-folyamatok ban hozzon létre egy üres folyamatot.

1. Válassza ki a folyamathoz szükséges erőforrásokat, például a logikai alkalmazássablont és a sablonparaméter-fájlokat, amelyeket manuálisan vagy a létrehozási folyamat részeként hoz létre.

1. Az ügynöki feladathoz keresse meg és adja hozzá az **Azure Resource Group deployment** feladat.

   !["Azure Resource Group Deployment" feladat hozzáadása](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Konfigurálás [egyszerű szolgáltatással](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Hivatkozások hozzáadása a logikai alkalmazássablonhoz és a sablonparaméter-fájlokhoz.

1. Folytassa a kiadási folyamat lépéseinek kiépítését bármely más környezethez, automatikus teszthez vagy jóváhagyóhoz, ha szükséges.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>OAuth-kapcsolatok engedélyezése

Üzembe helyezés után a logikai alkalmazás működik, végpontok között érvényes paraméterekkel. A [hitelesítő adatok hitelesítéséhez](../active-directory/develop/authentication-scenarios.md)azonban engedélyeznie kell az OAuth-kapcsolatokat. Az OAuth-kapcsolatok engedélyezésének módjai:

* Az automatikus központi telepítések, használhat egy parancsfájlt, amely beleegyezést biztosít az egyes OAuth-kapcsolatok. Íme egy példa parancsfájl a GitHubon a [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projektben.

* OAuth-kapcsolatok manuális engedélyezéséhez nyissa meg a logikai alkalmazást a Logic App Designerben, akár az Azure Portalon, akár a Visual Studióban. A tervezőben engedélyezze a szükséges kapcsolatokat.

Ha a kapcsolatok engedélyezéséhez az Azure Active Directory (Azure AD) [egyszerű szolgáltatását](../active-directory/develop/app-objects-and-service-principals.md) használja, olvassa el, hogyan [adhatja meg az egyszerű szolgáltatásparamétereket a logikai alkalmazássablonban.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Logikai alkalmazások figyelése](../logic-apps/monitor-logic-apps.md)

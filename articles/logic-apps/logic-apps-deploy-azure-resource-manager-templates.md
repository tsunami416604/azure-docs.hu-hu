---
title: Logic app-sablonok üzembe helyezése – Azure Logic Apps
description: Megtudhatja, hogyan telepítheti a Azure Logic Appshoz létrehozott Azure Resource Manager sablonokat
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 07/26/2019
ms.openlocfilehash: dbc92502cdab6e76ba6b8730fca7e0aed3966a43
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494938"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Azure Logic Apps Azure Resource Manager sablonjainak üzembe helyezése

Miután létrehozott egy Azure Resource Manager sablont a logikai alkalmazáshoz, a következő módon telepítheti a sablont:

* [Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Azure DevOps Azure-folyamatok](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Üzembe helyezés Azure Portal

A Logic app-sablonok Azure-ba történő automatikus üzembe helyezéséhez válassza a következő **üzembe helyezés az Azure** -ban gombot, amely bejelentkezik a Azure Portalba, és megkéri a logikai alkalmazással kapcsolatos információk megadására. Ezután elvégezheti a logikai alkalmazás sablonjának vagy paramétereinek a szükséges módosításait.

[![Üzembe helyezés az Azure-ban](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ezt az információt például a rendszer a Azure Portalba való bejelentkezés után kéri:

* Azure-előfizetés neve
* Használni kívánt erőforráscsoport
* Logikai alkalmazás helye
* A logikai alkalmazás neve
* Egy teszt URI
* A megadott feltételek és Kikötések elfogadása

További információkért tekintse meg a következő témaköröket:

* [Áttekintés A Logic apps üzembe helyezésének automatizálása Azure Resource Manager-sablonokkal](logic-apps-azure-resource-manager-templates-overview.md)
* [Erőforrások üzembe helyezése Azure Resource Manager-sablonokkal és a Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Üzembe helyezés a Visual Studióval

Ha a Visual Studióval létrehozott Azure erőforráscsoport-projektből szeretne logikai alkalmazást telepíteni, kövesse az alábbi [lépéseket a logikai alkalmazás manuális üzembe helyezéséhez](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) az Azure-ban.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

Egy adott Azure-erőforráscsoport üzembe helyezéséhez használja a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

További információkért tekintse meg a következő témaköröket:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

Egy adott *Azure-erőforráscsoport*üzembe helyezéséhez használja a következő parancsot:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

További információkért tekintse meg a következő témaköröket:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Üzembe helyezés az Azure DevOps

A logikai alkalmazások sablonjainak üzembe helyezéséhez és a környezetek kezeléséhez a csapatok általában olyan eszközt használnak, mint például az Azure- [DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services)az Azure- [folyamatok](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) . Az Azure-folyamatok egy [Azure erőforráscsoport-telepítési feladatot](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) biztosítanak, amelyet bármely Build-vagy kiadási folyamathoz hozzáadhat. A kiadási folyamat üzembe helyezésének és létrehozásának engedélyezéséhez szükség van egy Azure Active Directory (AD) [egyszerű szolgáltatásra](../active-directory/develop/app-objects-and-service-principals.md)is. További információ az [egyszerű szolgáltatások Azure-folyamatokkal való használatáról](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

További információ a folyamatos integrációról és a folyamatos üzembe helyezésről (CI/CD) az Azure-folyamatokkal rendelkező Azure Resource Manager-sablonokkal kapcsolatban:

* [Resource Manager-sablonok integrálása az Azure-folyamatokkal](../azure-resource-manager/vs-resource-groups-project-devops-pipelines.md)
* [Oktatóanyag: Azure Resource Manager sablonok folyamatos integrálása az Azure-folyamatokkal](../azure-resource-manager/resource-manager-tutorial-use-azure-pipelines.md)

Az Azure-folyamatok használatának általános magas szintű lépései:

1. Hozzon létre egy üres folyamatot az Azure-folyamatokban.

1. Válassza ki a folyamathoz szükséges erőforrásokat, például a logikai alkalmazás sablonját és a sablon paramétereit tartalmazó fájlokat, amelyeket manuálisan vagy a létrehozási folyamat részeként hoz létre.

1. Az ügynök feladatához keresse meg és adja hozzá az **Azure-erőforráscsoport telepítési** feladatát.

   !["Azure Resource Group Deployment" feladat hozzáadása](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Konfigurálás egy [egyszerű szolgáltatással](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Adjon hozzá hivatkozásokat a logikai alkalmazás sablonja és a sablon paramétereinek fájljaihoz.

1. Továbbra is hozhatja létre a környezet, automatizált tesztelési vagy igény szerint a jóváhagyók a kibocsátási folyamat lépéseit.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>OAuth-kapcsolatok engedélyezése

Az üzembe helyezés után a logikai alkalmazás teljes körűen működik, és érvényes paraméterekkel rendelkezik. A [hitelesítő adatok hitelesítéséhez](../active-directory/develop/authentication-scenarios.md)azonban továbbra is engedélyezni kell minden OAuth-kapcsolatot, hogy érvényes hozzáférési jogkivonatokat állítson elő. Az alábbi módokon engedélyezheti az OAuth-kapcsolatokat:

* Automatikus telepítés esetén olyan parancsfájlt használhat, amely az egyes OAuth-kapcsolatok beleegyezik. Íme egy példa a GitHub-szkriptre a [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projektben.

* A OAuth-kapcsolatok manuális engedélyezéséhez nyissa meg a logikai alkalmazást a Logic app Designerben, vagy a Azure Portal vagy a Visual Studióban. A tervezőben engedélyezze a szükséges kapcsolatokat.

Ha a kapcsolatok [engedélyezése helyett egy](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory (Azure ad) szolgáltatásnevet használ, megtudhatja, hogyan [adhat meg egyszerű szolgáltatásnév-paramétereket a logikai alkalmazás sablonjában](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Logikai alkalmazások figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)

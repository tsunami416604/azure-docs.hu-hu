---
title: Logikaialkalmazás-sablonok üzembe helyezése
description: Megtudhatja, hogyan telepítheti a Azure Logic Appshoz létrehozott Azure Resource Manager sablonokat
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/01/2019
ms.openlocfilehash: 506c518e8bdd889c585feb92f2a6baee8ba0147c
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979087"
---
# <a name="deploy-azure-resource-manager-templates-for-azure-logic-apps"></a>Azure Logic Apps Azure Resource Manager sablonjainak üzembe helyezése

Miután létrehozott egy Azure Resource Manager sablont a logikai alkalmazáshoz, a következő módon telepítheti a sablont:

* [Azure Portal](#portal)
* [Visual Studio](#visual-studio)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Azure Resource Manager REST API](../azure-resource-manager/templates/deploy-rest.md)
* [Azure DevOps Azure-folyamatok](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Üzembe helyezés Azure Portal

A Logic app-sablonok Azure-ba történő automatikus üzembe helyezéséhez válassza a következő **üzembe helyezés az Azure** -ban gombot, amely bejelentkezik a Azure Portalba, és megkéri a logikai alkalmazással kapcsolatos információk megadására. Ezután elvégezheti a logikai alkalmazás sablonjának vagy paramétereinek a szükséges módosításait.

[![Üzembe helyezés az Azure-ban](./media/logic-apps-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ha például a Azure Portalba való bejelentkezés után kéri a következő információkat:

* Azure-előfizetés neve
* Használni kívánt erőforráscsoport
* Logikai alkalmazás helye
* A logikai alkalmazás neve
* Egy teszt URI
* A megadott feltételek és Kikötések elfogadása

További információt az alábbi témakörökben talál:

* [Áttekintés: a Logic apps üzembe helyezésének automatizálása Azure Resource Manager-sablonokkal](logic-apps-azure-resource-manager-templates-overview.md)
* [Erőforrások üzembe helyezése Azure Resource Manager-sablonokkal és a Azure Portal](../azure-resource-manager/templates/deploy-portal.md)

<a name="visual-studio"></a>

## <a name="deploy-with-visual-studio"></a>Üzembe helyezés a Visual Studio használatával

Ha a Visual Studióval létrehozott Azure erőforráscsoport-projektből szeretne logikai alkalmazást telepíteni, kövesse az alábbi [lépéseket a logikai alkalmazás manuális üzembe helyezéséhez](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure) az Azure-ban.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

Egy adott *Azure-erőforráscsoport*üzembe helyezéséhez használja a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

További információt az alábbi témakörökben talál:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure parancssori felületén keresztül

Egy adott *Azure-erőforráscsoport*üzembe helyezéséhez használja a következő parancsot:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

További információt az alábbi témakörökben talál:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével](../azure-resource-manager/templates/deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Üzembe helyezés az Azure DevOps

A logikai alkalmazások sablonjainak üzembe helyezéséhez és a környezetek kezeléséhez a csapatok általában olyan eszközt használnak, mint például az Azure- [DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services)az Azure- [folyamatok](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) . Az Azure-folyamatok egy [Azure erőforráscsoport-telepítési feladatot](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) biztosítanak, amelyet bármely Build-vagy kiadási folyamathoz hozzáadhat. A kiadási folyamat üzembe helyezésének és létrehozásának engedélyezéséhez szükség van egy Azure Active Directory (AD) [egyszerű szolgáltatásra](../active-directory/develop/app-objects-and-service-principals.md)is. További információ az [egyszerű szolgáltatások Azure-folyamatokkal való használatáról](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

További információ a folyamatos integrációról és a folyamatos üzembe helyezésről (CI/CD) az Azure-folyamatokkal rendelkező Azure Resource Manager-sablonokkal kapcsolatban: a következő témakörökben és példákban:

* [Resource Manager-sablonok integrálása az Azure-folyamatokkal](../azure-resource-manager/templates/add-template-to-azure-pipelines.md)
* [Oktatóanyag: Azure Resource Manager-sablonok folyamatos integrálása az Azure-folyamatokkal](../azure-resource-manager/templates/template-tutorial-use-azure-pipelines.md)
* [Minta: Kapcsolódás Azure Service Bus várólistákhoz Azure Logic Apps és üzembe helyezése Azure-folyamatokkal az Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-service-bus-queues-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Kapcsolódás Azure Storage-fiókokhoz Azure Logic Apps és üzembe helyezés az Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-azure-storage-accounts-from-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: Function app-művelet beállítása az Azure DevOps Azure-folyamatokkal való üzembe helyezéséhez és üzembe helyezéséhez Azure Logic Apps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/set-up-an-azure-function-app-action-for-azure-logic-apps-and-deploy-with-azure-devops-pipelines/)
* [Minta: csatlakozás egy integrációs fiókhoz Azure Logic Apps és üzembe helyezése Azure-folyamatokkal az Azure DevOps](https://docs.microsoft.com/samples/azure-samples/azure-logic-apps-deployment-samples/connect-to-an-integration-account-from-azure-logic-apps-and-deploy-by-using-azure-devops-pipelines/)

Az Azure-folyamatok használatának általános magas szintű lépései:

1. Hozzon létre egy üres folyamatot az Azure-folyamatokban.

1. Válassza ki a folyamathoz szükséges erőforrásokat, például a logikai alkalmazás sablonját és a sablon paramétereit tartalmazó fájlokat, amelyeket manuálisan vagy a létrehozási folyamat részeként hoz létre.

1. Az ügynök feladatához keresse meg és adja hozzá az **Azure-erőforráscsoport telepítési** feladatát.

   !["Azure Resource Group Deployment" feladat hozzáadása](./media/logic-apps-deploy-azure-resource-manager-templates/add-azure-resource-group-deployment-task.png)

1. Konfigurálás egy [egyszerű szolgáltatással](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure).

1. Adjon hozzá hivatkozásokat a logikai alkalmazás sablonja és a sablon paramétereinek fájljaihoz.

1. Szükség szerint folytassa a kiadási folyamat lépéseit bármilyen más környezet, automatizált teszt vagy jóváhagyó esetében.

<a name="authorize-oauth-connections"></a>

## <a name="authorize-oauth-connections"></a>OAuth-kapcsolatok engedélyezése

Az üzembe helyezés után a logikai alkalmazás teljes körűen működik, és érvényes paraméterekkel rendelkezik. A [hitelesítő adatok hitelesítéséhez](../active-directory/develop/authentication-scenarios.md)azonban továbbra is engedélyezni kell minden OAuth-kapcsolatot, hogy érvényes hozzáférési jogkivonatokat állítson elő. Az alábbi módokon engedélyezheti az OAuth-kapcsolatokat:

* Automatikus telepítés esetén olyan parancsfájlt használhat, amely az egyes OAuth-kapcsolatok beleegyezik. Íme egy példa a GitHub-szkriptre a [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) projektben.

* A OAuth-kapcsolatok manuális engedélyezéséhez nyissa meg a logikai alkalmazást a Logic app Designerben, vagy a Azure Portal vagy a Visual Studióban. A tervezőben engedélyezze a szükséges kapcsolatokat.

Ha a kapcsolatok engedélyezése helyett egy Azure Active Directory (Azure AD [) szolgáltatásnevet használ](../active-directory/develop/app-objects-and-service-principals.md) , megtudhatja, hogyan [adhat meg egyszerű szolgáltatásnév-paramétereket a logikai alkalmazás sablonjában](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Logikai alkalmazások figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)

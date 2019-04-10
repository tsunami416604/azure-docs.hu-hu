---
title: Az Azure Resource Manager-sablonok – Azure Logic Apps rendelkező logikai alkalmazások üzembe helyezése
description: A logic apps telepítése Azure Resource Manager-sablonok használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: bbb10bf0174b6e06e28d171510345ed92b6642d9
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59357070"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>A logic apps az Azure Resource Manager-sablonok üzembe helyezése

Miután létrehozott egy Azure Resource Manager-sablon a logikai alkalmazás üzembe helyezéséhez, helyezheti üzembe a sablont a következő lehetőségeket biztosítva:

* [Azure Portal](#portal)
* [Azure PowerShell](#powershell)
* [Azure CLI](#cli)
* [Az Azure Resource Manager REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Az Azure DevOps Azure folyamatok](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Az Azure portal használatával üzembe helyezése

Automatikus üzembe helyezéséhez a logikaialkalmazás-sablon az Azure-ba, válassza ki a következő **üzembe helyezés az Azure** gombra, amely az Azure Portalra jelentkezik be, és kéri a logikai alkalmazással kapcsolatos adatok. Ezután a logikaialkalmazás-sablon vagy a paraméterek győződjön meg a szükséges módosításokat.

[![Daz Azure-bA eploy](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Ha például kér ezeket az információkat az Azure Portalra való bejelentkezés után:

* Azure-előfizetés neve
* Használni kívánt erőforráscsoportot
* Logikai alkalmazás helye
* A logikai alkalmazás neve
* Egy teszt URI
* A megadott feltételek és kikötések elfogadása

További információkért lásd: [erőforrások üzembe helyezése Azure Resource Manager-sablonokkal és az Azure Portalon](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="authorize-oauth-connections"></a>OAuth-kapcsolatok engedélyezése

Az üzembe helyezést követően az a logikai alkalmazás – teljes körű érvényes paraméterekkel működik. Azonban továbbra is engedélyeznie kell egy érvényes hozzáférési jogkivonatot az OAuth-kapcsolatok. Az automatikus központi telepítése esetén használhatja egy parancsfájlt, amely minden OAuth-kapcsolat esetén például ez hozzájárul [példaszkript a GitHub LogicAppConnectionAuth projekt](https://github.com/logicappsio/LogicAppConnectionAuth). OAuth-kapcsolatok az Azure Portalon keresztül, vagy a Visual Studióban nyissa meg a logikai alkalmazás a Logic Apps Designerben is engedélyezhető.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Üzembe helyezés az Azure PowerShell-lel

Egy adott telepítése *Azure-erőforráscsoport*, használja a következő parancsot:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

Egy adott Azure-előfizetéssel szeretné telepíteni, használja ezt a parancsot:

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](/powershell/module/az.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

Egy adott telepítése *Azure-erőforráscsoport*, használja a következő parancsot:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Egy adott Azure-előfizetéssel szeretné telepíteni, használja ezt a parancsot:

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

További információkért lásd az alábbi témakörök: 

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure parancssori felületével](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Üzembe helyezés az Azure DevOps

Logic app-sablonok üzembe helyezése, és kezelheti a környezeteket, teams leggyakrabban használt eszköz például [Azure folyamatok](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) a [Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Az Azure folyamatok biztosít egy [Azure erőforráscsoport-telepítés a feladat](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) , hogy minden build ad hozzá, vagy kibocsátásában.
Engedélyezési üzembe helyezéséhez, és hozzon létre a kiadási folyamathoz, is szüksége lesz egy Azure Active Directory (AD) [szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md). Tudjon meg többet [szolgáltatásnevek használatával Azure folyamatok](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

Az alábbiakban az általános magas szintű lépései Azure folyamatok használatával:

1. Az Azure-folyamatok hozzon létre egy üres folyamatot.

1. Válassza ki a folyamat, például a logikaialkalmazás-sablon és a sablon paramétereit fájlokat, amelyek hoz létre manuálisan, vagy a létrehozási folyamat részeként a szükséges erőforrásokat.

1. Az ügynök feladatot, kereshet és adhat hozzá a **Azure erőforráscsoport-telepítés** feladat.

   !["Az Azure erőforráscsoport-telepítés" feladat hozzáadása](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. Állítson be egy [szolgáltatásnév](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

1. Adja hozzá a logikaialkalmazás-sablon és paraméterek sablonfájlokat hivatkozásokat.

1. Továbbra is hozhatja létre a környezet, automatizált tesztelési vagy igény szerint a jóváhagyók a kibocsátási folyamat lépéseit.

## <a name="get-support"></a>Támogatás kérése

A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Logikai alkalmazások figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)

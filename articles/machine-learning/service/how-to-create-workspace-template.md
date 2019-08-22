---
title: Munkaterület létrehozása Azure Resource Manager sablonnal
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan hozhat létre új Azure Machine Learning szolgáltatás-munkaterületet egy Azure Resource Manager sablon használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/16/2019
ms.custom: seoapril2019
ms.openlocfilehash: 89c24512e50dedbf10d145088ec77c2e6e303d1e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873153"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning-service"></a>Munkaterületek létrehozása Azure Machine Learning szolgáltatáshoz Azure Resource Manager sablon használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning szolgáltatás-munkaterületet Azure Resource Manager sablonok használatával. A Resource Manager-sablonok segítségével egyszerűen hozhat létre erőforrásokat egyetlen, koordinált műveletként. A sablon egy JSON-dokumentum, amely meghatározza a központi telepítéshez szükséges erőforrásokat. Emellett telepítési paramétereket is megadhat. A paraméterek a sablon használatakor a bemeneti értékek biztosítására szolgálnak.

További információ: [alkalmazások központi telepítése Azure Resource Manager sablonnal](../../azure-resource-manager/resource-group-template-deploy.md).

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha még nem rendelkezik ilyennel, próbálja ki [Azure Machine learning szolgáltatás ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* Ha a parancssori felületről szeretne sablont használni, [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) vagy az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-t kell használnia.

## <a name="resource-manager-template"></a>Resource Manager-sablon

A következő Resource Manager-sablon használatával létrehozhat egy Azure Machine Learning szolgáltatás-munkaterületet és a hozzá tartozó Azure-erőforrásokat:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Ez a sablon a következő Azure-szolgáltatásokat hozza létre:

* Azure-erőforráscsoport
* Azure Storage-fiók
* Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Azure Machine Learning-munkaterület

Az erőforráscsoport az a tároló, amely a szolgáltatásokat tárolja. A Azure Machine Learning munkaterület különböző szolgáltatásokat igényel.

A példában szereplő sablonnak két paramétere van:

* A **hely** , ahol az erőforráscsoport és a szolgáltatások létre lesznek hozva.

    A sablon a legtöbb erőforráshoz kiválasztott helyet fogja használni. A kivétel a Application Insights szolgáltatás, amely nem érhető el a többi szolgáltatás összes helyén. Ha olyan helyet választ, ahol nem érhető el, a szolgáltatás az USA déli középső régiójában lesz létrehozva.

* A **munkaterület neve**, amely a Azure Machine learning munkaterület rövid neve.

    A többi szolgáltatás neve véletlenszerűen jön létre.

A sablonokkal kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Azure Resource Manager sablonok szerzője](../../azure-resource-manager/resource-group-authoring-templates.md)
* [Alkalmazás üzembe helyezése Azure Resource Manager-sablonokkal](../../azure-resource-manager/resource-group-template-deploy.md)
* [Microsoft. MachineLearningServices erőforrástípusok](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="use-the-azure-portal"></a>Az Azure Portal használata

1. Kövesse az [erőforrások telepítése egyéni sablonból](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template)című témakör lépéseit. Amikor megérkezik a __Sablon szerkesztése__ képernyőre, illessze be a sablont a dokumentumból.
1. A sablon használatához válassza a __Mentés__ lehetőséget. Adja meg a következő információkat, és fogadja el a felsorolt feltételeket és kikötéseket:

   * Előfizetés: Válassza ki az erőforrásokhoz használni kívánt Azure-előfizetést.
   * Erőforráscsoport: Válasszon ki vagy hozzon létre egy erőforráscsoportot, amely tartalmazza a szolgáltatásokat.
   * Munkaterület neve: A létrehozandó Azure Machine Learning munkaterülethez használandó név. A munkaterület nevének 3 és 33 karakter közöttinek kell lennie. Csak alfanumerikus karaktereket és "-" karaktert tartalmazhat.
   * Helyen Válassza ki azt a helyet, ahová létre kívánja hozni az erőforrásokat.

További információ: [erőforrások központi telepítése egyéni sablonból](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Azure PowerShell használatával

Ez a példa feltételezi, hogy mentette a sablont egy nevű `azuredeploy.json` fájlba az aktuális könyvtárban:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace"
```

További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és Azure PowerShell](../../azure-resource-manager/resource-group-template-deploy.md) , valamint [saját Resource Manager-sablon üzembe helyezése sas-jogkivonat és Azure PowerShell segítségével](../../azure-resource-manager/resource-manager-powershell-sas-token.md).

## <a name="use-azure-cli"></a>Az Azure parancssori felület használatával

Ez a példa feltételezi, hogy mentette a sablont egy nevű `azuredeploy.json` fájlba az aktuális könyvtárban:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus
```

További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md) -vel, valamint [saját Resource Manager-sablon üzembe helyezése sas-JOGKIVONAT és Azure CLI használatával](../../azure-resource-manager/resource-manager-cli-sas-token.md).

## <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault hozzáférési szabályzat és Azure Resource Manager sablonok

Ha Azure Resource Manager sablonnal hozza létre a munkaterületet és a hozzá tartozó erőforrásokat (beleértve a Azure Key Vault), többször is. Például a sablon többszöri használata ugyanazzal a paraméterekkel, mint a folyamatos integráció és üzembe helyezési folyamat részeként.

A sablonokon keresztül a legtöbb erőforrás-létrehozási művelet idempotens, de Key Vault törli a hozzáférési házirendeket a sablon használatakor. A hozzáférési házirendek törlése megszakítja a hozzáférést a Key Vault az azt használó meglévő munkaterületekhez. Előfordulhat például, hogy a Azure Notebooks virtuális gép működőképességének leállítása/létrehozása sikertelen.  

A probléma elkerüléséhez a következő módszerek egyikét javasoljuk:

*  A sablont ne telepítse többször ugyanarra a paraméterekre. Vagy törölje a meglévő erőforrásokat, mielőtt a sablon használatával újra létrehozza őket.
  
* Vizsgálja meg a Key Vault hozzáférési házirendeket, majd használja ezeket a házirendeket a sablon accessPolicies tulajdonságának beállításához.
* Ellenőrizze, hogy a Key Vault erőforrás már létezik-e. Ha igen, ne hozza létre újra a sablonon keresztül. Hozzáadhat például egy olyan paramétert, amely lehetővé teszi, hogy letiltsa a Key Vault erőforrás létrehozását, ha az már létezik.

## <a name="next-steps"></a>További lépések

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és Resource Manager-Rest APIokkal](../../azure-resource-manager/resource-group-template-deploy-rest.md).
* [Azure-erőforráscsoportok létrehozása és üzembe helyezése a Visual Studióval](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

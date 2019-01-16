---
title: Resource Manager üzembe helyezések hibaelhárítása |} A Microsoft Docs
description: Megtudhatja, hogyan monitorozást és hibaelhárítást végezhet a Resource Managerben üzemelő példányokhoz.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 89d1573ff03771e5229e1ce28bb4ee7ecec702e3
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332817"
---
# <a name="tutorial-troubleshoot-resource-manager-template-deployments"></a>Oktatóanyag: Resource Manager-sablon üzembe helyezés hibaelhárítása

Útmutató Resource Manager-sablon üzembehelyezési hibák elhárítása. Ebben az oktatóanyagban két hibák egy sablont a beállítása, és a vizsgálati naplók és üzembe helyezési előzmények használata a problémák megoldásához.

Sablon üzemelő példányhoz kapcsolódó hibák két típusa van:

- **Érvényesítési hibák** keletkeznek-forgatókönyvek üzembe helyezés előtt lehet meghatározni. A sablonban, vagy túllépné az előfizetési kvóták erőforrásokat üzembe helyezni kívánt tartalmazzák szintaxishibáinak. 
- **Telepítési hibák** ugyanabból a feltételek a központi telepítési folyamat során. Ezek tartalmazzák az elérni kívánt olyan erőforrások, amelyek párhuzamosan lesz üzembe helyezve.

Mindkét típusú hibák, amellyel a telepítés hibáinak hibakódot adja vissza. Mindkét típusú hibák jelennek meg a tevékenységnaplóban. Azonban érvényesítési hibák nem jelennek meg az üzembe helyezési előzmények, mert az üzembe helyezés soha nem indította el.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Problémás sablon létrehozása
> * Érvényesítési hibák elhárítása
> * Üzembehelyezési hibák elhárítása
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

- [Visual Studio Code](https://code.visualstudio.com/) [Resource Manager Tools bővítménnyel](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="create-a-problematic-template"></a>Problémás sablon létrehozása

Nevű sablon megnyitása [hozzon létre egy standard szintű tárfiókot](https://azure.microsoft.com/resources/templates/101-storage-account-create/) a [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/), és állítson be két sablonokkal kapcsolatos problémák.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. Módosítsa a **apiVersion** sor a következő sort:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **apiVersion1** érvénytelen elem neve. Érvényesítési hiba.
    - Az API-verziót kell "2018-07-01".  Központi telepítési hiba.

5. A **File** (Fájl) > **Save as** (Mentés másként) lehetőség kiválasztásával mentheti a fájlt a helyi számítógépre, **azuredeploy.json** néven.

## <a name="troubleshoot-the-validation-error"></a>Az érvényesítési hiba elhárítása

Tekintse meg a [helyezheti üzembe a sablont](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) szakasz a sablon üzembe helyezéséhez.

Ekkor hibaüzenetet kap a rendszerhéjból hasonló:

```
New-AzureRmResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

A hibaüzenet azt jelzi, hogy a probléma van **apiVersion1**.

Hárítsa el a problémát úgy módosítja a Visual Studio Code használatával **apiVersion1** való **apiVersion**, majd mentse a sablont.

## <a name="troubleshoot-the-deployment-error"></a>A központi telepítési hiba elhárítása

Tekintse meg a [helyezheti üzembe a sablont](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) szakasz a sablon üzembe helyezéséhez.

Ekkor hibaüzenetet kap a rendszerhéjból hasonló:

```
New-AzureRmResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

A központi telepítési hiba található a következő eljárás segítségével az Azure Portalról:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg az erőforráscsoport kiválasztásával **erőforráscsoportok** majd az erőforráscsoport neve. Meg kell jelennie **1 sikertelen** alatt **üzembe helyezési**.

    ![Resource Manager oktatóanyagában hibaelhárítása](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Válassza ki **hiba részleteinek**.

    ![Resource Manager oktatóanyagában hibaelhárítása](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    A hibaüzenet: ugyanaz, mint korábban bemutatott:

    ![Resource Manager oktatóanyagában hibaelhárítása](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

A hiba a tevékenységnaplókból is talál:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **figyelő** > **tevékenységnapló**.
3. A szűrők segítségével a naplóban találja.

    ![Resource Manager oktatóanyagában hibaelhárítása](./media/resource-manager-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

A Visual Studio Code használatával hárítsa el a problémát, és ezt követően telepítse újra a sablont.

Gyakori hibák listáját lásd: [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](./resource-manager-common-deployment-errors.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan Resource Manager-sablon üzembehelyezési hibák elhárítása.  További információkért lásd: [hibáinak elhárítása a közös Azure-beli hibák az Azure Resource Manager](./resource-manager-common-deployment-errors.md).
---
title: Üzemelő példányok hibaelhárítása
description: Megtudhatja, hogyan figyelheti és elháríthatja Azure Resource Manager sablon központi telepítéseit. Megjeleníti a tevékenységek naplóit és az üzembe helyezési előzményeket.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 68ddb5c07ffac2aad4e2dafd16301fa29f391797
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86119344"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Oktatóanyag: ARM-sablonok üzembe helyezésének hibakeresése

Ismerje meg, hogyan lehet elhárítani a Azure Resource Manager (ARM) sablon telepítési hibáit. Ebben az oktatóanyagban két hibát állít be egy sablonban, és megtudhatja, hogyan oldja meg a problémákat a tevékenységek naplói és a telepítési előzmények használatával.

A sablon üzembe helyezéséhez két típusú hiba van:

- Az **érvényesítési hibák** az üzembe helyezés előtt meghatározható forgatókönyvek esetén jelentkeznek. Ezek közé tartoznak a sablon szintaxishibái, vagy az olyan erőforrások üzembe helyezése, amelyek túllépik az előfizetése kvótáit.
- A **telepítési hibák** a telepítési folyamat során felmerülő feltételekből származnak. Ezek közé tartozik például egy olyan erőforrás elérésére tett kísérlet, amelynek az üzembe helyezése párhuzamosan zajlik.

Mindkét típusú hiba az üzembe helyezés hibaelhárításához használható hibakódot ad vissza. Mindkét típusú hiba megjelenik a tevékenységnaplóban. Az érvényesítési hibák azonban nem jelennek meg az üzembe helyezési előzmények között, mert az üzembe helyezés el sem indult.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> - Problémás sablon létrehozása
> - Érvényesítési hibák elhárítása
> - Üzembehelyezési hibák elhárítása
> - Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

- Visual Studio CodeResource Manager Tools bővítménnyel. Tekintse meg a rövid útmutató [: Azure Resource Manager sablonok létrehozása Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)-ban című témakört.

## <a name="create-a-problematic-template"></a>Problémás sablon létrehozása

Nyisson meg egy [standard Storage-fiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/) az [Azure gyorsindítási sablonokból](https://azure.microsoft.com/resources/templates/)nevű sablont, és állítson be két sablonbeli problémát.

1. A Visual Studio Code-ból válassza a **fájl** > **megnyitott**fájl elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. Módosítsa a **apiVersion** sort a következő sorba:

    ```json
    "apiVersion1": "2018-07-02",
    ```

    - a **apiVersion1** érvénytelen az elem neve. Érvényesítési hiba.
    - Az API-verzió "2018-07-01".  Központi telepítési hiba.

5. Válassza a **fájl** > **Mentés másként** lehetőséget, hogy a fájlt **azuredeploy.jsa** helyi számítógépre mentse.

## <a name="troubleshoot-the-validation-error"></a>Az érvényesítési hiba megoldása

A sablon üzembe helyezéséhez tekintse meg a [sablon üzembe helyezése](template-tutorial-create-multiple-instances.md#deploy-the-template) szakaszt.

A rendszerhéj a következőhöz hasonló hibaüzenetet kap:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

A hibaüzenet azt jelzi, hogy a probléma a **apiVersion1**.

A Visual Studio Code segítségével javítsa ki a problémát úgy, hogy módosítja a **apiVersion1** a **apiVersion**, majd menti a sablont.

## <a name="troubleshoot-the-deployment-error"></a>A központi telepítési hiba megoldása

A sablon üzembe helyezéséhez tekintse meg a [sablon üzembe helyezése](template-tutorial-create-multiple-instances.md#deploy-the-template) szakaszt.

A rendszerhéj a következőhöz hasonló hibaüzenetet kap:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

Az üzembe helyezési hiba a Azure Portal az alábbi eljárással érhető el:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Nyissa meg az erőforráscsoportot az **erőforráscsoportok** , majd az erőforráscsoport neve lehetőség kiválasztásával. Az üzemelő **példányban** **1 sikertelen hiba** jelenik meg.

    ![Resource Manager-oktatóanyag – problémamegoldás](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Válassza ki a **hiba részleteit**.

    ![Resource Manager-oktatóanyag – problémamegoldás](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    A hibaüzenet ugyanaz, mint a korábban bemutatott:

    ![Resource Manager-oktatóanyag – problémamegoldás](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

A hibát a tevékenység naplóiból is megtalálhatja:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **figyelő**  >  **tevékenység naplója**lehetőséget.
3. A szűrők segítségével keresse meg a naplót.

    ![Resource Manager-oktatóanyag – problémamegoldás](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

Javítsa ki a problémát a Visual Studio Code használatával, majd telepítse újra a sablont.

A gyakori hibák listáját lásd: [gyakori Azure-telepítési hibák elhárítása Azure Resource Managersal](common-deployment-errors.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan lehet elhárítani az ARM-sablonok telepítési hibáit.  További információ: [Az Azure központi telepítési hibáinak elhárítása Azure Resource Managersal](common-deployment-errors.md).

---
title: Üzemelő példányok hibaelhárítása
description: Ismerje meg, hogyan figyelheti és háríthatja el az Azure Resource Manager-sablon-telepítések felügyeletét és hibaelhárítását. A tevékenységnaplók és a telepítési előzmények megjelenítése.
author: mumian
ms.date: 01/15/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 30b66414e87f642bc72b8723ebff57f2e9009f17
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239237"
---
# <a name="tutorial-troubleshoot-arm-template-deployments"></a>Oktatóanyag: Arm-sablon-telepítések hibáinak elhárítása

Ismerje meg, hogyan háríthatja el az Azure Resource Manager (ARM) sablontelepítési hibáit. Ebben az oktatóanyagban két hibát állíthat be egy sablonban, és megtudhatja, hogyan használhatja a tevékenységnaplókat és a telepítési előzményeket a problémák megoldásához.

A sablon telepítésével kapcsolatban kétféle hiba típus létezik:

- **Az érvényesítési hibák** olyan forgatókönyvekből erednek, amelyek a telepítés előtt meghatározhatók. Ezek közé tartoznak a sablon szintaxishibái, vagy az olyan erőforrások üzembe helyezése, amelyek túllépik az előfizetése kvótáit.
- **A telepítési hibák** a központi telepítési folyamat során előforduló körülményekből erednek. Ezek közé tartozik például egy olyan erőforrás elérésére tett kísérlet, amelynek az üzembe helyezése párhuzamosan zajlik.

Mindkét típusú hiba az üzembe helyezés hibaelhárításához használható hibakódot ad vissza. Mindkét típusú hiba megjelenik a tevékenységnaplóban. Az érvényesítési hibák azonban nem jelennek meg az üzembe helyezési előzmények között, mert az üzembe helyezés el sem indult.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Problémás sablon létrehozása
> * Érvényesítési hibák elhárítása
> * Üzembehelyezési hibák elhárítása
> * Az erőforrások eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Lásd: [A Visual Studio-kód használata ARM-sablonok létrehozásához.](use-vs-code-to-create-template.md)

## <a name="create-a-problematic-template"></a>Problémás sablon létrehozása

Nyisson meg [egy szabványos tárfiókot az](https://azure.microsoft.com/resources/templates/101-storage-account-create/) Azure [QuickStart sablonokból](https://azure.microsoft.com/resources/templates/)nevű sablont, és állítson be két sablonproblémát.

1. A Visual Studio-kódból válassza a **Fájlmegnyitása**>**fájl**lehetőséget.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. Módosítsa az **apiVersion** sort a következő sorra:

    ```json
    "apiVersion1": "2018-07-02",
    ```
    - **Az apiVersion1** elemnév érvénytelen. Ez egy érvényesítési hiba.
    - Az API-verzió "2018-07-01".  Ez egy telepítési hiba.

5. Válassza a **Fájlmentés**>**másként** lehetőséget a fájl **azuredeploy.json néven** a helyi számítógépre történő mentéséhez.

## <a name="troubleshoot-the-validation-error"></a>Érvényesítési hiba elhárítása

A sablon [üzembe helyezéséhez](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) tekintse meg a sablon telepítése szakaszt.

A rendszerhéjból a következőhöz hasonló hibát kell kapnia:

```
New-AzResourceGroupDeployment : 4:29:24 PM - Error: Code=InvalidRequestContent; Message=The request content was invalid and could not be deserialized: 'Could not find member 'apiVersion1' on object of type 'TemplateResource'. Path 'properties.template.resources[0].apiVersion1', line 36, position 24.'.
```

A hibaüzenet azt jelzi, hogy a probléma az **apiVersion1.**

A Visual Studio Code segítségével javítsa ki a problémát az apiVersion1 **apiVersion (apiVersion)** módosításával, majd mentse a sablont. **apiVersion1**

## <a name="troubleshoot-the-deployment-error"></a>A telepítési hiba elhárítása

A sablon [üzembe helyezéséhez](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) tekintse meg a sablon telepítése szakaszt.

A rendszerhéjból a következőhöz hasonló hibát kell kapnia:

```
New-AzResourceGroupDeployment : 4:48:50 PM - Resource Microsoft.Storage/storageAccounts 'storeqii7x2rce77dc' failed with message '{
  "error": {
    "code": "NoRegisteredProviderFound",
    "message": "No registered resource provider found for location 'centralus' and API version '2018-07-02' for type 'storageAccounts'. The supported api-versions are '2018-07-01, 2018-03-01-preview, 2018-02-01, 2017-10-01, 2017-06-01, 2016-12-01, 2016-05-01, 2016-01-01, 2015-06-15, 2015-05-01-preview'. The supported locations are 'eastus, eastus2, westus, westeurope, eastasia, southeastasia, japaneast, japanwest, northcentralus, southcentralus, centralus, northeurope, brazilsouth, australiaeast, australiasoutheast, southindia, centralindia, westindia, canadaeast, canadacentral, westus2, westcentralus, uksouth, ukwest, koreacentral, koreasouth, francecentral'."
  }
}'
```

A telepítési hiba az Azure Portalon található az alábbi eljárás sal:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Nyissa meg az erőforráscsoportot az **Erőforráscsoportok,** majd az erőforráscsoport nevének kiválasztásával. A Telepítés csoportban **az 1 Sikertelen (Sikertelen)** **jelenik meg.**

    ![Erőforrás-kezelő oktatóanyag – hibaelhárítás](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error.png)
3. Válassza a **Hiba részletei lehetőséget.**

    ![Erőforrás-kezelő oktatóanyag – hibaelhárítás](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-details.png)

    A hibaüzenet megegyezik a korábban láthatóval:

    ![Erőforrás-kezelő oktatóanyag – hibaelhárítás](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-error-summary.png)

A hiba a tevékenységnaplókból is megtalálható:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza **a Tevékenységnapló figyelése** > **lehetőséget.**
3. A szűrők segítségével keresse meg a naplót.

    ![Erőforrás-kezelő oktatóanyag – hibaelhárítás](./media/template-tutorial-troubleshoot/resource-manager-template-deployment-activity-log.png)

A Visual Studio-kód segítségével javítsa ki a problémát, majd telepítse újra a sablont.

A gyakori hibák listáját az [Azure Resource Manager gyakori Azure-telepítési hibák elhárítása című témakörben talál.](common-deployment-errors.md)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan háríthatja el az ARM-sablon telepítési hibáit.  További információt az [Azure Resource Manager gyakori Azure-telepítési hibák elhárítása című témakörben](common-deployment-errors.md)talál.

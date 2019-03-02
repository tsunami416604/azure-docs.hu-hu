---
title: Az Azure Resource Manager-sablon létrehozása
description: Az Azure Resource Manager-sablon írásának folyamatát ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2018
ms.author: tomfitz
ms.openlocfilehash: a802b12f34c5fe16202f55416a698823e55c01bb
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245897"
---
# <a name="create-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása

Ez a cikk ismerteti a folyamatot és a döntések az Azure Resource Manager-sablon létrehozásakor. Példák és szolgáltatásokat, amelyek segítségére lehetnek, ha a sablont készít áttekintést biztosít. A cikk feltételezi, hogy erőforrásokat egy erőforráscsoportba helyezi üzembe. Erőforrások üzembe helyezése az Azure-előfizetéshez, például az Azure-házirendek vagy a szerepköralapú hozzáférés-vezérlési hozzárendelések létrehozása, lásd: ilyenkor [erőforráscsoportokat és erőforrásokat az Azure-előfizetés létrehozása](deploy-to-subscription.md).

## <a name="select-json-editor"></a>Válassza ki a JSON-szerkesztő

A Resource Manager-sablon egy JSON-fájlt. Szüksége lesz egy helyes működését a JSON-fájlt az Authoring Tool eszközben. Számos lehetőség áll rendelkezésre, de ha még nem rendelkezik egy használni kívánt szerkesztővel telepítse [Visual Studio Code-ot (a VS Code)](https://code.visualstudio.com/). 

Miután telepítette a VS Code, adja hozzá a [Azure Resource Manager Tools bővítmény](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). A bővítmény hozzáadja, amelyekkel leegyszerűsíthető a sablon készítése számos funkciót.

![Template Visual Studio Code](./media/how-to-create-template/template-visual-studio-code.png)

A képernyőképen látható, megnyílik a Visual Studio Code egy Resource Manager-sablon. 

A Resource Manager tools bővítmény és a VS Code használata telepítési oktatóanyagért lásd: [a rövid útmutató: Az Azure Resource Manager-sablonok létrehozása a Visual Studio Code használatával](./resource-manager-quickstart-create-templates-use-visual-studio-code.md).

## <a name="understand-the-template-structure"></a>A sablon struktúra ismertetése

Tekintse át a sablonokat úgy, hogy a sablon működésének megismerése a részeit. A sablon nem rendelkezhet minden egyes szakaszban. A szakaszok azt szeretné, hogy arra koncentrálhasson, a következők:

* A [paraméterek](resource-manager-templates-parameters.md) rész, amely testre az üzembe helyezett infrastruktúra üzembe helyezése során megadhatja az értékeket mutatja. 

* A [változók](resource-group-authoring-templates.md#variables) rész, amely bemutatja a sablonban használt értékeket.

* A [funkciók](resource-group-authoring-templates.md#functions) szakaszt, amely bemutatja a sablonban használt sablon kifejezések testre szabott.

* A [erőforrások](resource-manager-templates-resources.md) rész, amely bemutatja az Azure-erőforrások, amelyek az előfizetés vannak telepítve.

* A [kimenete](resource-group-authoring-templates.md#outputs) rész, amely megjeleníti az üzembe helyezés befejezése után visszaadott értékeket.

## <a name="look-for-similar-templates"></a>Hasonló sablonok keresése

Gyakran előfordul annak egy meglévő sablont, amely üzembe helyezi egy megoldás, amely hasonlít a következőkre lesz szüksége. A [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) sablonok közösségi közreműködők a több száz rendelkezik.

![A rövid útmutató sablontár](./media/how-to-create-template/template-quickstart-repo.png)

Kereshet az adott tárházba, egy sablon, amely hasonlít a következőkre lesz szüksége. Nem probléma, ha a sablon nem csinál pontosan mit kell, testreszabhatja.

Miután megtalálta a sablont, válassza ki a **Tallózás a Githubon**, majd másolja a **azuredeploy.json** fájlt a tárházból. A VS Code-ban hozzon létre egy új fájlt **azuredeploy.json** , és adja hozzá a rövid útmutató adattárból másolt sablon fájl tartalmát.

## <a name="add-resources"></a>Erőforrások hozzáadása

Érdemes lehet, hogy pontosan mit hajtja végre a sablon testreszabásához. Először tekintse át az üzembe helyezett erőforrásokat. Szükség lehet hozzá, távolíthatja el vagy módosítsa a sablon erőforrások. Leírások és az erőforrások Szintaxis: [Azure Resource Manager sablonreferenciája](/azure/templates/).

![Sablonreferencia](./media/how-to-create-template/template-reference.png)

Után tekintse át azokat a tulajdonságokat, végezze el a szükséges módosításokat. Meghatározhatja az erőforrásokat kapcsolatos ajánlásokat, lásd: [erőforrások – ajánlott eljárások](template-best-practices.md#resources).

## <a name="add-or-remove-parameters"></a>Adja hozzá, vagy távolítsa el a paraméterek

Szükség lehet módosítani a sablon paramétereit. Adja hozzá, vagy távolítsa el a paraméterek alapján mekkora a testreszabásról szeretné engedélyezni, üzembe helyezés során. Paraméterek használatával kapcsolatos javaslatokat, lásd: [paraméterek – ajánlott eljárások](template-best-practices.md#parameters).

## <a name="add-tags"></a>Címkék hozzáadása

Címkék hozzáadása az erőforrások, hogy logikusan rendszerezhesse kategóriák szerint, és osztani a számlázási költségeket. Címkék hozzáadása egyszerűen, a JSON-t az erőforrás a alkalmazni. Például a következő tárfiók két címkékkel rendelkezik:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
      {
        "apiVersion": "2016-01-01",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
          "Dept": "Finance",
          "Environment": "Production"
        },
        "sku": {
          "name": "Standard_LRS"
        },
        "kind": "Storage",
        "properties": { }
      }
    ]
}
```

A címkék dinamikusan is alkalmazhat a paramétereket. További információkért lásd: [sablonban címkék](resource-manager-templates-resources.md#tags).

## <a name="review-template-functions"></a>Tekintse át a sablonokban használható függvények

Azt tapasztalhatja, hogy a sablonban vannak foglalt zárójelben, például a kifejezések `"[some-expression]"`. Ezek a kifejezések a sablonokban használható függvények használatával dinamikusan hozhat létre értékek üzembe helyezés során.

Például gyakran megjelenik egy kifejezés, például:

```json
"name": "[parameters('siteName')]"
```

A kifejezés egy paraméter értékének beolvasása. Az érték a name tulajdonság van hozzárendelve.

Vagy egy összetettebb kifejezést például számos funkciót használó jelenhetnek meg:

```json
"[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

A kifejezés egy olyan objektum a storage-fiók beolvasása.

Mi az funkciók, tekintse át a [sablon függvényhivatkozás](resource-group-template-functions.md) dokumentációját.

## <a name="create-more-than-one-instance"></a>Egynél több példány létrehozása

Néha szeretne létrehozni egynél több példányát egy erőforrást. Ha például szükség lehet különböző storage-fiókok. Nem, ismételje meg az erőforrás a sablonon keresztül, használhatja a `copy` szintaxis több példányt.

Az alábbi példa három tárfiókot hoz létre:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Adja meg a példányok dinamikusan és a egy paraméter is. További információkért lásd: [egynél több példányát egy erőforrást vagy tulajdonság frissítése az Azure Resource Manager-sablonok üzembe helyezése](resource-group-create-multiple.md).

## <a name="conditionally-deploy-a-resource"></a>Erőforrás feltételes üzembe helyezése

Néha meg kell adnia üzemel, hogy a sablon egy erőforrás üzembe helyezés során. Például érdemes lehet a rugalmasságot, vagy üzembe helyezése egy új erőforrást, vagy használjon egy meglévő erőforrást. A `condition` elem be- vagy kikapcsolása az erőforrás üzembe helyezési lehetővé teszi. A kifejezés a feltétel elem értéke igaz, amikor az erőforrás üzembe van helyezve. Hamis érték esetén a rendszer kihagyta az erőforrás üzembe helyezés során.

Az alábbi példa feltételesen helyezi üzembe a storage-fiók:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

További információkért lásd: a [feltétel elem](resource-manager-templates-resources.md#condition).

## <a name="review-dependencies"></a>Tekintse át a függőségek

A sablonban néhány erőforrás más erőforrások előtt telepíteni kell. Ha például az SQL server léteznie kell az SQL-adatbázis létrehozása előtt. Erőforrás-kezelő implicit módon meghatározza, hogy a telepítési sorrendet, az erőforrások során a [függvényre](resource-group-template-functions-resource.md#reference) szolgál. Azonban bizonyos esetekben szüksége explicit módon határozhatja meg a függőségek használatával a `dependsOn` elemet. Tekintse át a sablon megtekintéséhez, ha a függőségek hozzá kell adni. Ügyeljen arra, hogy nem adja hozzá a szükségtelen függőségek telepítési lelassulnak vagy körkörös hivatkozások létrehozása.

Az alábbi képen látható a különféle App Service-erőforrások esetében a függőségi sorrend:

![Webes alkalmazás függőségei](./media/how-to-create-template/web-dependencies.png)

Az alábbi példa bemutatja egy sablont, amely meghatározza a függőségek része.

```json
{
    "name": "[parameters('appName')]",
    "type": "Microsoft.Web/Sites",
    ...
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'))]",
            "[concat('Microsoft.Sql/servers/', parameters('dbServerName'), '/databases/', parameters('dbName'))]",
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('appName'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

További információkat [az erőforrások Azure Resource Manager-sablonokban történő üzembehelyezési sorrendjének meghatározását](resource-group-define-dependencies.md) ismertető témakörben talál.

## <a name="review-recommended-practices"></a>Ajánlott eljárások áttekintése

A sablon üzembe helyezése előtt tekintse át a [gyakorlati tanácsok az Azure Resource Manager-sablon](template-best-practices.md) tekintse meg, ha vannak ilyenek az ajánlott megközelítéseket szeretne végrehajtani a sablonban.

Ha a sablon használata különböző Azure-felhő környezetben van szüksége, tekintse meg [felhőalapú konzisztencia fejlesztése az Azure Resource Manager-sablonokkal](templates-cloud-consistency.md).

## <a name="next-steps"></a>További lépések

* A sablon üzembe helyezéséhez lásd: [üzembe helyezése az Azure CLI-vel](resource-group-template-deploy-cli.md) vagy [üzembe helyezés a PowerShell-lel](resource-group-template-deploy.md).
* A sablonok létrehozásának részletes rövid útmutatóban talál [létrehozása Azure Resource Manager-sablonok használatával a Visual Studio Code](resource-manager-quickstart-create-templates-use-visual-studio-code.md).
* A sablonban rendelkezésre álló függvények listája: [sablonfüggvények](resource-group-template-functions.md).

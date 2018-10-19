---
title: Összekapcsolt Azure Resource Manager-sablonok | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre összekapcsolt Azure Resource Manager-sablonokat virtuális gépek létrehozásához
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d25ca14b78465a6c4fec7e90bc20436e3ca553fc
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419627"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Oktatóanyag: Összekapcsolt Azure Resource Manager-sablonok létrehozása

Ismerje meg, hogyan hozhat létre összekapcsolt Azure Resource Manager-sablonokat. Összekapcsolt sablonok használatával az egyik sablonja meghívhat egy másikat. Ez nagyszerűen használható a sablonok modularizálásához. Ebben az oktatóanyagban ugyanazt a sablont fogja használni, mint az [Oktatóanyag: Több erőforráspéldány létrehozása Resource Manager-sablonok használatával](./resource-manager-tutorial-create-multiple-instances.md) című anyagban, amely tartalmazza egy virtuális gép, egy virtuális hálózat, illetve más függő erőforrások, köztük egy tárfiók létrehozását. El fogja különíteni a tárfiók-erőforrást egy összekapcsolt sablonhoz.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * Az összekapcsolt sablon létrehozása
> * Az összekapcsolt sablon feltöltése
> * Hivatkozás az összekapcsolt sablonra
> * Függőség konfigurációja
> * Értékek lekérése összekapcsolt sablonból
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/).
* Resource Manager Tools bővítmény.  Lásd [a bővítmény telepítését](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) ismertető részt.
* Végezze el az [Oktatóanyag: Több erőforráspéldány létrehozása Resource Manager-sablonok használatával](./resource-manager-tutorial-create-multiple-instances.md) című anyagban leírtakat.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure-beli gyorsindítási sablonok a Resource Manager-sablonok adattáraként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg. Ez a sablon megegyezik az [Oktatóanyag: Több erőforráspéldány létrehozása Resource Manager-sablonok használatával](./resource-manager-tutorial-create-multiple-instances.md) című anyagban használt sablonnal. Mentse a sablont két példányban a következő felhasználásra:

- **A fő sablon**: ez hozza létre a tárfiók kivételével az összes erőforrást.
- **Az összekapcsolt sablon**: ez hozza létre a tárfiókot.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.
5. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával hozza létre a fájl egy újabb másolatát **linkedTemplate.json** néven.

## <a name="create-the-linked-template"></a>Az összekapcsolt sablon létrehozása

Az összekapcsolt sablon létrehoz egy tárfiókot. Az összekapcsolt sablon majdnem teljesen azonos a tárfiókot létrehozó különálló sablonnal. Ebben az oktatóanyagban az összekapcsolt sablonnak vissza kell adnia egy értéket a fő sablonnak. Ez az érték az `outputs` elemben van megadva.

1. Ha még nincs megnyitva, nyissa meg a Visual Studio Code-ban a linkedTemplate.json fájlt.
2. Hajtsa végre a következő módosításokat:

    - Távolítson el a tárfiók kivételével minden erőforrást. Összesen négy erőforrást fog eltávolítani.
    - Módosítsa az **outputs** (kimenetek) elemet, hogy a következőképpen nézzen ki:

        ```json
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
        ```
        A **storageUri** a fő sablon virtuális gépének erőforrás-definíciójához szükséges.  Az értéket egy kimeneti értékként fogja átadni a fő sablonnak.
    - Távolítsa el a nem használt paramétereket. Ezek a paraméterek hullámos zöld vonallal vannak aláhúzva. Csak egyetlen paraméter, a **location** (hely) maradjon.
    - Távolítsa el a **variables** (változók) elemet. Ebben az oktatóanyagban nem lesz rájuk szükség.
    - Vegyen fel egy **storageAccountName** (tárFiókNeve) nevű paramétert. A tárfiók nevét a fő sablon egy paraméterként adja át az összekapcsolt sablonnak.

    Ha készen van, a sablonnak így kell kinéznie:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountName":{
            "type": "string",
            "metadata": {
              "description": "Azure Storage account name."
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
              "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
          }
        ],
        "outputs": {
            "storageUri": {
                "type": "string",
                "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
              }
        }
    }
    ```
3. Mentse a módosításokat.

## <a name="upload-the-linked-template"></a>Az összekapcsolt sablon feltöltése

A sablonnak elérhetőnek kell lennie arról a helyről, ahonnan az üzembe helyezést futtatja. Ez a hely lehet egy Azure-tárfiók, a Github vagy a Dropbox. Ha a sablonok érzékeny adatokat tartalmaznak, feltétlenül lássa el őket hozzáférés-védelemmel. Ebben az oktatóanyagban ugyanúgy a Cloud Shelles üzembe helyezési módszert fogja használni, mint az [Oktatóanyag: Több erőforráspéldány létrehozása Resource Manager-sablonok használatával](./resource-manager-tutorial-create-multiple-instances.md) című anyagban. A fő sablon (azuredeploy.json) a felületre lesz feltöltve. Az összekapcsolt sablont (linkedTemplate.json) meg kell osztani valahol.  Hogy kevesebb feladat legyen ebben az oktatóanyagban, már feltöltöttük az előző szakaszban létrehozott összekapcsolt sablont egy [Azure-tárfiókba](https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json).

## <a name="call-the-linked-template"></a>Az összekapcsolt sablon meghívása

A fő sablon egy azuredeploy.json nevű fájl.

1. Ha még nincs megnyitva, nyissa meg a Visual Studio Code-ban az azuredeploy.json fájlt.
2. Törölje a sablonból a tárfiók erőforrás-definícióját.
3. A tárfiók definíciójának helyén adja hozzá a következő json kódrészletet:

    ```json
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Ügyeljen ezekre a részletekre:

    - A fő sablon `Microsoft.Resources/deployments` erőforrása kapcsolja őt össze egy másik sablonnal.
    - A `deployments` erőforrásban található egy `linkedTemplate` név. Ezzel a névvel történik a [függőség konfigurálása](#configure-dependency).  
    - Összekapcsolt sablonok meghívásakor csak [Incremental](./deployment-modes.md) (Növekményes) üzembe helyezési mód használható.
    - A `templateLink/uri` tartalmazza az összekapcsolt sablon URI-azonosítóját. Az összekapcsolt sablon fel lett töltve egy megosztott tárfiókra. Módosíthatja az URI-azonosítót, ha valahova máshova tölti fel a sablont az interneten.
    - A `parameters` használatával adhat át értékeket a fő sablonból az összekapcsolt sablonba.
4. Mentse a módosításokat.

## <a name="configure-dependency"></a>Függőség konfigurációja

Bizonyára emlékszik, hogy az [Oktatóanyag: Több erőforráspéldány létrehozása Resource Manager-sablonok használatával](./resource-manager-tutorial-create-multiple-instances.md) című anyagban a virtuális gép erőforrása a tárfióktól függ:

![Az Azure Resource Manager-sablonok függőségi diagramja](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Mivel a tárfiók jelenleg az összekapcsolt sablonban van definiálva, módosítania kell a `Microsoft.Compute/virtualMachines` erőforrás következő két elemét.

- Konfigurálja újra a `dependOn` elemet. A tárfiók definíciója az összekapcsolt sablonba lett áthelyezve.
- Konfigurálja újra a `properties/diagnosticsProfile/bootDiagnostics/storageUri` elemet. [Az összekapcsolt sablon létrehozása](#create-the-linked-template) című szakaszban hozzáadott egy kimeneti értéket:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```
    Erre az értékre a fő sablonnak van szüksége.

1. Ha még nincs megnyitva, nyissa meg a Visual Studio Code-ban az azuredeploy.json fájlt.
2. Bontsa ki a virtuális gép erőforrás-definícióját, majd módosítsa a **dependsOn** elemet a következő képernyőképnek megfelelően:

    ![Az összekapcsolt Azure Resource Manager-sablonok függőségi konfigurációja ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)
    
    A „linkedTemplate” az üzembe helyezési erőforrás neve.  
3. módosítsa a **properties/diagnosticsProfile/bootDiagnostics/storageUri** elemet az előző képernyőképnek megfelelően.

További információkat az [Összekapcsolt és beágyazott sablonok használata Azure-erőforrások üzembe helyezésekor](./resource-group-linked-templates.md) című cikkben talál.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) szakaszban tekintheti meg.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban összekapcsolt sablonokat fog létrehozni és üzembe helyezni. A következő cikkben megismerheti, hogyan lehet Azure erőforrásokat több régióban üzembe helyezni, és hogyan használhatók a biztonságos üzembe helyezési eljárások:


> [!div class="nextstepaction"]
> [Az Azure Deployment Manager használata](./deployment-manager-tutorial.md)


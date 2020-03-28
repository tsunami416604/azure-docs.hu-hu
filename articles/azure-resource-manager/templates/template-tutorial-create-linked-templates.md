---
title: Hivatkozott sablonok létrehozása
description: Ismerje meg, hogyan hozhat létre összekapcsolt Azure Resource Manager-sablonokat virtuális gépek létrehozásához
author: mumian
ms.date: 12/03/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e1cce566fb7aab286c57f32d9348e51dd0a7c1ee
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239321"
---
# <a name="tutorial-create-linked-arm-templates"></a>Oktatóanyag: Csatolt ARM-sablonok létrehozása

Ismerje meg, hogyan hozhat létre csatolt Azure Resource Manager (ARM) sablonokat. Összekapcsolt sablonok használatával az egyik sablonja meghívhat egy másikat. Ez nagyszerűen használható a sablonok modularizálásához. Ebben az oktatóanyagban az oktatóanyagban használt sablont [használja: Arm-sablonok létrehozása függő erőforrásokkal,](./template-tutorial-create-templates-with-dependent-resources.md)amely létrehoz egy virtuális gépet, egy virtuális hálózatot és más függő erőforrást, beleértve a tárfiókot is. A tárfiók-erőforrás létrehozását egy csatolt sablonra bontja.

A csatolt sablon hívása olyan, mint egy függvényhívás.  Azt is megtudhatja, hogyan adhatja át a paraméterértékeket a csatolt sablonnak, és hogyan szerezheti be a "visszatérési értékeket" a csatolt sablonból.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * Az összekapcsolt sablon létrehozása
> * Az összekapcsolt sablon feltöltése
> * Hivatkozás az összekapcsolt sablonra
> * Függőség konfigurációja
> * A sablon üzembe helyezése
> * További gyakorlatok

További információ: [Csatolt és beágyazott sablonok használata az Azure-erőforrások üzembe helyezésekor](./linked-templates.md)című témakörben olvashat.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Lásd: [A Visual Studio-kód használata ARM-sablonok létrehozásához.](use-vs-code-to-create-template.md)
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```console
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információ: [Az Azure Key Vault integrálása az ARM-sablon üzembe helyezésében](./template-tutorial-use-key-vault.md)című témakörben olvashat. Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure QuickStart Templates az ARM-sablonok tárháza. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg. Ez ugyanaz a sablon, amelyet az [Oktatóanyag: Arm-sablonok létrehozása függő erőforrásokkal](./template-tutorial-create-templates-with-dependent-resources.md)használ. Mentse a sablont két példányban a következő felhasználásra:

* **A fő sablon**: ez hozza létre a tárfiók kivételével az összes erőforrást.
* **Az összekapcsolt sablon**: ez hozza létre a tárfiókot.

1. A Visual Studio-kódból válassza a **Fájlmegnyitása**>**fájl**lehetőséget.
1. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
1. A sablon hat erőforrást határoz meg:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/networkSecurityGroups`](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     A sablon testreszabása előtt érdemes a sablonséma alapvető megértését kapni.
1. Válassza a **Fájlmentés**>**másként** lehetőséget, ha a fájl egy példányát az **azuredeploy.json**nevű számítógépre szeretné menteni.
1. Válassza a **Fájlmentés**>**másként** lehetőséget, ha a fájl egy másik példányát **linkedTemplate.json**néven szeretné létrehozni.

## <a name="create-the-linked-template"></a>Az összekapcsolt sablon létrehozása

Az összekapcsolt sablon létrehoz egy tárfiókot. A csatolt sablon önálló sablonként használható tárfiók létrehozásához. Ebben az oktatóanyagban a csatolt sablon két paramétert vesz igénybe, és egy értéket visszaad a fő sablonnak. Ez a "visszatérési" érték `outputs` az elemben van definiálva.

1. Nyissa meg **a linkedTemplate.json fájlt** a Visual Studio-kódban, ha a fájl nincs megnyitva.
1. Hajtsa végre a következő módosításokat:

    * A **helytől**eltérő összes paraméter eltávolítása.
    * Vegyen fel egy **storageAccountName** (tárFiókNeve) nevű paramétert.

      ```json
      "storageAccountName":{
        "type": "string",
        "metadata": {
            "description": "Azure Storage account name."
        }
      },
      ```

      A tárfiók nevét és helyét a fő sablon adja át a csatolt sablon paraméterekként.

    * Távolítsa el a **változók** elemet és az összes változódefiníciót.
    * Távolítsa el az összes erőforrást, kivéve a tárfiókot. Összesen négy erőforrást fog eltávolítani.
    * Frissítse a tárfiók-erőforrás **névelemének** értékét a következőre:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * Módosítsa az **outputs** (kimenetek) elemet, hogy a következőképpen nézzen ki:

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```

       A **storageUri** a fő sablon virtuális gépének erőforrás-definíciójához szükséges.  Az értéket egy kimeneti értékként fogja átadni a fő sablonnak.

        Ha készen van, a sablonnak így kell kinéznie:

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
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
              "apiVersion": "2018-11-01",
              "name": "[parameters('storageAccountName')]",
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

1. Mentse a módosításokat.

## <a name="upload-the-linked-template"></a>Az összekapcsolt sablon feltöltése

A fő sablonnak és a csatolt sablonnak elérhetőnek kell lennie a központi telepítés futtatásának helyéről. Ebben az oktatóanyagban a felhőbeli rendszerhéj telepítési módszerét használja az [oktatóanyagban használt módon: Arm-sablonok létrehozása függő erőforrásokkal.](./template-tutorial-create-templates-with-dependent-resources.md) A fő sablon (azuredeploy.json) a felületre lesz feltöltve. A csatolt sablont (linkedTemplate.json) biztonságosan meg kell osztani. A következő PowerShell-parancsfájl létrehoz egy Azure Storage-fiókot, feltölti a sablont a Storage-fiókba, majd létrehoz egy SAS-jogkivonatot, amely korlátozott hozzáférést biztosít a sablonfájlhoz. Az oktatóanyag egyszerűsítése érdekében a parancsfájl letölt egy befejezett csatolt sablont egy GitHub-tárházból. Ha a létrehozott csatolt sablont szeretné használni, a [felhőbeli rendszerhéj](https://shell.azure.com) segítségével feltöltheti a csatolt sablont, majd módosíthatja a parancsfájlt a saját csatolt sablon jának használatához.

> [!NOTE]
> A parancsfájl korlátozza a SAS-jogkivonatot, amelyet nyolc órán belül használni kell. Ha több időre van szüksége az oktatóanyag befejezéséhez, növelje a lejárati időt.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

Write-Host "You need the following values later in the tutorial:"
Write-Host "Resource Group Name: $resourceGroupName"
Write-Host "Linked template URI with SAS token: $templateURI"
Write-Host "Press [ENTER] to continue ..."
```

1. Válassza a **Try It** zöld gombot az Azure felhőbeli rendszerhéj ablaktábla megnyitásához.
2. Válassza **a Másolás lehetőséget** a PowerShell-parancsfájl másolásához.
3. Kattintson a jobb gombbal a héjablakon (a sötétkék részen) belülre, majd válassza a **Beillesztés parancsot.**
4. Jegyezze fel a rendszerhéj ablaktábla végén található két értéket (Erőforráscsoport neve és Csatolt sablon URI). Az oktatóanyag későbbi részében szüksége lesz rájuk.
5. A rendszerhéj ablaktábla bezárásához válassza a Kilépés az **élességállítási módból** lehetőséget.

A gyakorlatban létrehoz egy SAS-jogkivonatot a fő sablon üzembe helyezésekor, és egy kisebb ablakot ad a SAS-jogkivonat lejárati helyének, hogy biztonságosabbá tegye. További információ: [SAS-jogkivonat biztosítása a telepítés során.](./secure-template-with-sas-token.md#provide-sas-token-during-deployment)

## <a name="call-the-linked-template"></a>Az összekapcsolt sablon meghívása

A fő sablon egy azuredeploy.json nevű fájl.

1. Nyissa **meg az azuredeploy.json alkalmazást** a Visual Studio-kódban, ha nincs megnyitva.
1. Cserélje le a tárfiók erőforrás-definícióját a következő jsonkódrészletre:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":""
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Ügyeljen ezekre a részletekre:

    * A fő sablon `Microsoft.Resources/deployments` erőforrása kapcsolja őt össze egy másik sablonnal.
    * A `deployments` erőforrásban található egy `linkedTemplate` név. Ezzel a névvel történik a [függőség konfigurálása](#configure-dependency).
    * Összekapcsolt sablonok meghívásakor csak [Incremental](./deployment-modes.md) (Növekményes) üzembe helyezési mód használható.
    * A `templateLink/uri` tartalmazza az összekapcsolt sablon URI-azonosítóját. Frissítse az értéket a csatolt sablon (a SAS-jogkivonattal rendelkező) feltöltésekor kapandó URI-ra.
    * A `parameters` használatával adhat át értékeket a fő sablonból az összekapcsolt sablonba.
1. Győződjön meg arról, `uri` hogy frissítette az elem értékét a csatolt sablon feltöltésekor kapott értékre (a SAS-jogkivonattal rendelkezőre). A gyakorlatban meg szeretné adni az URI-t egy paraméterrel.
1. A módosított sablon mentése

## <a name="configure-dependency"></a>Függőség konfigurációja

Visszahívás [oktatóanyagból: Arm-sablonok létrehozása függő erőforrásokkal,](./template-tutorial-create-templates-with-dependent-resources.md)a virtuálisgép-erőforrás a tárfióktól függ:

![Az Azure Resource Manager-sablonok függőségi diagramja](./media/template-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Mivel a tárfiók jelenleg az összekapcsolt sablonban van definiálva, módosítania kell a `Microsoft.Compute/virtualMachines` erőforrás következő két elemét.

* Konfigurálja újra a `dependsOn` elemet. A tárfiók definíciója az összekapcsolt sablonba lett áthelyezve.
* Konfigurálja újra a `properties/diagnosticsProfile/bootDiagnostics/storageUri` elemet. [Az összekapcsolt sablon létrehozása](#create-the-linked-template) című szakaszban hozzáadott egy kimeneti értéket:

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

    ![Az összekapcsolt Azure Resource Manager-sablonok függőségi konfigurációja](./media/template-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* a központi telepítéserőforrás neve.
3. Frissítse **a properties/diagnosticsProfile/bootDiagnostics/storageUri** ahogy az előző képernyőképen látható.
4. Mentse a módosított sablont.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) szakaszban tekintheti meg. Használja ugyanazt az erőforráscsoport nevet, mint a storage-fiók a csatolt sablon tárolására. Ez megkönnyíti az erőforrások karbantartását a következő szakaszban. A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Lásd: [Előfeltételek](#prerequisites).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="additional-practice"></a>További gyakorlat

A projekt fejlesztéséhez hajtsa végre a következő további módosításokat a befejezett projekten:

1. Módosítsa a fő sablont (azuredeploy.json), hogy a csatolt sablon URI-értékét egy paraméteren keresztül.
2. Ahelyett, hogy egy SAS-jogkivonatot hozlétre a csatolt sablon feltöltésekén, hozza létre a jogkivonatot a fő sablon telepítésekor. További információ: [SAS-jogkivonat biztosítása a telepítés során.](./secure-template-with-sas-token.md#provide-sas-token-during-deployment)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy sablont egy fő sablonlá és egy csatolt sablonba modularizált. A virtuálisgép-bővítmények üzembe helyezés utáni feladatok végrehajtásáról az:

> [!div class="nextstepaction"]
> [Virtuális gépi bővítmények üzembe helyezése](./template-tutorial-deploy-vm-extensions.md)

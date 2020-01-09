---
title: Hivatkozott sablonok létrehozása
description: Ismerje meg, hogyan hozhat létre összekapcsolt Azure Resource Manager-sablonokat virtuális gépek létrehozásához
author: mumian
ms.date: 12/03/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 903193b48f41b2ba25bc35ac245e5afb482e61d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472778"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Oktatóanyag: Összekapcsolt Azure Resource Manager-sablonok létrehozása

Ismerje meg, hogyan hozhat létre összekapcsolt Azure Resource Manager-sablonokat. Összekapcsolt sablonok használatával az egyik sablonja meghívhat egy másikat. Ez nagyszerűen használható a sablonok modularizálásához. Ebben az oktatóanyagban ugyanazt a sablont fogja használni, amelyet a következő [oktatóanyagban használ: Azure Resource Manager sablonok létrehozása függő erőforrásokkal](./template-tutorial-create-templates-with-dependent-resources.md), amely létrehoz egy virtuális gépet, egy virtuális hálózatot és más függő erőforrást, beleértve a Storage-fiókot. A Storage-fiók erőforrás-létrehozását egy csatolt sablonhoz kell elkülöníteni.

A csatolt sablon meghívása hasonló a függvények meghívásához.  Azt is megtudhatja, hogyan adhat hozzá paramétereket a csatolt sablonhoz, és hogyan kérheti le a "Return Values" értéket a csatolt sablonból.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * Az összekapcsolt sablon létrehozása
> * Az összekapcsolt sablon feltöltése
> * Hivatkozás az összekapcsolt sablonra
> * Függőség konfigurációja
> * A sablon üzembe helyezése
> * További eljárások

További információ: [csatolt és beágyazott sablonok használata Azure-erőforrások telepítésekor](./linked-templates.md).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio Code a Resource Manager-eszközök bővítménnyel. További információ: [Azure Resource Manager sablonok létrehozása a Visual Studio Code használatával](use-vs-code-to-create-template.md).
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd [Oktatóanyag: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába](./template-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure gyorsindítási sablonok a Resource Manager-sablonok tárházaként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg. Ez ugyanaz a sablon, amelyet a következő [oktatóanyagban használunk: Azure Resource Manager sablonok létrehozása függő erőforrásokkal](./template-tutorial-create-templates-with-dependent-resources.md). Mentse a sablont két példányban a következő felhasználásra:

* **A fő sablon**: ez hozza létre a tárfiók kivételével az összes erőforrást.
* **Az összekapcsolt sablon**: ez hozza létre a tárfiókot.

1. A Visual Studio Code-ban válassza a **File** (Fájl) >**Open File** (Fájl megnyitása) elemet.
1. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
1. A sablon hat erőforrást definiál:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/networkSecurityGroups`](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     A sablon testreszabása előtt hasznos lehet a sablon sémájának alapvető megismerése.
1. A **File** (Fájl) >**Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.
1. A **File** (Fájl) >**Save As** (Mentés másként) kiválasztásával hozza létre a fájl egy újabb másolatát **linkedTemplate.json** néven.

## <a name="create-the-linked-template"></a>Az összekapcsolt sablon létrehozása

Az összekapcsolt sablon létrehoz egy tárfiókot. A csatolt sablon önálló sablonként is használható a Storage-fiók létrehozásához. Ebben az oktatóanyagban a csatolt sablon két paramétert fogad, és visszaadja az értéket a fő sablonnak. Ez a "Return" érték van definiálva a `outputs` elemben.

1. Ha a fájl nincs megnyitva, nyissa meg a **linkedTemplate. JSON** fájlt a Visual Studio Code-ban.
1. Hajtsa végre a következő módosításokat:

    * Távolítsa el az összes paramétert, kivéve a **helyet**.
    * Vegyen fel egy **storageAccountName** (tárFiókNeve) nevű paramétert.

      ```json
      "storageAccountName":{
        "type": "string",
        "metadata": {
            "description": "Azure Storage account name."
        }
      },
      ```

      A Storage-fiók neve és helye a fősablonból a társított sablonba paraméterként lesz átadva.

    * Távolítsa el a **változók** elemet és az összes változó definícióját.
    * Távolítsa el a Storage-fióktól eltérő összes erőforrást. Összesen négy erőforrást fog eltávolítani.
    * Frissítse a Storage-fiók erőforrás **Name** elemének értékét a következőre:

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
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-11-01",
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

A fősablonnak és a csatolt sablonnak elérhetőnek kell lennie az üzemelő példány futtatásához. Ebben az oktatóanyagban a Cloud Shell telepítési módszert használja a következő [oktatóanyagban: Azure Resource Manager sablonok létrehozása függő erőforrásokkal](./template-tutorial-create-templates-with-dependent-resources.md). A fő sablon (azuredeploy.json) a felületre lesz feltöltve. A csatolt sablont (linkedTemplate. JSON) biztonságosan kell megosztani. A következő PowerShell-szkript létrehoz egy Azure Storage-fiókot, feltölti a sablont a Storage-fiókba, majd létrehoz egy SAS-jogkivonatot, amely korlátozott hozzáférést biztosít a sablon fájljához. Az oktatóanyag leegyszerűsítése érdekében a szkript egy GitHub-adattárból tölti le a befejezett csatolt sablont. Ha a létrehozott csatolt sablont szeretné használni, a [Cloud Shell](https://shell.azure.com) használatával feltöltheti a csatolt sablont, majd módosíthatja a parancsfájlt saját csatolt sablon használatára.

> [!NOTE]
> A parancsfájl 8 órán belül korlátozza az SAS-tokent. Ha több időre van szüksége az oktatóanyag elvégzéséhez, növelje a lejárati időt.

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

1. Kattintson a **kipróbálás** zöldre gombra az Azure Cloud Shell panel megnyitásához.
2. A PowerShell-szkript másolásához válassza a **Másolás** lehetőséget.
3. Kattintson a jobb gombbal a rendszerhéj ablaktáblán belül bárhová (a Navy kék rész), majd válassza a **Beillesztés**lehetőséget.
4. Jegyezze fel a két értéket (erőforráscsoport neve és csatolt sablon URI-ja) a rendszerhéj ablaktábla végén. Az oktatóanyag későbbi részében szüksége lesz rájuk.
5. A rendszerhéj ablaktábla bezárásához válassza a **Kilépés a fókusz módból** lehetőséget.

A gyakorlatban a fősablon telepítésekor létrehoz egy SAS-tokent, és lehetővé teszi, hogy az SAS-jogkivonat lejárata egy kisebb ablak legyen, hogy biztonságosabb legyen. További információ: SAS- [jogkivonat megadása az üzembe helyezés során](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Az összekapcsolt sablon meghívása

A fő sablon egy azuredeploy.json nevű fájl.

1. Ha nincs megnyitva, nyissa meg a **azuredeploy. JSON** fájlt a Visual Studio Code-ban.
1. Cserélje le a Storage-fiók erőforrás-definícióját a következő JSON-kódrészletre:

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
    * A `templateLink/uri` tartalmazza az összekapcsolt sablon URI-azonosítóját. Frissítse az értéket a kapott URI-ra, amikor feltölti a csatolt sablont (az egyiket SAS-jogkivonattal).
    * A `parameters` használatával adhat át értékeket a fő sablonból az összekapcsolt sablonba.
1. Győződjön meg arról, hogy frissítette a `uri` elem értékét a csatolt sablon feltöltésekor kapott értékre (az egyik SAS-tokenrel). A gyakorlatban az URI-t egy paraméterrel kell megadnia.
1. A módosított sablon mentése

## <a name="configure-dependency"></a>Függőség konfigurációja

Visszahívás az [oktatóanyagból: Azure Resource Manager sablonok létrehozása függő erőforrásokkal](./template-tutorial-create-templates-with-dependent-resources.md), a virtuális gép erőforrása a Storage-fióktól függ:

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

    A *linkedTemplate* az üzembehelyezési erőforrás neve.
3. Frissítse a **Properties/diagnosticsProfile/bootDiagnostics/storageUri tulajdonságot** az előző képernyőképen látható módon.
4. Mentse a módosított sablont.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) szakaszban tekintheti meg. Ugyanazt az erőforráscsoport-nevet használja, mint a társított sablon tárolására szolgáló Storage-fiók. Megkönnyíti az erőforrások tisztítását a következő szakaszban. A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Lásd: [Előfeltételek](#prerequisites).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="additional-practice"></a>További gyakorlat

A projekt javításához végezze el a következő módosításokat a befejezett projekten:

1. Módosítsa a fősablont (azuredeploy. JSON) úgy, hogy az a társított sablon URI értékét egy paraméterrel hozza meg.
2. Ahelyett, hogy SAS-tokent generál a csatolt sablon feltöltésekor, hozza létre a tokent a fő sablon telepítésekor. További információ: SAS- [jogkivonat megadása az üzembe helyezés során](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy sablont egy fő sablonba és egy csatolt sablonba helyeztek. Ha szeretné megtudni, hogyan használhatja a virtuálisgép-bővítményeket az üzembe helyezés utáni feladatok végrehajtásához, olvassa el a következő témakört:

> [!div class="nextstepaction"]
> [Virtuális gépi bővítmények üzembe helyezése](./template-tutorial-deploy-vm-extensions.md)

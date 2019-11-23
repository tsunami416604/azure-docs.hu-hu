---
title: Hivatkozott sablonok létrehozása
description: Ismerje meg, hogyan hozhat létre összekapcsolt Azure Resource Manager-sablonokat virtuális gépek létrehozásához
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9764edb986b2ee847e3fcecda228f53551b462c3
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325428"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Oktatóanyag: Összekapcsolt Azure Resource Manager-sablonok létrehozása

Ismerje meg, hogyan hozhat létre összekapcsolt Azure Resource Manager-sablonokat. Összekapcsolt sablonok használatával az egyik sablonja meghívhat egy másikat. Ez nagyszerűen használható a sablonok modularizálásához. In this tutorial, you use the same template used in [Tutorial: Create Azure Resource Manager templates with dependent resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md), which creates a virtual machine, a virtual network, and other dependent resource including a storage account. You separate the storage account resource creation to a linked template.

Calling a linked template is like making a function call.  You also learn how to pass parameter values to the linked template, and how to get "return values" from the linked template.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * Az összekapcsolt sablon létrehozása
> * Az összekapcsolt sablon feltöltése
> * Hivatkozás az összekapcsolt sablonra
> * Függőség konfigurációja
> * A sablon üzembe helyezése
> * Additional practices

For more information, see [Use linked and nested templates when deploying Azure resources](./resource-group-linked-templates.md).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd [Oktatóanyag: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába](./resource-manager-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure-beli gyorsindítási sablonok a Resource Manager-sablonok adattáraként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg. This is the same template used in [Tutorial: Create Azure Resource Manager templates with dependent resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Mentse a sablont két példányban a következő felhasználásra:

* **A fő sablon**: ez hozza létre a tárfiók kivételével az összes erőforrást.
* **Az összekapcsolt sablon**: ez hozza létre a tárfiókot.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A sablon öt erőforrást határoz meg:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     It is helpful to get some basic understanding of the template schema before customizing the template.
5. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.
6. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával hozza létre a fájl egy újabb másolatát **linkedTemplate.json** néven.

## <a name="create-the-linked-template"></a>Az összekapcsolt sablon létrehozása

Az összekapcsolt sablon létrehoz egy tárfiókot. The linked template can be used as a standalone template to create a storage account. In this tutorial, the linked template takes two parameters, and passes a value back to the main template. This "return" value is defined in the `outputs` element.

1. Open **linkedTemplate.json** in Visual Studio Code if the file is not opened.
2. Hajtsa végre a következő módosításokat:

    * Remove all the parameters other than **location**.
    * Vegyen fel egy **storageAccountName** (tárFiókNeve) nevű paramétert.
        ```json
        "storageAccountName":{
          "type": "string",
          "metadata": {
              "description": "Azure Storage account name."
          }
        },
        ```
        The storage account name and location are passed from the main template to the linked template as parameters.

    * Remove the **variables** element, and all the variable definitions.
    * Remove all the resources other than the storage account. Összesen négy erőforrást fog eltávolítani.
    * Update the value of the **name** element of the storage account resource to:

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
              "apiVersion": "2018-07-01",
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

The main template and the linked template need to be accessible from where you run the deployment. In this tutorial, you use the Cloud shell deployment method as you used in [Tutorial: Create Azure Resource Manager templates with dependent resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md). A fő sablon (azuredeploy.json) a felületre lesz feltöltve. The linked template (linkedTemplate.json) must be shared somewhere securely. The following PowerShell script creates an Azure Storage account, uploads the template to the Storage account, and then generates a SAS token to grant limited access to the template file. To simplify the tutorial, the script downloads a completed linked template from a Github repository. If you want to use the linked template you created, you can use the [Cloud shell](https://shell.azure.com) to upload your linked template, and then modify the script to use your own linked template.

> [!NOTE]
> The script limits the SAS token to be used within eight hours. If you need more time to complete this tutorial, increase the expiry time.

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

echo "You need the following values later in the tutorial:"
echo "Resource Group Name: $resourceGroupName"
echo "Linked template URI with SAS token: $templateURI"
```

1. Select the **Try It** green button to open the Azure cloud shell pane.
2. Select **Copy** to copy the PowerShell script.
3. Right-click anywhere inside the shell pane (the navy blue part), and then select **Paste**.
4. Make a note of the two values (Resource Group Name and Linked template URI) at the end of the shell pane. Az oktatóanyag későbbi részében szüksége lesz rájuk.
5. Select **Exit focus mode** to close the shell pane.

In practice, you generate a SAS token when you deploy the main template, and give the SAS token expiry a smaller window to make it more secure. For more information, see [Provide SAS token during deployment](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Az összekapcsolt sablon meghívása

A fő sablon egy azuredeploy.json nevű fájl.

1. Open **azuredeploy.json** in Visual Studio Code if it is not opened.
2. Delete the storage account resource definition from the template:

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    ```
3. A tárfiók definíciójának helyén adja hozzá a következő json kódrészletet:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json"
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
    * A `templateLink/uri` tartalmazza az összekapcsolt sablon URI-azonosítóját. Update the value to the URI you get when you upload the linked template (the one with a SAS token).
    * A `parameters` használatával adhat át értékeket a fő sablonból az összekapcsolt sablonba.
4. Make sure you have updated the value of the `uri` element to the value you got when you upload the linked template (the one with a SAS token). In practice, you want to supply the URI with a parameter.
5. Save the revised template

## <a name="configure-dependency"></a>Függőség konfigurációja

Recall from [Tutorial: Create Azure Resource Manager templates with dependent resources](./resource-manager-tutorial-create-templates-with-dependent-resources.md), the virtual machine resource depends on the storage account:

![Az Azure Resource Manager-sablonok függőségi diagramja](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

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

    ![Az összekapcsolt Azure Resource Manager-sablonok függőségi konfigurációja](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    A *linkedTemplate* az üzembehelyezési erőforrás neve.
3. Update **properties/diagnosticsProfile/bootDiagnostics/storageUri** as shown in the previous screenshot.
4. Save the revised template.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) szakaszban tekintheti meg. Use the same resource group name as the storage account for storing the linked template. It makes it easier to clean up resources in the next section. A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Lásd: [Előfeltételek](#prerequisites).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="additional-practice"></a>Additional practice

To improve the project, make the following additional changes to the completed project:

1. Modify the main template (azuredeploy.json) so that it takes the linked template URI value via a parameter.
2. Instead of generating a SAS token when you upload the linked template, generate the token when you deploy the main template. For more information, see [Provide SAS token during deployment](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Következő lépések

In this tutorial, you modularized a template into a main template and a linked template. To learn how to use virtual machine extensions to perform post deployment tasks, see:

> [!div class="nextstepaction"]
> [Virtuális gépi bővítmények üzembe helyezése](./resource-manager-tutorial-deploy-vm-extensions.md)

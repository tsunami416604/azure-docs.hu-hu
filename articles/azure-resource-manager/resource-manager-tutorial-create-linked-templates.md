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
ms.date: 03/18/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c5399f46106d94d593a15530ee0c223a3f5f3eaf
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802048"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Oktatóanyag: Csatolt Azure Resource Manager-sablonok létrehozása

Ismerje meg, hogyan hozhat létre összekapcsolt Azure Resource Manager-sablonokat. Összekapcsolt sablonok használatával az egyik sablonja meghívhat egy másikat. Ez nagyszerűen használható a sablonok modularizálásához. Ebben az oktatóanyagban ugyanazt a sablont [fogja használni, mint az oktatóanyagban: Hozzon létre Azure Resource Manager sablonokat függő](./resource-manager-tutorial-create-templates-with-dependent-resources.md)erőforrásokkal, amelyek egy virtuális gépet, egy virtuális hálózatot és egy más, a Storage-fiókot is tartalmazó, függő erőforrást hoznak létre. Szétválasztja a tárfiók egy hivatkozott sablonnak az erőforrás létrehozása.

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

További információ: [csatolt és beágyazott sablonok használata Azure-erőforrások telepítésekor](./resource-group-linked-templates.md).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) [Resource Manager Tools bővítménnyel](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információ [: oktatóanyag: Azure Key Vault integrálása a Resource Manager](./resource-manager-tutorial-use-key-vault.md)template deploymentban. Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure-beli gyorsindítási sablonok a Resource Manager-sablonok adattáraként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. A jelen oktatóanyagban használt sablon [egyszerű, windowsos virtuális gép üzembe helyezése](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) néven található meg. Ez az [oktatóanyagban használt sablon: Hozzon létre Azure Resource Manager sablonokat függő](./resource-manager-tutorial-create-templates-with-dependent-resources.md)erőforrásokkal. Mentse a sablont két példányban a következő felhasználásra:

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

     A sablon testreszabása előtt hasznos lehet a sablon sémájának alapvető megismerése.
5. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.
6. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával hozza létre a fájl egy újabb másolatát **linkedTemplate.json** néven.

## <a name="create-the-linked-template"></a>Az összekapcsolt sablon létrehozása

Az összekapcsolt sablon létrehoz egy tárfiókot. A csatolt sablon önálló sablonként is használható a Storage-fiók létrehozásához. Ebben az oktatóanyagban a csatolt sablon két paramétert fogad, és visszaadja az értéket a fő sablonnak. Ez a "Return" érték van definiálva `outputs` a elemben.

1. Ha a fájl nincs megnyitva, nyissa meg a **linkedTemplate. JSON** fájlt a Visual Studio Code-ban.
2. Hajtsa végre a következő módosításokat:

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

    * Távolítsa el a **változók** elemet, és a változó definíciókat.
    * Távolítsa el a Storage-fióktól eltérő összes erőforrást. Összesen négy erőforrást fog eltávolítani.
    * Frissítse az értéket, a **neve** elem, hogy a tárfiók típusú erőforrást:

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

A fő sablont és a hivatkozott sablonnak kell érhető el a telepítés futtatásához. Ebben az oktatóanyagban a Cloud Shell üzembe helyezési módszerét [fogja használni az oktatóanyagban: Hozzon létre Azure Resource Manager sablonokat függő](./resource-manager-tutorial-create-templates-with-dependent-resources.md)erőforrásokkal. A fő sablon (azuredeploy.json) a felületre lesz feltöltve. A hivatkozott sablonnak (linkedTemplate.json) kell lennie biztonságosan megosztott valahol. A következő PowerShell-szkript létrehoz egy Azure Storage-fiókot, feltölti a sablont a Storage-fiókba, majd létrehoz egy SAS-jogkivonatot, amely korlátozott hozzáférést biztosít a sablon fájljához. Az oktatóanyag leegyszerűsítése érdekében a parancsfájl egy befejezett csatolt sablont tölt le egy megosztott helyről. Ha szeretné a csatolt sablonnal létrehozott, használja a [Cloud shell](https://shell.azure.com) töltse fel a társított sablont, és módosítsa a parancsfájl használata a saját hivatkozott sablonnak.

> [!NOTE]
> A parancsfájl korlátozza a SAS-jogkivonat nyolc órán belül használható. Ha ez az oktatóanyag további időre van szüksége, növelje a lejárati időpont.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
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

1. Válassza ki a **Kipróbálom** zöld gombot az Azure cloud shell panel megnyitásához.
2. Válassza ki **másolási** másolása a PowerShell-parancsfájlt.
3. Kattintson a jobb gombbal a rendszerhéj panelen (a kék tengerészkék része) belül bárhol, és válassza **beillesztési**.
4. Jegyezze fel a két érték (erőforráscsoport neve, és a társított sablon URI-t) a rendszerhéj panel alján. Az oktatóanyag későbbi részében szüksége lesz rájuk.
5. Válassza ki **Kilépés a fókusz módból** a rendszerhéj panel bezárásához.

A gyakorlatban, generáljon egy SAS-token, a fő sablon üzembe helyezéséhez, és a SAS-jogkivonat lejárati adjon meg egy kisebb ablakban teheti biztonságosabbá. További információkért lásd: [üzembe helyezés során adja meg a SAS jogkivonat](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Az összekapcsolt sablon meghívása

A fő sablon egy azuredeploy.json nevű fájl.

1. Ha nincs megnyitva, nyissa meg a **azuredeploy. JSON** fájlt a Visual Studio Code-ban.
2. A tárolási fiók erőforrás-definíció törlése a sablonból:

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

    * A fő sablon `Microsoft.Resources/deployments` erőforrása kapcsolja őt össze egy másik sablonnal.
    * A `deployments` erőforrásban található egy `linkedTemplate` név. Ezzel a névvel történik a [függőség konfigurálása](#configure-dependency).
    * Összekapcsolt sablonok meghívásakor csak [Incremental](./deployment-modes.md) (Növekményes) üzembe helyezési mód használható.
    * A `templateLink/uri` tartalmazza az összekapcsolt sablon URI-azonosítóját. Frissítse az értéket, ha a hivatkozott sablonnak (a SAS-jogkivonat használatával egyet) feltölti az URI-t.
    * A `parameters` használatával adhat át értékeket a fő sablonból az összekapcsolt sablonba.
4. Ellenőrizze, hogy frissítette az értékét a `uri` az értékre akkor kapott, amikor feltölti a társított sablont (egy SAS-jogkivonat használatával) elemet. A gyakorlatban, kíván megadni, az URI-t paraméterrel.
5. A módosított sablon mentéséhez

## <a name="configure-dependency"></a>Függőség konfigurációja

Az oktatóanyagból való [visszahívás: A függő erőforrásokkal](./resource-manager-tutorial-create-templates-with-dependent-resources.md)rendelkező Azure Resource Manager-sablonok létrehozása a virtuális gép erőforrása a Storage-fióktól függ:

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
3. frissítés **storageUri tulajdonságok/diagnosticsProfile/bootDiagnostics** az előző képernyőképen látható módon.
4. A módosított sablon mentéséhez.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) szakaszban tekintheti meg. A tárfiók a társított sablon tárolására szolgáló ugyanazt az erőforráscsoport-nevet használjuk. Ez megkönnyíti a következő szakaszban az erőforrások törlése. A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót a virtuális gép rendszergazdai fiókjához. Lásd: [Előfeltételek](#prerequisites).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="additional-practice"></a>Kiegészítő eljárás

A projekt javítása érdekében hajtsa végre a következő további módosításokat a befejezett projekt:

1. Módosítsa a fő sablont (azuredeploy.json), hogy a hivatkozott sablonnak URI értékét egy paraméteren keresztül tart.
2. SAS-token generálása, amikor feltölti a hivatkozott sablonnak, helyett hozza létre a jogkivonatot, amikor telepíti a fő sablont. További információkért lásd: [üzembe helyezés során adja meg a SAS jogkivonat](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban, modularized egy sablont egy fő sablont és a egy hivatkozott sablonnak. További virtuálisgép-bővítmények használata utáni telepítési feladatok végrehajtásához, tekintse meg:

> [!div class="nextstepaction"]
> [Virtuális gépi bővítmények üzembe helyezése](./resource-manager-tutorial-deploy-vm-extensions.md)

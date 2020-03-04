---
title: Biztonságos összetevők a sablonokban
description: Ismerje meg, hogyan védheti meg az Azure Resource Manager-sablonokkal a használt összetevőket.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7069ff363cf274ba855efc9b598d8d01e64e18d1
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250114"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Oktatóanyag: Biztonságos összetevők az Azure Resource Manager-sablon-üzembehelyezések

Megtudhatja, hogyan védheti meg a Azure Resource Manager-sablonokban használt összetevőket egy közös hozzáférési aláírásokkal (SAS) rendelkező Azure Storage-fiókkal. Üzembe helyezési összetevők olyan fájlok, a fő sablonfájl mellett a telepítés befejezéséhez szükséges. Például az [oktatóanyagban: SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal](./template-tutorial-deploy-sql-extensions-bacpac.md)a fő sablon egy Azure SQL Database példányt hoz létre. Egy BACPAC-fájlt is meghív a táblák létrehozásához és az adatbeszúráshoz. A BACPAC-fájl egy összetevő, és egy Azure Storage-fiókban tárolódik. A rendszer egy Storage-fiók kulcsát használta a lelet eléréséhez. 

Ebben az oktatóanyagban SAS használhatja a BACPAC-fájlba korlátozott hozzáférés adható a saját Azure Storage-fiókban. További információ az SAS-ról: [közös hozzáférésű aláírások (SAS) használata](../../storage/common/storage-dotnet-shared-access-signature-part-1.md).

A csatolt sablonok biztonságossá tételéhez tekintse meg az [oktatóanyag: csatolt Azure Resource Manager-sablonok létrehozása](./template-tutorial-create-linked-templates.md)című témakört.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Készítse elő a BACPAC-fájlt.
> * Nyisson meg egy meglévő sablont.
> * Szerkessze a sablont.
> * A sablon üzembe helyezése.
> * A telepítés ellenőrzése.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio Code egy Resource Manager-eszközök bővítménnyel. További információ: [Azure Resource Manager sablonok létrehozása a Visual Studio Code használatával](./use-vs-code-to-create-template.md).
* [Oktatóanyag áttekintése: SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal](./template-tutorial-deploy-sql-extensions-bacpac.md). Ebben az oktatóanyagban használt sablon fejlesztett ki, hogy az oktatóanyag lesz. Ebben a cikkben egy letöltési hivatkozást a befejezett sablon áll rendelkezésre.
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót az SQL Server rendszergazdai fiókjához. Az alábbi példa segítségével hozhatja meg a jelszót:

    ```console
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd [Oktatóanyag: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába](./template-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="prepare-a-bacpac-file"></a>A BACPAC-fájl előkészítése

Ebben a szakaszban előkészíti a BACPAC-fájlt, hogy a fájl biztonságosan elérhető legyen a Resource Manager-sablon telepítésekor. Ez a szakasz öt eljárás szerepelnek:

* Töltse le a BACPAC-fájlba.
* Azure Storage-fiók létrehozása.
* Hozzon létre egy Storage-fiók blob-tárolóját.
* Töltse fel a BACPAC-fájlt a tárolóba.
* A SAS-jogkivonat a BACPAC-fájl lekérése.

1. Válassza a **kipróbálás** lehetőséget a Cloud Shell megnyitásához. Ezután illessze be a következő PowerShell-szkriptet a rendszerhéj ablakába.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name"   # This name is used to generate names for Azure resources, such as storage account name.
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"

    $resourceGroupName = $projectName + "rg"
    $storageAccountName = $projectName + "store"
    $containerName = "bacpacfile" # The name of the Blob container to be created.

    $bacpacURL = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"
    $bacpacFileName = "SQLDatabaseExtension.bacpac" # A file name used for downloading and uploading the BACPAC file.

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacURL -OutFile "$home/$bacpacFileName"

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

    # Upload the bacpac file
    Set-AzStorageBlobContent `
        -Container $containerName `
        -File "$home/$bacpacFileName" `
        -Blob $bacpacFileName `
        -Context $context

    # Generate a SAS token
    $bacpacURI = New-AzStorageBlobSASToken `
        -Context $context `
        -Container $containerName `
        -Blob $bacpacFileName `
        -Permission r `
        -ExpiryTime (Get-Date).AddHours(8.0) `
        -FullUri

    $str = $bacpacURI.split("?")

    Write-Host "You need the blob url and the SAS token later in the tutorial:"
    Write-Host $str[0]
    Write-Host (-join ("?", $str[1]))

    Write-Host "Press [ENTER] to continue ..."
    ```

1. Jegyezze fel a BACPAC fájl URL-címét és az SAS-tokent. A sablon telepítésekor ezeket az értékeket kell megadnia.

## <a name="open-an-existing-template"></a>Nyisson meg egy meglévő sablon

Ebben a munkamenetben módosítja az [oktatóanyagban létrehozott sablont: az SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal](./template-tutorial-deploy-sql-extensions-bacpac.md) a BACPAC-fájl sas-tokenrel való meghívásához. Az SQL-bővítmény oktatóanyagában kifejlesztett sablon meg van osztva a [githubban](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. A Visual Studio Code-ban válassza a **File** (Fájl)  > **Open File** (Fájl megnyitása) elemet.
1. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy2.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.

    A sablonban négy erőforrás van definiálva:

   * `Microsoft.Sql/servers`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/firewallRules`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

        A Testreszabás előtt hasznos lehet a sablon alapvető megismerése.
1. A **File** (Fájl)  > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, *azuredeploy.json* néven.

## <a name="edit-the-template"></a>A sablon szerkesztése

1. Cserélje le a storageAccountKey paraméter definícióját a következő paraméter-definícióra: 

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Erőforrás-kezelő oktatóanyag biztonságos összetevők paramétereit](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. Frissítse az SQL-bővítmény erőforrásának következő három elemének értékét:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

A befejezett sablon ehhez hasonlóan néz ki:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tekintse meg a telepítési folyamat [sablonjának központi telepítése](./template-tutorial-create-multiple-instances.md#deploy-the-template) című szakaszát. Használja helyette a következő PowerShell üzembe helyezési parancsfájlt.

```azurepowershell
$projectName = Read-Host -Prompt "Enter the project name that is used earlier"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the sql database admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$bacpacUrl = Read-Host -Prompt "Enter the BACPAC url"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString

$resourceGroupName = $projectName + "rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacUrl $bacpacUrl `
    -TemplateFile "$HOME/azuredeploy.json"

Write-Host "Press [ENTER] to continue ..."
```

Használni létrehozott jelszót. Lásd: [Előfeltételek](#prerequisites).
_ArtifactsLocation, _artifactsLocationSasToken és bacpacFileName értékeit lásd: [BACPAC-fájl előkészítése](#prepare-a-bacpac-file).

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

A portálon válassza ki az SQL Database-adatbázist az újonnan üzembe helyezett erőforráscsoportból. Válassza a **Lekérdezésszerkesztő (előzetes verzió)** elemet, majd adja meg a rendszergazdai hitelesítő adatokat. Ekkor két, az adatbázisba importált tábla jelenik meg.

![Lekérdezés-szerkesztő (előzetes verzió)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét. Az erőforráscsoport összesen hat erőforrást fog látni.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy SQL-kiszolgálót és egy SQL-adatbázist helyezett üzembe, és egy SAS-token használatával importált egy BACPAC-fájlt. A Resource Manager-sablonok folyamatos fejlesztéséhez és üzembe helyezéséhez szükséges Azure-folyamat létrehozásáról további információt a következő témakörben talál:

> [!div class="nextstepaction"]
> [Folyamatos integráció az Azure-folyamattal](./template-tutorial-use-azure-pipelines.md)

---
title: Biztonságos összetevők a sablonokban
description: Ismerje meg, hogyan biztosíthatja az Azure Resource Manager-sablonokban használt összetevőket.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: ad6ea3c68ed6f48ac48bbbdafed7f8660df23937
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239232"
---
# <a name="tutorial-secure-artifacts-in-arm-template-deployments"></a>Oktatóanyag: Biztonságos összetevők az ARM-sablontelepítésekben

Megtudhatja, hogyan biztosíthatja az Azure Resource Manager (ARM) sablonjaiban használt összetevőket egy Megosztott hozzáférésű aláírásokkal (SAS) rendelkező Azure Storage-fiók használatával. A központi telepítési összetevők a központi sablonfájlon kívül olyan fájlok, amelyek a központi telepítés végrehajtásához szükségesek. Például az [oktatóanyag: SQL BACPAC-fájlok importálása ARM-sablonokkal,](./template-tutorial-deploy-sql-extensions-bacpac.md)a fő sablon létrehoz egy Azure SQL Database-példányt. Azt is kéri a BACPAC fájlt létrehozni táblázatok és beszúrni az adatokat. A BACPAC-fájl egy összetevő, és egy Azure Storage-fiókban tárolja. A tárfiók kulcs a műtermék eléréséhez használt.

Ebben az oktatóanyagban a SAS használatával korlátozott hozzáférést biztosít a BACPAC-fájlhoz a saját Azure Storage-fiókjában. A SAS-ről a [Megosztott hozzáférésű aláírások (SAS) használata című](../../storage/common/storage-dotnet-shared-access-signature-part-1.md)témakörben talál további információt.

A csatolt sablonok védelméről az [Oktatóanyag: Csatolt ARM-sablonok létrehozása című témakörben olvashat.](./template-tutorial-create-linked-templates.md)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * BACPAC-fájl előkészítése.
> * Nyisson meg egy meglévő sablont.
> * A sablon szerkesztése.
> * Telepítse a sablont.
> * Ellenőrizze a központi telepítést.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio-kód Erőforráskezelő eszközök kiterjesztéssel. Lásd: [A Visual Studio-kód használata ARM-sablonok létrehozásához.](./use-vs-code-to-create-template.md)
* Bemutató [anyag áttekintése: SQL BACPAC fájlok importálása ARM sablonokkal.](./template-tutorial-deploy-sql-extensions-bacpac.md) A sablon használt bemutató az egyik kifejlesztett, hogy a bemutató. A kitöltött sablon letöltési linkje ebben a cikkben található.
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót az SQL Server rendszergazdai fiókjához. Íme egy példa, amelynek segítségével jelszót hozhat létre:

    ```console
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információ: [Az Azure Key Vault integrálása az ARM-sablon üzembe helyezésében](./template-tutorial-use-key-vault.md)című témakörben olvashat. Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="prepare-a-bacpac-file"></a>A BACPAC-fájl előkészítése

Ebben a szakaszban előkészíti a BACPAC-fájlt, hogy a fájl biztonságosan elérhető legyen az ARM sablon telepítésekor. Ebben a szakaszban öt eljárás található:

* Töltse le a BACPAC fájlt.
* Azure Storage-fiók létrehozása.
* Hozzon létre egy tárfiók blob tároló.
* Töltse fel a BACPAC-fájlt a tárolóba.
* A BACPAC-fájl SAS-tokenjének beolvasása.

1. Válassza **a Próbálja ki** a felhőhéj megnyitásához válassza a Próbálja ki. Ezután illessze be a következő PowerShell-parancsfájlt a rendszerhéj ablakába.

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

1. Írja le a BACPAC-fájl URL-címét és a SAS-tokent. A sablon telepítésekor ezekre az értékekre van szükség.

## <a name="open-an-existing-template"></a>Meglévő sablon megnyitása

Ebben a munkamenetben módosítja az [oktatóanyagban létrehozott sablont: SQL BACPAC-fájlok importálása ARM sablonokkal](./template-tutorial-deploy-sql-extensions-bacpac.md) a BACPAC-fájl SAS-jogkivonattal történő hívásához. Az SQL bővítmény oktatóanyagában kifejlesztett sablon meg van osztva a [GitHubon.](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json)

1. A Visual Studio-kódból válassza a **Fájlmegnyitása** > **fájl**lehetőséget.
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

        A sablon testreszabása előtt hasznos lehet a sablon alapvető ismerete.
1. Válassza a **Fájlmentés** > **másként** lehetőséget, ha a fájl egy példányát az *azuredeploy.json*nevű számítógépre szeretné menteni.

## <a name="edit-the-template"></a>A sablon szerkesztése

1. Cserélje le a storageAccountKey paraméterdefiníciót a következő paraméterdefinícióra:

    ```json
        "_artifactsLocationSasToken": {
          "type": "securestring",
          "metadata": {
            "description": "Specifies the SAS token required to access the artifact location."
          }
        },
    ```

    ![Az Erőforrás-kezelő oktatóanyaga biztonságos összetevők paraméterei](./media/template-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

1. Frissítse az SQL-bővítmény erőforrás következő három elemének értékét:

    ```json
    "storageKeyType": "SharedAccessKey",
    "storageKey": "[parameters('_artifactsLocationSasToken')]",
    "storageUri": "[parameters('bacpacUrl')]",
    ```

A befejezett sablon ehhez hasonlóan néz ki:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy3.json?range=1-106&highlight=38-43,95-97)]

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tekintse meg a telepítési eljárás [a sablon telepítése](./template-tutorial-create-multiple-instances.md#deploy-the-template) szakaszban. Használja inkább a következő PowerShell-telepítési parancsfájlt.

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

Generált jelszó használata. Lásd: [Előfeltételek](#prerequisites).
A _artifactsLocation, _artifactsLocationSasToken és bacpacFileName értékekről a [BACPAC-fájl előkészítése](#prepare-a-bacpac-file)című témakörben látható.

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

A portálon válassza ki az SQL Database-adatbázist az újonnan üzembe helyezett erőforráscsoportból. Válassza a **Lekérdezésszerkesztő (előzetes verzió)** elemet, majd adja meg a rendszergazdai hitelesítő adatokat. Az adatbázisba importálva két táblát fog látni.

![Lekérdezésszerkesztő (előzetes verzió)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét. Az erőforráscsoportban összesen hat erőforrás jelenik meg.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy SQL-kiszolgálót és egy SQL-adatbázist telepített, és Egy BACPAC-fájlt importált Egy SAS-jogkivonat használatával. Annak megismeréséhez, hogyan lehet Azure-erőforrásokat több régióban üzembe helyezni, és hogyan alkalmazhat biztonságos üzembehelyezési gyakorlatokat, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Biztonságos üzembehelyezési gyakorlatok](./deployment-manager-tutorial.md)

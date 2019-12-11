---
title: SQL BACPAC-fájlok importálása sablonokkal
description: Megtudhatja, hogyan importálhat SQL Database-bővítményt az SQL BACPAC-fájlok Azure Resource Manager-sablonokkal történő importálásához.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9f5e2e402e13076dc538a9c9d55e5b67e0d86d4f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978899"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Oktatóanyag: SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal

Megtudhatja, hogyan importálhat Azure SQL Database-bővítményeket egy BACPAC-fájl importálásához Azure Resource Manager-sablonokkal. Az üzembe helyezési összetevők a központi telepítés befejezéséhez szükséges fő sablonfájlokat is tartalmazó fájlok. A BACPAC-fájl egy összetevő. Ebben az oktatóanyagban létrehoz egy sablont egy Azure-SQL Server üzembe helyezéséhez, egy SQL Database és egy BACPAC-fájl importálásához. További információ az Azure-beli virtuális gépi bővítmények üzembe helyezéséről Azure Resource Manager-sablonok használatával: [# Oktatóanyag: Virtuális gépi bővítmények üzembe helyezése Azure Resource Manager-sablonokkal](./resource-manager-tutorial-deploy-vm-extensions.md).

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A BACPAC-fájl előkészítése
> * Gyorsindítási sablon megnyitása
> * A sablon szerkesztése
> * A sablon üzembe helyezése
> * Az üzemelő példány ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio Code a Resource Manager-eszközök bővítménnyel. További információ: [Azure Resource Manager sablonok létrehozása a Visual Studio Code használatával](./resource-manager-tools-vs-code.md).
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót az SQL Server rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd [Oktatóanyag: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába](./resource-manager-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="prepare-a-bacpac-file"></a>A BACPAC-fájl előkészítése

Egy BACPAC-fájl meg van osztva a [githubon](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). További információ saját fájl létrehozásához: [Azure SQL-adatbázis exportálása BACPAC-fájlba](../sql-database/sql-database-export.md). Ha egy saját helyen kívánja közzétenni a fájlt, frissítenie kell a sablont az oktatóanyag egy későbbi részében.

A BACPAC-fájlt egy Azure Storage-fiókban kell tárolni, mielőtt a Resource Manager-sablon használatával importálható. A következő PowerShell-szkript előkészíti a BACPAC-fájlt az alábbi lépésekkel:

* Töltse le a BACPAC fájlt.
* Azure Storage-fiók létrehozása.
* Hozzon létre egy Storage-fiók blob-tárolóját.
* Töltse fel a BACPAC-fájlt a tárolóba.
* Jelenítse meg a Storage-fiók kulcsát és a blob URL-címét.

1. Válassza a **kipróbálás** lehetőséget a Cloud Shell megnyitásához, majd illessze be a következő PowerShell-szkriptet a rendszerhéj ablakába.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Jegyezze fel a Storage-fiók kulcsát és a BACPAC fájl URL-címét. A sablon telepítésekor ezeket az értékeket kell megadnia.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az oktatóanyagban használt sablont a [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json)tárolja.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.

    A sablonban két erőforrás van definiálva:

   * `Microsoft.Sql/servers` kérdésre adott válaszban foglalt lépéseket. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases` kérdésre adott válaszban foglalt lépéseket.  Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Érdemes megismerkedni a sablon alapvető működésével, mielőtt megkezdi annak testreszabását.
4. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.

## <a name="edit-the-template"></a>A sablon szerkesztése

1. Adja hozzá a **Parameters (paraméterek** ) szakasz végén két további paramétert a Storage-fiók kulcsának és a BACPAC URL-címének megadásához:

    ```json
    "storageAccountKey": {
      "type":"string",
      "metadata":{
        "description": "Specifies the key of the storage account where the BACPAC file is stored."
      }
    },
    "bacpacUrl": {
      "type":"string",
      "metadata":{
        "description": "Specifies the URL of the BACPAC file."
      }
    }
    ```

    Adjon hozzá egy vesszőt a **adminPassword**után. A VS Code-ból származó JSON-fájl formázásához nyomja meg a **[SHIFT] + [Alt] + F**billentyűkombinációt.

    Lásd: [BACPAC-fájl előkészítése](#prepare-a-bacpac-file) a két érték beszerzéséről.

1. Adjon hozzá két további erőforrást a sablonhoz.

    * Ahhoz, hogy az SQL Database bővítmény BACPAC-fájlokat importáljon, engedélyeznie kell az Azure-szolgáltatásokból érkező forgalmat. Adja hozzá a következő tűzfalszabály-definíciót az SQL Server-definíció alatt:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        A sablon a következőképpen fog kinézni:

        ![Azure Resource Manager, sql-bővítmények üzembe helyezése, BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Adjon hozzá egy SQL Database-bővítmény erőforrást az adatbázis definíciójához az alábbi JSON-nal:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        A sablon a következőképpen fog kinézni:

        ![Azure Resource Manager, sql-bővítmények üzembe helyezése, BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        További információ az erőforrás-definícióról: [SQL Database-bővítmény referenciája](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). A következők a fontosabb elemek:

        * **dependsOn**: A bővítményerőforrást az SQL Database létrehozása után kell létrehozni.
        * **storageKeyType**: adja meg a használandó tárolási kulcs típusát. Az értéke `StorageAccessKey` vagy `SharedAccessKey` lehet. `StorageAccessKey` használata ebben az oktatóanyagban.
        * **storageKey**: a BACPAC-fájlt tároló tárolási fiók kulcsát határozza meg. Ha a tárolási kulcs típusa SharedAccessKey, azt előtte kell lennie: "?"
        * **storageUri**: a Storage-fiókban tárolt BACPAC-fájl URL-címét határozza meg.
        * **administratorLoginPassword**: Az SQL-rendszergazda jelszava. Használjon generált jelszót. Lásd: [Előfeltételek](#prerequisites).

A befejezett sablon ehhez hasonlóan néz ki:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) szakaszban tekintheti meg. Ehelyett használja a következő PowerShell üzembehelyezési szkriptet:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Érdemes lehet ugyanazt a nevet használni, mint amikor előkészítette a bacpac-fájlt, hogy az összes erőforrás ugyanabban az erőforráscsoporthoz legyen tárolva.  Egyszerűbb az erőforrás kezelése, például az erőforrások tisztítása. Ha ugyanazt a projekt-nevet használja, távolítsa el a **New-AzResourceGroup** parancsot a parancsfájlból, vagy adja meg az y vagy n választ, ha a rendszer megkérdezi, hogy szeretné-e frissíteni a meglévő erőforráscsoportot.

Használjon generált jelszót. Lásd: [Előfeltételek](#prerequisites).

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

Ha az ügyfélszámítógépről szeretné elérni az SQL Servert, további tűzfalszabály hozzáadására van szükség. További információt az [IP-Tűzfalszabályok létrehozásával és kezelésével](../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules)foglalkozó témakörben talál.

A portálon válassza ki az SQL Database-adatbázist az újonnan üzembe helyezett erőforráscsoportból. Válassza a **Lekérdezésszerkesztő (előzetes verzió)** elemet, majd adja meg a rendszergazdai hitelesítő adatokat. Két, az adatbázisba importált táblázatot fog látni:

![Azure Resource Manager, sql-bővítmények üzembe helyezése, BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban üzembe helyezett egy SQL Servert és egy SQL Database-adatbázist, és importált egy BACPAC-fájlt. A BACPAC-fájlt egy Azure Storage-fiók tárolja. Az URL-címmel bárki hozzáférhet a fájlhoz. A BACPAC-fájl (összetevő) védelmének megismeréséhez lásd:

> [!div class="nextstepaction"]
> [Az összetevők biztonságossá tétele](./resource-manager-tutorial-secure-artifacts.md)

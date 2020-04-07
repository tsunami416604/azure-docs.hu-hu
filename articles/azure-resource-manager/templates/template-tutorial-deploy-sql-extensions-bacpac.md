---
title: SQL BACPAC-fájlok importálása sablonokkal
description: Megtudhatja, hogy miként importálhat SQL BACPAC-fájlokat az Azure Resource Manager-sablonokkal az Azure SQL Database-bővítmények használatával.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 83108c056035b16d26343d82c721b275ebcad0c5
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754325"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Oktatóanyag: SQL BACPAC-fájlok importálása ARM sablonokkal

Megtudhatja, hogy miként importálhat BACPAC-fájlt az Azure Resource Manager (ARM) sablonokkal, hogyan importálhat BACPAC-fájlt. A központi telepítési összetevők a központi telepítés végrehajtásához szükséges fájlok a fő sablonfájlokon kívül minden olyan fájl, amely szükséges. A BACPAC fájl egy műtárgy.

Ebben az oktatóanyagban hozzon létre egy sablont egy Azure SQL-kiszolgáló és egy SQL-adatbázis központi telepítéséhez és egy BACPAC-fájl importálásához. Az Azure virtuálisgép-bővítmények ARM-sablonok használatával történő központi telepítéséről [az Oktatóanyag: Virtuálisgép-bővítmények üzembe helyezése ARM-sablonokkal](./template-tutorial-deploy-vm-extensions.md)című témakörben olvashat.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * BACPAC-fájl előkészítése.
> * Nyisson meg egy rövid útmutató sablont.
> * A sablon szerkesztése.
> * Telepítse a sablont.
> * Ellenőrizze a központi telepítést.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio Code a Resource Manager Tools bővítménnyel. Lásd: [A Visual Studio-kód használata ARM-sablonok létrehozásához.](./use-vs-code-to-create-template.md)
* A biztonság növelése érdekében használjon létrehozott jelszót az Azure SQL Server rendszergazdai fiókjához. Íme egy példa, amelynek segítségével jelszót hozhat létre:

    ```console
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információ: [Az Azure Key Vault integrálása az ARM-sablon üzembe helyezésében](./template-tutorial-use-key-vault.md)című témakörben olvashat. Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="prepare-a-bacpac-file"></a>A BACPAC-fájl előkészítése

A BACPAC-fájl meg van osztva a [GitHubon.](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac) További információ saját fájl létrehozásához: [Azure SQL-adatbázis exportálása BACPAC-fájlba](../../sql-database/sql-database-export.md). Ha egy saját helyen kívánja közzétenni a fájlt, frissítenie kell a sablont az oktatóanyag egy későbbi részében.

A BACPAC-fájlt egy Azure Storage-fiókban kell tárolni, mielőtt ARM-sablon használatával importálható lenne. A következő PowerShell-parancsfájl a következő lépésekkel készíti elő a BACPAC-fájlt:

* Töltse le a BACPAC fájlt.
* Azure Storage-fiók létrehozása.
* Hozzon létre egy tárfiók blob tároló.
* Töltse fel a BACPAC-fájlt a tárolóba.
* A tárfiók kulcsának és a blob URL-címének megjelenítése.

1. Válassza **a Próbálja ki** a felhőhéj megnyitásához válassza a Próbálja ki. Ezután illessze be a következő PowerShell-parancsfájlt a rendszerhéj ablakába.

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

1. Írja le a tárfiók kulcsát és a BACPAC-fájl URL-címét. A sablon telepítésekor ezekre az értékekre van szükség.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az oktatóanyagban használt sablon a [GitHubban](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json)tárolódik.

1. A Visual Studio-kódból válassza a **Fájlmegnyitása** > **fájl**lehetőséget.
1. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.

    A sablonban két erőforrás van definiálva:

   * `Microsoft.Sql/servers`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

        A sablon testreszabása előtt hasznos lehet a sablon alapvető ismerete.
1. Válassza a **Fájlmentés** > **másként** lehetőséget, ha a fájl egy példányát az *azuredeploy.json*nevű számítógépre szeretné menteni.

## <a name="edit-the-template"></a>A sablon szerkesztése

1. Adjon hozzá még két paramétert a **paraméterek** szakasz végén a tárfiók kulcsának és a BACPAC URL-címének beállításához.

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

    Adjon hozzá vesszőt **az adminPassword**után . Ha a Visual Studio-kódból formázni szeretné a JSON-fájlt, válassza a Shift+Alt+F lehetőséget.

    A két érték eléréséről a [BACPAC-fájl előkészítése](#prepare-a-bacpac-file).

1. Adjon hozzá két további erőforrást a sablonhoz.

    * Ahhoz, hogy az SQL Database-bővítmény bacpac fájlokat importáljon, engedélyeznie kell az Azure-szolgáltatásokból érkező forgalmat. Adja hozzá a következő tűzfalszabály-definíciót az SQL-kiszolgáló definíciója alá:

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

        A sablon így néz ki:

        ![Sablon tűzfalszabály-definícióval](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

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

        A sablon így néz ki:

        ![Sablon SQL Database kiterjesztéssel](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        További információ az erőforrás-definícióról: [SQL Database-bővítmény referenciája](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). A következők a fontosabb elemek:

        * **dependsOn**: A bővítményerőforrást az SQL Database létrehozása után kell létrehozni.
        * **storageKeyType**: Adja meg a használni kívánt tárolókulcs típusát. Az értéke `StorageAccessKey` vagy `SharedAccessKey` lehet. Használja `StorageAccessKey` ebben az oktatóanyagban.
        * **storageKey**: Adja meg annak a tárfióknak a kulcsát, amelyben a BACPAC-fájlt tárolja. Ha a tárolókulcs `SharedAccessKey`típusa , akkor a "?" karakter előtt kell szerepelnie.
        * **storageUri**: Adja meg a tárfiókban tárolt BACPAC-fájl URL-címét.
        * **administratorLoginPassword**: Az SQL-rendszergazda jelszava. Generált jelszó használata. Lásd: [Előfeltételek](#prerequisites).

A befejezett sablon ehhez hasonlóan néz ki:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) szakaszban tekintheti meg. Ehelyett használja a következő PowerShell üzembehelyezési szkriptet:

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

Fontolja meg a BACPAC-fájl előkészítésekor használt projektnevet, hogy az összes erőforrás ugyanabban az erőforráscsoportban legyen tárolva. Ily módon egyszerűbben kezelheti az erőforrás-feladatokat, például az erőforrások tisztítását. Ha ugyanazt a projektnevet használja, `New-AzResourceGroup` eltávolíthatja a parancsot a parancsfájlból, vagy igennel (y) vagy nemmel (n) válaszolhat, amikor a rendszer megkérdezi, hogy frissíteni kívánja-e a meglévő erőforráscsoportot.

Generált jelszó használata. Lásd: [Előfeltételek](#prerequisites).

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

Az SQL-kiszolgáló ügyfélszámítógépről történő eléréséhez további tűzfalszabályt kell hozzáadnia. További információt az [IP-tűzfalszabályok létrehozása és kezelése](../../sql-database/sql-database-firewall-configure.md#create-and-manage-ip-firewall-rules)című témakörben talál.

Az Azure Portalon válassza ki az SQL-adatbázist az újonnan telepített erőforráscsoportból. Válassza a **Lekérdezésszerkesztő (előzetes verzió)** elemet, majd adja meg a rendszergazdai hitelesítő adatokat. Az adatbázisba importálva két táblát fog látni.

![Lekérdezésszerkesztő (előzetes verzió)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
1. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
1. Válassza ki az erőforráscsoport nevét. Az erőforráscsoportban összesen hat erőforrás jelenik meg.
1. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban telepített egy SQL-kiszolgálót és egy SQL-adatbázist, és importált egy BACPAC-fájlt. A sablonok telepítésével kapcsolatos hibák elhárításáról az:

> [!div class="nextstepaction"]
> [ARM-sablon-telepítések – problémamegoldás](./template-tutorial-troubleshoot.md)

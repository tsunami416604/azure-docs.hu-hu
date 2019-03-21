---
title: SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal | Microsoft Docs
description: Ismerje meg, hogy az SQL Database-bővítmény használata az Azure Resource Manager-sablonok SQL BACPAC-fájlok importálása.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/25/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 40c7e00ad5271457904fc3273fdb633b0c7a8016
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58085065"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Oktatóanyag: Az Azure Resource Manager-sablonok SQL BACPAC-fájlok importálása

Ismerje meg, hogyan BACPAC-fájl, az Azure Resource Manager-sablonok importálása az Azure SQL Database-bővítmények segítségével. Üzembe helyezési összetevők olyan fájlok, a fő sablonfájl egy központi telepítés befejezéséhez szükséges mellett. A BACPAC-fájlba az összetevőt. Ebben az oktatóanyagban-sablon üzembe helyezése egy Azure SQL Server, SQL-adatbázis létrehozása és a egy BACPAC-fájl importálása. Az Azure Resource Manager-sablonok használata az Azure virtuálisgép-bővítmények központi telepítésével kapcsolatos információkért lásd: [# oktatóanyag: Üzembe helyezése Azure Resource Manager-sablonokkal a virtuális gépi bővítmények](./resource-manager-tutorial-deploy-vm-extensions.md).

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

* [Visual Studio Code](https://code.visualstudio.com/) a Resource Manager Tools bővítménnyel. Lásd [a bővítmény telepítését](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) ismertető részt.
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót az SQL Server rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd: [oktatóanyag: Integrálhatja az Azure Key Vault Resource Manager-sablon üzembe helyezési](./resource-manager-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="prepare-a-bacpac-file"></a>A BACPAC-fájl előkészítése

BACPAC-fájl meg van osztva egy [Azure Storage-fiók](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac) a nyilvános hozzáférést. További információ saját fájl létrehozásához: [Azure SQL Database-adatbázis exportálása BACPAC-fájlba](../sql-database/sql-database-export.md). Ha egy saját helyen kívánja közzétenni a fájlt, frissítenie kell a sablont az oktatóanyag egy későbbi részében.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az Azure-beli gyorsindítási sablonok a Resource Manager-sablonok adattáraként szolgálnak. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. Az oktatóanyagban használt sablon neve [Deploy an Azure SQL Server with Threat Detection](https://azure.microsoft.com/resources/templates/201-sql-threat-detection-server-policy-optional-db/) (Azure SQL Server-kiszolgáló üzembe helyezése fenyegetésészleléssel).

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-sql-threat-detection-server-policy-optional-db/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.

    A sablonban három erőforrás van meghatározva:

   * `Microsoft.Sql/servers`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/securityalertpolicies).
   * `Microsoft.SQL.servers/databases`.  Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).

     Érdemes megismerkedni a sablon alapvető működésével, mielőtt megkezdi annak testreszabását.
4. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.

## <a name="edit-the-template"></a>A sablon szerkesztése

Adjon hozzá két további erőforrásokat a sablonhoz.

* Ahhoz, hogy az SQL Database-bővítmény BACPAC-fájlokat importálhasson, engedélyeznie kell az Azure-szolgáltatásokhoz való hozzáférést. Adja hozzá az alábbi JSON-t az SQL Server definíciójához:

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2015-05-01-preview",
        "dependsOn": [
            "[variables('databaseServerName')]"
        ],
        "properties": {
            "startIpAddress": "0.0.0.0",
            "endIpAddress": "0.0.0.0"
        }
    }
    ```

    A sablon a következőképpen fog kinézni:

    ![Azure Resource Manager, sql-bővítmények üzembe helyezése, BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

* Adjon hozzá egy SQL Database-bővítmény erőforrást az adatbázis definíciójához az alábbi JSON-nal:

    ```json
    "resources": [
        {
            "name": "Import",
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "dependsOn": [
                "[resourceId('Microsoft.Sql/servers/databases', variables('databaseServerName'), variables('databaseName'))]"
            ],
            "properties": {
                "storageKeyType": "SharedAccessKey",
                "storageKey": "?",
                "storageUri": "https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac",
                "administratorLogin": "[variables('databaseServerAdminLogin')]",
                "administratorLoginPassword": "[variables('databaseServerAdminLoginPassword')]",
                "operationMode": "Import",
            }
        }
    ]
    ```

    A sablon a következőképpen fog kinézni:

    ![Azure Resource Manager, sql-bővítmények üzembe helyezése, BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

    További információ az erőforrás-definícióról: [SQL Database-bővítmény referenciája](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases/extensions). A következők a fontosabb elemek:

    * **dependsOn**: Az SQL-adatbázis létrehozása után a bővítmény erőforrást kell létrehozni.
    * **storageKeyType**: A tárkulcs típusa. Az értéke `StorageAccessKey` vagy `SharedAccessKey` lehet. Mivel a mellékelt BACPAC-fájl egy nyilvános hozzáférésű Azure Storage-fiókban van megosztva, a SharedAccessKey értéket kell használni.
    * **StorageKey tulajdonságát**: A tárkulcs. Ha SharedAccessKey típusú tárkulcsot használ, szerepelnie kell előtte egy „?” karakternek.
    * **storageUri**: A tároló URI-t használja. Ha nem a mellékelt BACPAC-fájlt kívánja használni, frissítenie kell az értékeket.
    * **administratorLoginPassword**: Az SQL-rendszergazda jelszava. Használni létrehozott jelszót. Lásd: [Előfeltételek](#prerequisites).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) szakaszban tekintheti meg. Ehelyett használja a következő PowerShell üzembehelyezési szkriptet:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json"
```

Használni létrehozott jelszót. Lásd: [Előfeltételek](#prerequisites).

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

A portálon válassza ki az SQL Database-adatbázist az újonnan üzembe helyezett erőforráscsoportból. Válassza a **Lekérdezésszerkesztő (előzetes verzió)** elemet, majd adja meg a rendszergazdai hitelesítő adatokat. Két, az adatbázisba importált táblázatot fog látni:

![Azure Resource Manager, sql-bővítmények üzembe helyezése, BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban üzembe helyezett egy SQL Servert és egy SQL Database-adatbázist, és importált egy BACPAC-fájlt. A BACPAC-fájlba egy Azure storage-fiók van tárolva. Az URL-cím birtokában bárki férhetnek hozzá a fájlhoz. Ismerje meg, hogyan teheti biztonságossá a BACPAC-fájlba (összetevők), lásd:

> [!div class="nextstepaction"]
> [Az összetevők biztonságos](./resource-manager-tutorial-secure-artifacts.md)

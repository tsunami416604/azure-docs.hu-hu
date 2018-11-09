---
title: SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal | Microsoft Docs
description: Ismerje meg, hogyan használható az SQL Database-bővítmény SQL BACPAC-fájlok importálására Azure Resource Manager-sablonokkal.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/29/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 875662ec3bf4582c58f98e8afe3753fc202ad0b6
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243503"
---
# <a name="tutorial-import-sql-bacpac-files-with-azure-resource-manager-templates"></a>Oktatóanyag: SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal

Ismerje meg, hogyan importálhat BACPAC-fájlokat Azure SQL Database-bővítményekkel. Ebben az oktatóanyagban létrehoz egy sablont egy Azure SQL Server, egy SQL Database és egy BACPAC-fájl üzembe helyezéséhez. További információ az Azure-beli virtuális gépi bővítmények üzembe helyezéséről Azure Resource Manager-sablonok használatával: [# Oktatóanyag: Virtuális gépi bővítmények üzembe helyezése Azure Resource Manager-sablonokkal](./resource-manager-tutorial-deploy-vm-extensions.md).

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
    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd [Oktatóanyag: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába](./resource-manager-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="prepare-a-bacpac-file"></a>A BACPAC-fájl előkészítése

A BACPAC-fájl egy [nyilvános hozzáférésű Azure Storage-fiókon](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac) van megosztva. További információ saját fájl létrehozásához: [Azure SQL Database-adatbázis exportálása BACPAC-fájlba](../sql-database/sql-database-export.md). Ha egy saját helyen kívánja közzétenni a fájlt, frissítenie kell a sablont az oktatóanyag egy későbbi részében.

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

Két további erőforrást kell hozzáadnia a sablonhoz.

* Ahhoz, hogy az SQL Database-bővítmény BACPAC-fájlokat importálhasson, engedélyeznie kell az Azure-szolgáltatásokhoz való hozzáférést. Adja hozzá az alábbi JSON-t az SQL Server definíciójához:

    ```json
    {
        "type": "firewallrules",
        "name": "AllowAllAzureIps",
        "location": "[parameters('location')]",
        "apiVersion": "2014-04-01",
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

    További információ az erőforrás-definícióról: [SQL Database-bővítmény referenciája](https://docs.microsoft.com/en-us/azure/templates/microsoft.sql/servers/databases/extensions). A következők a fontosabb elemek:

    * **dependsOn**: A bővítményerőforrást az SQL Database létrehozása után kell létrehozni.
    * **storageKeyType**: A használni kívánt tárkulcs típusa. Az értéke `StorageAccessKey` vagy `SharedAccessKey` lehet. Mivel a mellékelt BACPAC-fájl egy nyilvános hozzáférésű Azure Storage-fiókban van megosztva, a SharedAccessKey értéket kell használni.
    * **storageKey**: A használni kívánt tárkulcs. Ha SharedAccessKey típusú tárkulcsot használ, szerepelnie kell előtte egy „?” karakternek.
    * **storageUri**: A használni kívánt tárolási uri. Ha nem a mellékelt BACPAC-fájlt kívánja használni, frissítenie kell az értékeket.
    * **administratorLoginPassword**: Az SQL-rendszergazda jelszava. Javasoljuk, hogy automatikusan létrehozott jelszót használjon. Lásd: [Előfeltételek](#prerequisites).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) szakaszban tekintheti meg. Ehelyett használja a következő PowerShell üzembehelyezési szkriptet:

```azurepowershell
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmResourceGroupDeployment -Name $deploymentName `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile azuredeploy.json
```

Javasoljuk, hogy automatikusan létrehozott jelszót használjon. Lásd: [Előfeltételek](#prerequisites).

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

Ebben az oktatóanyagban üzembe helyezett egy SQL Servert és egy SQL Database-adatbázist, és importált egy BACPAC-fájlt. Annak megismeréséhez, hogyan lehet Azure-erőforrásokat több régióban üzembe helyezni, és hogyan alkalmazhat biztonságos üzembehelyezési gyakorlatokat, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Az Azure Deployment Manager használata](./resource-manager-tutorial-deploy-vm-extensions.md)

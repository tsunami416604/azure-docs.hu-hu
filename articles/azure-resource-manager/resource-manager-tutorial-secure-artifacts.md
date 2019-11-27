---
title: Biztonságos összetevők a sablonokban
description: Ismerje meg, hogyan védheti meg az Azure Resource Manager-sablonokkal a használt összetevőket.
author: mumian
ms.date: 10/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b37f7e284b655a362c5a4231a7c1da3719762644
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326443"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Oktatóanyag: Biztonságos összetevők az Azure Resource Manager-sablon-üzembehelyezések

Ismerje meg, hogyan teheti biztonságossá az összetevőket az Azure Resource Manager-sablonok az Azure Storage-fiók használata közös hozzáférésű jogosultságkódok (SAS) használt. Üzembe helyezési összetevők olyan fájlok, a fő sablonfájl mellett a telepítés befejezéséhez szükséges. Például az [oktatóanyagban: SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)a fő sablon létrehoz egy Azure SQL Database; egy BACPAC-fájlt is meghív a táblák létrehozásához és az adatbeszúráshoz. A BACPAC-fájlba az összetevőt. A lehívandó összetevő nyilvános hozzáférés az Azure storage-fiók tárolva van. Ebben az oktatóanyagban SAS használhatja a BACPAC-fájlba korlátozott hozzáférés adható a saját Azure Storage-fiókban. További információ az SAS-ról: [közös hozzáférésű aláírások (SAS) használata](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

A csatolt sablonok biztonságossá tételéhez tekintse meg az [oktatóanyag: csatolt Azure Resource Manager-sablonok létrehozása](./resource-manager-tutorial-create-linked-templates.md)című témakört.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A BACPAC-fájl előkészítése
> * Nyisson meg egy meglévő sablon
> * A sablon szerkesztése
> * A sablon üzembe helyezése
> * A telepítés ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio Code a Resource Manager-eszközök bővítménnyel. További információ: [Azure Resource Manager sablonok létrehozása a Visual Studio Code használatával](./resource-manager-tools-vs-code.md).
* [Oktatóanyag áttekintése: SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). Ebben az oktatóanyagban használt sablon fejlesztett ki, hogy az oktatóanyag lesz. Ebben a cikkben egy letöltési hivatkozást a befejezett sablon áll rendelkezésre.
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót az SQL Server rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd [Oktatóanyag: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába](./resource-manager-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="prepare-a-bacpac-file"></a>A BACPAC-fájl előkészítése

Ebben a szakaszban, készítse elő a BACPAC-fájlba, a fájl elérhető biztonságosan központi telepítése esetén a Resource Manager-sablon. Ez a szakasz öt eljárás szerepelnek:

* Töltse le a BACPAC-fájlba.
* Azure Storage-fiók létrehozása.
* Hozzon létre egy Storage-fiók Blob tárolóba.
* Töltse fel a BACPAC-fájlt a tárolóba.
* A SAS-jogkivonat a BACPAC-fájl lekérése.

A lépések PowerShell-parancsfájl használatával történő automatizálásához tekintse meg a [csatolt sablon feltöltése](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template)a szkriptből című témakört.

### <a name="download-the-bacpac-file"></a>A BACPAC-fájl letöltése

Töltse le a [BACPAC fájlt](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac), és mentse a fájlt a helyi számítógépre a **SQLDatabaseExtension. BACPAC**névvel.

### <a name="create-a-storage-account"></a>Create a storage account

1. Válassza ki az alábbi képre kattintva megnyithatja a Resource Manager-sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Adja meg a következő tulajdonságokat:

    * **Előfizetés**: Válassza ki az Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, és adjon meg egy nevet. Egy erőforráscsoportot az Azure-erőforrások tárolója felügyeleti erre a célra. Ebben az oktatóanyagban a tárfiók és az Azure SQL Database használhatja ugyanazt az erőforráscsoportot. Jegyezze fel az erőforráscsoport neve, szüksége lesz rá az Azure SQL Database később az oktatóanyagok létrehozásakor.
    * **Hely**: válasszon régiót. Például az **USA középső**régiója.
    * **Storage-fiók típusa**: használja az alapértelmezett értéket, amely **Standard_LRS**.
    * **Hely**: használja az alapértelmezett értéket, amely **[resourceGroup (). location]** . Ez azt jelenti, hogy a tárfiók használata az erőforráscsoport helyét.
    * **Elfogadom a fentiekben megkezdett feltételeket és kikötéseket**: (kiválasztva)
3. Válassza a **Beszerzés** lehetőséget.
4. Válassza ki az értesítési ikonra (Harang ikon) a központi telepítési állapotának megtekintéséhez a portál jobb felső sarokban.

    ![Erőforrás-kezelő oktatóanyag portál értesítési panel](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. Miután a Storage-fiók üzembe helyezése sikeres volt, az értesítési panel **Ugrás az erőforráscsoport** elemére kattintva nyissa meg az erőforráscsoportot.

### <a name="create-a-blob-container"></a>Blobtároló létrehozása

Egy Blob-tárolóba van szükség a fájlok feltöltése előtt.

1. A megnyitáshoz válassza ki a tárfiókot. Csak egy storage-fiókot az erőforráscsoportban szereplő gondoskodnak. A tárfiók nevét az alábbi képernyőképen látható egy eltér.

    ![Erőforrás-kezelő oktatóanyag storage-fiók](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Válassza a **Blobok** csempét.

    ![Útmutató Resource Manager-blobok](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Új tároló létrehozásához válassza a felül található **+ tároló** elemet.
4. Írja be a következő értékeket:

    * **Név**: adja meg a **sqlbacpac**.
    * **Nyilvános hozzáférési szint**: használja az alapértelmezett értéket, a **privát (nincs névtelen hozzáférés)** .
5. Kattintson az **OK** gombra.
6. Az újonnan létrehozott tároló megnyitásához válassza a **sqlbacpac** lehetőséget.

### <a name="upload-the-bacpac-file-to-the-container"></a>A BACPAC-fájl feltöltése a tárolóba

1. Válassza a **Feltöltés** lehetőséget.
2. Írja be a következő értékeket:

    * **Fájlok**: az utasításokat követve válassza ki a korábban letöltött BACPAC-fájlt. Az alapértelmezett név a **SQLDatabaseExtension. bacpac**.
    * **Hitelesítés típusa**: válassza az **sas**elemet.  Az *sas* az alapértelmezett érték.
3. Válassza a **Feltöltés** lehetőséget.  Miután sikeresen feltöltötte a fájlt, a fájlnév fel kell sorolni a tárolóban.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />SAS-token létrehozása

1. Kattintson a jobb gombbal a **SQLDatabaseExtension. bacpac** elemre a tárolóból, majd válassza az SAS-csoport **előállítása**lehetőséget.
2. Írja be a következő értékeket:

    * **Engedély**: használja az alapértelmezett, **olvasás**.
    * **Kezdési és lejárati dátum/idő**: az alapértelmezett érték nyolc órát biztosít az SAS-token használatára. Ha több időre van szüksége az oktatóanyag elvégzéséhez, a frissítés **lejár**.
    * **Engedélyezett IP-címek**: hagyja üresen ezt a mezőt.
    * **Engedélyezett protokollok**: használja az alapértelmezett értéket: **https**.
    * **Aláíró kulcs**: használja az alapértelmezett értéket: **1. kulcs**.
3. Válassza **a blob sas-token és URL-cím előállítása**lehetőséget.
4. Készítsen másolatot a **blob sas URL-címéről**. Az URL-cím közepén a fájlnév a **SQLDatabaseExtension. bacpac**.  A fájl nevét az URL-címet osztja három részből áll:

   - Összetevő **helye**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Ellenőrizze, hogy a hely végződik a "/".
   - **BACPAC-fájl neve**: SQLDatabaseExtension. BACPAC.
   - Összetevő **helye sas-token**: Ellenőrizze, hogy a jogkivonat megelőzi-e a "?" előtagot.

     [A sablon üzembe helyezéséhez](#deploy-the-template)a következő három érték szükséges.

## <a name="open-an-existing-template"></a>Nyisson meg egy meglévő sablon

Ebben a munkamenetben módosítja az [oktatóanyagban létrehozott sablont: az SQL BACPAC-fájlok importálása Azure Resource Manager-sablonokkal](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) a BACPAC-fájl sas-tokenrel való meghívásához.  Az SQL-bővítmény oktatóanyagában kifejlesztett sablon meg van osztva a [githubban](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.

    Nincsenek definiálva a sablonban öt erőforrások:

   * `Microsoft.Sql/servers`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules`. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases`.  Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions`.  Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Érdemes megismerkedni a sablon alapvető működésével, mielőtt megkezdi annak testreszabását.
4. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.

## <a name="edit-the-template"></a>A sablon szerkesztése

Adja hozzá a következő további paraméterek:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Erőforrás-kezelő oktatóanyag biztonságos összetevők paramétereit](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Frissítse az értéket, a következő két elemet:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) szakaszban tekintheti meg. Ehelyett használja a következő PowerShell üzembehelyezési szkriptet:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Használni létrehozott jelszót. Lásd: [Előfeltételek](#prerequisites).
_ArtifactsLocation, _artifactsLocationSasToken és bacpacFileName értékeit lásd: SAS- [token létrehozása](#generate-a-sas-token).

## <a name="verify-the-deployment"></a>A telepítés ellenőrzése

A portálon válassza ki az SQL Database-adatbázist az újonnan üzembe helyezett erőforráscsoportból. Válassza a **Lekérdezésszerkesztő (előzetes verzió)** elemet, majd adja meg a rendszergazdai hitelesítő adatokat. Két, az adatbázisba importált táblázatot fog látni:

![Azure Resource Manager, sql-bővítmények üzembe helyezése, BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban üzembe helyezett egy SQL Server, SQL-adatbázis és a SAS-jogkivonat használatával egy BACPAC-fájlba importálni. A Resource Manager-sablonok folyamatos fejlesztéséhez és üzembe helyezéséhez szükséges Azure-folyamat létrehozásáról további információt a következő témakörben talál:.

> [!div class="nextstepaction"]
> [Folyamatos integráció az Azure-folyamattal](./resource-manager-tutorial-use-azure-pipelines.md)

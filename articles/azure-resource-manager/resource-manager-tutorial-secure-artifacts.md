---
title: Az Azure Resource Manager-sablon-üzembehelyezések összetevők biztonságos |} A Microsoft Docs
description: Ismerje meg, hogyan védheti meg az Azure Resource Manager-sablonokkal a használt összetevőket.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: c83865835dfff4013072757c8f40a8d8fc83deaa
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55493242"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Oktatóanyag: Az Azure Resource Manager-sablon-üzembehelyezések biztonságos összetevők

Ismerje meg, hogyan teheti biztonságossá az összetevőket az Azure Resource Manager-sablonok az Azure Storage-fiók használata közös hozzáférésű jogosultságkódok (SAS) használt. Üzembe helyezési összetevők olyan fájlok, a fő sablonfájl mellett a telepítés befejezéséhez szükséges. Ha például a [oktatóanyag: Az Azure Resource Manager-sablonok SQL BACPAC-fájlok importálása a](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), a fő sablont hoz létre az Azure SQL Database; táblákat hozhat létre és adatokat szúrhat be egy BACPAC-fájlba is meghívja. A BACPAC-fájlba az összetevőt. A lehívandó összetevő nyilvános hozzáférés az Azure storage-fiók tárolva van. Ebben az oktatóanyagban SAS használhatja a BACPAC-fájlba korlátozott hozzáférés adható a saját Azure Storage-fiókban. További információ a SAS: [a közös hozzáférésű jogosultságkód (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Ismerje meg, hogyan teheti biztonságossá a hivatkozott sablonnak, lásd: [oktatóanyag: A csatolt Azure Resource Manager-sablonok létrehozása](./resource-manager-tutorial-create-linked-templates.md).

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A BACPAC-fájl előkészítése
> * Nyisson meg egy meglévő sablon
> * A sablon szerkesztése
> * A sablon üzembe helyezése
> * Az üzemelő példány ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/) a Resource Manager Tools bővítménnyel. Lásd [a bővítmény telepítését](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) ismertető részt.
* Felülvizsgálat [oktatóanyag: Az Azure Resource Manager-sablonok SQL BACPAC-fájlok importálása a](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). Ebben az oktatóanyagban használt sablon fejlesztett ki, hogy az oktatóanyag lesz. Ebben a cikkben egy letöltési hivatkozást a befejezett sablon áll rendelkezésre.
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót az SQL Server rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd: [oktatóanyag: Integrálhatja az Azure Key Vault Resource Manager-sablon üzembe helyezési](./resource-manager-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="prepare-a-bacpac-file"></a>A BACPAC-fájl előkészítése

Ebben a szakaszban, készítse elő a BACPAC-fájlba, a fájl elérhető biztonságosan központi telepítése esetén a Resource Manager-sablon. Ez a szakasz öt eljárás szerepelnek:

* Töltse le a BACPAC-fájlba.
* Azure Storage-fiók létrehozása.
* Hozzon létre egy Storage-fiók Blob tárolóba.
* Töltse fel a BACPAC-fájlt a tárolóba.
* A SAS-jogkivonat a BACPAC-fájl lekérése.

Ezeket a lépéseket egy PowerShell-parancsprogram használatával automatizálhatja, tekintse meg a parancsfájl [töltse fel a hivatkozott sablonnak](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>A BACPAC-fájl letöltése

Töltse le a [BACPAC-fájlba](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/SQLDatabaseExtension.bacpac), és mentse a fájlt a helyi számítógépre ugyanazzal a névvel, **SQLDatabaseExtension.bacpac**.

### <a name="create-a-storage-account"></a>Tárfiók létrehozása

1. Válassza ki az alábbi képre kattintva megnyithatja a Resource Manager-sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Adja meg a következő tulajdonságokat:

    * **Előfizetés**: Válassza ki az Azure-előfizetését.
    * **Erőforráscsoport**: Válassza ki **új létrehozása** , és adjon meg egy nevet. Egy erőforráscsoportot az Azure-erőforrások tárolója felügyeleti erre a célra. Ebben az oktatóanyagban a tárfiók és az Azure SQL Database használhatja ugyanazt az erőforráscsoportot. Jegyezze fel az erőforráscsoport neve, szüksége lesz rá az Azure SQL Database később az oktatóanyagok létrehozásakor.
    * **Hely**: Válasszon régiót. Ha például **USA középső RÉGIÓJA**. 
    * **Tárfiók típusa**: használja az alapértelmezett értéket, amely **Standard_LRS**.
    * **Hely**: Használja az alapértelmezett értéket, amely **[resourceGroup () .location]**. Ez azt jelenti, hogy a tárfiók használata az erőforráscsoport helyét.
    * **Elfogadom a feltételeket és a fenti lépések feltételek**: (kiválasztva)
3. Válassza a **Beszerzés** lehetőséget.
4. Válassza ki az értesítési ikonra (Harang ikon) a központi telepítési állapotának megtekintéséhez a portál jobb felső sarokban.

    ![Erőforrás-kezelő oktatóanyag portál értesítési panel](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. A storage-fiók sikeres telepítése után válassza ki a **erőforráscsoport megnyitása** nyissa meg az erőforráscsoport az értesítési panelről.

### <a name="create-a-blob-container"></a>Blobtároló létrehozása

Egy Blob-tárolóba van szükség a fájlok feltöltése előtt. 

1. A megnyitáshoz válassza ki a tárfiókot. Csak egy storage-fiókot az erőforráscsoportban szereplő gondoskodnak. A tárfiók nevét az alábbi képernyőképen látható egy eltér.

    ![Erőforrás-kezelő oktatóanyag storage-fiók](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Válassza ki a **Blobok** csempére.

    ![Útmutató Resource Manager-blobok](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Válassza ki **+ tároló** hozzon létre egy új tárolót, a lista elejéről.
4. Írja be a következő értékeket:

    * **Név**: Adja meg **sqlbacpac**. 
    * **Nyilvános hozzáférés szintje**: használja az alapértelmezett értéket **privát (nincs névtelen hozzáférés)**.
5. Kattintson az **OK** gombra.
6. Válassza ki **sqlbacpac** megnyitásához az újonnan létrehozott tárolóban.

### <a name="upload-the-bacpac-file-to-the-container"></a>A BACPAC-fájl feltöltése a tárolóba

1. Válassza a **Feltöltés** lehetőséget.
2. Írja be a következő értékeket:

    * **Fájlok**: Az utasításokat követve válassza ki a BACPAC-fájlt az alábbi, korábban letöltött. Alapértelmezés szerint ez **SQLDatabaseExtension.bacpac**.
    * **Hitelesítési típus**: Válassza ki **SAS**.  *SAS* az alapértelmezett érték.
3. Válassza a **Feltöltés** lehetőséget.  Miután sikeresen feltöltötte a fájlt, a fájlnév fel kell sorolni a tárolóban.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />Hozzon létre egy SAS-token

1. Kattintson a jobb gombbal **SQLDatabaseExtension.bacpac** a tárolót, és válassza ki a **SAS létrehozása**.
2. Írja be a következő értékeket:

    * **Engedély**: Használja az alapértelmezett **olvasási**.
    * **Kezdési és lejárati dátum/idő**: Az alapértelmezett érték a SAS-tokennel nyolc órát biztosítja. Ha ez az oktatóanyag további időre van szüksége, frissítse **lejárati**.
    * **Engedélyezett IP-címei**: Hagyja üresen a mezőt.
    * **Engedélyezett protokollok**: használja az alapértelmezett érték: **HTTPS**.
    * **Aláíró kulcs**: használja az alapértelmezett érték: **Kulcs 1**.
3. Válassza ki **készítése a blob SAS-jogkivonat és URL-cím**.
4. Készítsen másolatot **a Blob SAS URL-cím**. Az URL-cím közepén van a Fájlnév **SQLDatabaseExtension.bacpac**.  A fájl nevét az URL-címet osztja három részből áll:

    - **Hely összetevő**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Ellenőrizze, hogy a hely végződik a "/".
    - **BACPAC-fájl neve**: SQLDatabaseExtension.bacpac.
    - **Összetevő hely SAS-jogkivonat**: Ellenőrizze, hogy a jogkivonat szerepel egy "?."

    Az a három értékre szüksége [helyezheti üzembe a sablont](#deploy-the-template).

## <a name="open-an-existing-template"></a>Nyisson meg egy meglévő sablon

Ebben a munkamenetben létrehozott sablont módosít [oktatóanyag: Az Azure Resource Manager-sablonok SQL BACPAC-fájlok importálása a](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) hívni az SAS-jogkivonat használatával BACPAC-fájlba.  A sablon az SQL-bővítmény oktatóanyagban fejlesztett megosztott [ https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json ](https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json).

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://armtutorials.blob.core.windows.net/sqlextensionbacpac/azuredeploy.json
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

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) szakaszban tekintheti meg. Ehelyett használja a következő PowerShell üzembehelyezési szkriptet:

```azurepowershell
$deploymentName = Read-Host -Prompt "Enter the name for this deployment"
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -Name $deploymentName `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile azuredeploy.json
```

Használni létrehozott jelszót. Lásd: [Előfeltételek](#prerequisites).
Az értékek _artifactsLocation, _artifactsLocationSasToken és bacpacFileName, lásd: [hozzon létre egy SAS-token](#generate-a-sas-token).

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

Ebben az oktatóanyagban üzembe helyezett egy SQL Server, SQL-adatbázis és a SAS-jogkivonat használatával egy BACPAC-fájlba importálni. Annak megismeréséhez, hogyan lehet Azure-erőforrásokat több régióban üzembe helyezni, és hogyan alkalmazhat biztonságos üzembehelyezési gyakorlatokat, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Az Azure Deployment Manager használata](./resource-manager-tutorial-deploy-vm-extensions.md)

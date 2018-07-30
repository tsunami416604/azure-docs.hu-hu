---
title: Titkosított tárfiók üzembe helyezésére szolgáló Azure Resource Manager-sablon létrehozása | Microsoft Docs
description: A titkosított tárfiók üzembe helyezésére szolgáló sablont a Visual Studio Code-dal fogja létrehozni.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7c78636a210ae90c5bfe1d0bfd35e4e05633f5cd
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188199"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Oktatóanyag: Titkosított tárfiók üzembe helyezésére szolgáló Azure Resource Manager-sablon létrehozása

Ismerje meg, hogyan keresheti meg az Azure Resource Manager-sablon befejezéséhez szükséges adatokat.

Ebben az oktatóanyagban az Azure-beli gyorsindítási sablonok egyik alapszintű sablonját fogja használni egy Azure Storage-fiók létrehozásához.  Titkosított tárfiók létrehozásához a sablon referenciadokumentációjának használatával szabhatja testre az alapszintű sablont.

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon formátumának ismertetése
> * Paraméterek használata a sablonban
> * Változók használata a sablonban
> * A sablon szerkesztése
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* [Visual Studio Code](https://code.visualstudio.com/).
* Resource Manager Tools bővítmény. A telepítésről további információkat [a Resource Manager Tools bővítmény telepítését](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites) ismertető részben talál.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az ebben a rövid útmutatóban használt sablon [standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/) néven található meg. A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
4. A **File** (Fájl) > **Save as** (Mentés másként) lehetőség kiválasztásával mentheti a fájlt a helyi számítógépre, **azuredeploy.json** néven.

## <a name="understand-the-format"></a>A formátum ismertetése

A VS Code-ban csukja össze a sablont a gyökérszintig. Az ekkor előálló legegyszerűbb struktúra a következő elemeket tartalmazza:

![Resource Manager-sablon – Legegyszerűbb struktúra](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**: adja meg a sablon nyelvének verziószámát tartalmazó JSON-sémafájl helyét.
* **contentVersion**: adjon meg egy tetszőleges értéket ehhez az elemhez a sablon lényeges módosításainak dokumentálásához.
* **parameters**: adja meg az üzembe helyezéskor beállított értékeket az erőforrás üzembe helyezésének testreszabásához.
* **variables**: adja meg a sablonban JSON-töredékekként használt értékeket a sablonnyelvi kifejezések leegyszerűsítéséhez.
* **resources**: adja meg az erőforráscsoportban üzembe helyezett vagy frissített erőforrástípusokat.
* **outputs**: adja meg az üzembe helyezés után visszaadott értékeket.

## <a name="use-parameters-in-template"></a>Paraméterek használata a sablonban

Paraméterek megadásával testreszabhatja az üzemelő példányt úgy, hogy az adott környezetnek megfelelő értékeket ad meg. A tárfiókhoz tartozó értékek beállításakor a sablonban meghatározott paramétereket fogja használni.

![Resource Manager-sablon – Paraméterek](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

Ebben a sablonban két paraméter van meghatározva. Figyelje meg a location.defaultValue paraméterben használt sablonfüggvényt:

```json
"defaultValue": "[resourceGroup().location]",
```

A resourceGroup() függvény az aktuális erőforráscsoportot jelölő objektumot adja vissza. A sablonfüggvények listájáért lásd az [Azure Resource Manager-sablonfüggvényeket](./resource-group-template-functions.md).

A sablonban meghatározott paraméterek használata:

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables-in-template"></a>Változók használata a sablonban

A változók segítségével a sablonban használható értékeket állíthat össze. A változók segítségével leegyszerűsíthetők a sablonok.

![Resource Manager-sablon – Változók](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

Ez a sablon egy változót (*storageAccountName*) határoz meg. A definícióban a következő két sablonfüggvény használjuk:

- **concat()**: sztringek összefűzésére. További információkért lásd a [concat](./resource-group-template-functions-string.md#concat) ismertetését.
- **uniqueString()**: determinisztikus kivonatsztring a paraméterekként megadott értékek alapján történő létrehozásához. Minden Azure-tárfióknak – az Azure egészét figyelembe véve – egyedi névvel kell rendelkeznie. Ez a függvény egy egyedi sztringet állít elő. További sztringfüggvényekért lásd a [sztringfüggvények](./resource-group-template-functions-string.md) ismertetését.

A sablonban meghatározott változó használata:

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>A sablon szerkesztése

A tárfiók titkosítással kapcsolatos konfigurációjának megkereséséhez az Azure Storage-fiók sablonreferenciáját fogja használni.

1. Tallózással keresse meg az [Azure Templates](https://docs.microsoft.com/azure/templates/) (Azure-sablonok) elemet.
2. A bal oldali tartalomjegyzékben válassza ki a **Reference** (Referencia) -> **Storage** -> **Storage Accounts** (Tárfiókok) elemet. Ezen az oldalon megtalálhatók a tárfiókadatok meghatározásával kapcsolatos információk.
3. Tekintse át a titkosítással kapcsolatos információkat.  
4. A tárfiók erőforrás-definíciójának tulajdonságokat tartalmazó eleméhez adja hozzá a következő JSON-adatokat:

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
    Ebben a részben történik a Blob Storage szolgáltatás titkosítási funkciójának engedélyezése.

Az erőforrások eleme végül a következőképpen fog kinézni:

![Resource Manager-sablon – Titkosított tárfiók – Erőforrások](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablonok üzembe helyezésének számos módszere létezik.  Ebben az oktatóanyagban az Azure Portal Cloud Shelljét fogja használni. A Cloud Shell az Azure CLI és az Azure PowerShell használatát is támogatja. Az itt található utasítások a CLI használata esetén érvényesek.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com)
2. Válassza ki a jobb felső sarokban a **Cloud Shell** elemet az alábbi képen látható módon:

    ![Azure Portal – Cloud Shell](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell.png)

3. Kattintson a lefelé mutató nyílra, majd válassza ki a **Bash** elemet, ha nem az szerepel ott. Ebben az oktatóanyagban az Azure CLI-t fogja használni.

    ![Azure Portal – Cloud Shell – Parancssori felület](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-choose-cli.png)
4. Kattintson az **Újraindítás** lehetőségre a felület újraindításához.
5. Kattintson a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemre.

    ![Azure Portal – Cloud Shell – Fájl feltöltése](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-upload-file.png)
6. Válassza ki az oktatóanyag korábbi részében mentett fájlt. Alapértelmezés szerint a fájl neve a következő: **azuredeploy.json**.
7. A fájl sikeres feltöltésének ellenőrzéséhez futtassa az **ls** parancsot a Cloud Shellben. A **cat** paranccsal a sablon tartalmát is ellenőrizheti.

    ![Azure Portal – Cloud Shell – Fájlok listázása](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-list-file.png)
8. Futtassa az alábbi parancsokat a Cloud Shellben:

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file azuredeploy.json
    ```
    Az alábbi képernyőképen egy üzembehelyezési minta látható:

    ![Azure Portal – Cloud Shell – Sablon üzembe helyezése](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-deploy-template.png)

    A képernyőképen az alábbi értékek szerepelnek:

    * **&lt;ResourceGroupName>**: myresourcegroup0719. A paraméter két helyen jelenik meg.  Győződjön meg arról, hogy ugyanazt az értéket használja mindkét helyen.
    * **&lt;AzureLocation>**: eastus2
    * **&lt;DeployName>**: mydeployment0719
    * **&lt;TemplateFile>**: azuredeploy.json

    A képernyőkép kimenetén a tárfiók neve a következő: *fhqbfslikdqdsstandardsa*. 

9. Az alábbi PowerShell-parancs futtatásával megjelenítheti az újonnan létrehozott tárfiókot:

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

    Az alábbi képernyőképen láthatóhoz hasonló kimenetet fog látni, ami azt jelzi, hogy a Blob Storage-titkosítás engedélyezve lett.

    ![Azure Resource Manager – Titkosított tárfiók](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt ismertette, hogyan használhatja a sablonreferenciát egy létező sablon testreszabására. Ebben az oktatóanyagban olyan sablont használtunk, amely csak egyetlen Azure-erőforrást tartalmaz.  A következő oktatóanyagban egy több erőforrást tartalmazó sablont fog fejleszteni.  Egyes erőforrások függő erőforrásokkal rendelkeznek.

> [!div class="nextstepaction"]
> [Több erőforrás létrehozása](./resource-manager-tutorial-create-templates-with-dependent-resources.md)

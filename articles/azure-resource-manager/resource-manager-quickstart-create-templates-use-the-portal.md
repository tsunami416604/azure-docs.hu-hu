---
title: Azure Resource Manager-sablon létrehozása és üzembe helyezése az Azure Portalon | Microsoft Docs
description: Megismerheti, hogyan hozhatja létre első Azure Resource Manager-sablonját az Azure Portalon, és hogyan helyezheti üzembe.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/11/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: c7759b9f0787b7926b3642b8b912ec5391347adf
ms.sourcegitcommit: 97d0dfb25ac23d07179b804719a454f25d1f0d46
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2019
ms.locfileid: "54911489"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Gyors útmutató: Létrehozása és üzembe helyezése Azure Resource Manager-sablonok az Azure portal használatával

Ismerje meg, hogyan hozhat létre egy Resource Manager-sablon használata az Azure Portalon, és a szerkesztési és helyezi üzembe a sablont a portálról. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. Az Azure-megoldások telepítésével és kezelésével kapcsolatos fogalmak megismeréséhez lásd: [Az Azure Resource Manager áttekintése](resource-group-overview.md).

Az oktatóanyag befejezése után üzembe helyezése egy Azure Storage-fiókot. Ugyanez a folyamat más Azure-erőforrások üzembe helyezéséhez használható.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="generate-a-template-using-the-portal"></a>Sablon generálása az Azure Portal használatával

A Resource Manager-sablon létrehozása előzmények nincs egyszerű feladat, különösen akkor, ha most ismerkedik az Azure-beli és a rendszer nem ismeri a JSON formátumban. Az Azure portal használatával, konfigurálhat egy erőforrás, például egy Azure Storage-fiókot. Mielőtt telepítené az erőforrást, exportálhatja a Resource Manager-sablon-konfigurációnak. A sablont későbbi használatra mentheti is.

Számos sablon tapasztalt fejlesztők működő sablonjainak előállítása, amikor azok nem ismeri az Azure-erőforrások üzembe helyezése meg ezt a módszert.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza az **Erőforrás létrehozása** > **Tárolás** > **Tárfiók - blob, fájl, tábla, üzenetsor** elemet.

    ![Azure-tárfiókok létrehozása az Azure Portalon](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. Adja meg a következő információkat:

    - **Erőforráscsoport**: Válassza ki **új létrehozása**, és adja meg a kiválasztott erőforráscsoport nevét. A képernyőképen az erőforráscsoport neve *mystorage1016rg*. Erőforráscsoport egy olyan tároló, az Azure-erőforrásokhoz. Erőforráscsoport megkönnyíti az Azure-erőforrások kezeléséhez.
    - **Név**: Adjon meg egy egyedi nevet a tárfiók. A képernyőképen ez a név a *mystorage1016*.

    A többi tulajdonság esetén használhatja az alapértelmezett értékeket.

    ![Azure-tárfiók konfigurációjának létrehozása az Azure Portalon](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Üzembe helyezés előtt egyes exportált sablonokat szerkeszteni szükséges.

4. Válassza a képernyő alján a **Felülvizsgálat + létrehozás** lehetőséget.
5. Válassza az **Automatizációs sablon letöltése** lehetőséget a képernyő alján. A portál megjeleníti a létrehozott sablont:

    ![Sablon generálása a Portal használatával](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    A sablon a főoldalon látható. Egy JSON-fájl hat legfelső szintű elemekkel - `schema`, `contentVersion`, `parameters`, `variables`, `resources`, és `output`. További információt az [Azure Resource Manager-sablonok struktúrája és szintaxisa](./resource-group-authoring-templates.md) című témakörben talál.

    Hat paraméter van definiálva. Az egyikük neve **storageAccountName**. Az előző képernyőképen a második kijelölt rész bemutatja, hogyan ebben a sablonban paraméterre hivatkozhatnak. A következő szakaszban úgy szerkeszti a sablont, hogy létrehozott nevet használjon a tárfiók neveként.

    A sablonban egy Azure-erőforrás van definiálva. A típusa [Microsoft.Storage/storageAccounts]. Tekintse meg az erőforrás definiálásának módját és a definíció szerkezetét.
6. Válassza a **Download** (Letöltés) lehetőséget. Mentse a **template.json** fájlt a letöltött csomagból a számítógépre. A következő szakaszban egy üzembehelyezési sablon eszközzel fogja szerkeszteni a sablont.
7. A **Paraméterek** lapon tekintheti meg a paraméterekhez megadott értékeket. Jegyezze fel ezeket az értékeket, mivel a következő szakaszban, a sablon üzembe helyezésekor szükség lesz rájuk.

    ![Sablon generálása a Portal használatával](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    A sablon és paraméterek fájlok együttes használatával, létrehozhat egy erőforrást, ebben az oktatóanyagban egy Azure storage-fiókot.

## <a name="edit-and-deploy-the-template"></a>Sablon szerkesztése és üzembe helyezése

Az Azure Portal is használható egyes alapvető szerkesztési műveletekhez. Ebben a rövid útmutatóban a portál *Template deployment* nevű eszközét használjuk. *Sablonalapú telepítés* a jelen oktatóanyagban használt, így az egész oktatóanyag egy felület – az Azure portal használatával is elvégezheti. Egy összetettebb sablon szerkesztése, érdemes lehet [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md), amely gazdagabb Szerkesztés funkciókat biztosít.

Az Azure megköveteli, hogy minden Azure-szolgáltatás egyedi névvel rendelkezzen. A központi telepítés sikertelen lehet, ha a megadott tárfiók neve, amely már létezik. A probléma elkerülése érdekében módosítsa a sablon használatához egy sablon függvény hívásához szükséges `uniquestring()` létrehozni a tárfiók egyedi neve.

1. Az Azure Portalon válassza az **Erőforrás létrehozása** lehetőséget.
2. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.
3. Válassza a **Template deployment** lehetőséget.

    ![Azure Resource Manager-sablonkönyvtár](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
4. Kattintson a **Létrehozás** gombra.
5. Válassza a **Saját sablon készítése a szerkesztőben** lehetőséget.
6. Válassza a **Fájl betöltése** lehetőséget, majd az útmutatásokat követve töltse be az előző szakaszban letöltött template.json fájlt.
7. Adjon hozzá egy változót, amint az alábbi képernyőképen látható:

    ```json
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
    ```
    ![Azure Resource Manager-sablonok](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

    Két sablonokban használható függvények használhatók: `concat()` és `uniqueString()`.

8. Távolítsa el az előző képernyőképen kiemelt **storageAccountName** paramétert.
9. Frissítse a **Microsoft.Storage/storageAccounts** erőforrás név elemét, és a paraméter helyett használja az újonnan definiált változót:

    ```json
    "name": "[variables('storageAccountName')]",
    ```

    A végső sablon így fog kinézni:

    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string"
            },
            "accountType": {
                "type": "string"
            },
            "kind": {
                "type": "string"
            },
            "accessTier": {
                "type": "string"
            },
            "supportsHttpsTrafficOnly": {
                "type": "bool"
            }
        },
        "variables": {
            "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
            {
                "name": "[variables('storageAccountName')]",
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2018-07-01",
                "location": "[parameters('location')]",
                "properties": {
                    "accessTier": "[parameters('accessTier')]",
                    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
                },
                "dependsOn": [],
                "sku": {
                    "name": "[parameters('accountType')]"
                },
                "kind": "[parameters('kind')]"
            }
        ],
        "outputs": {}
    }
    ```
7. Kattintson a **Mentés** gombra.
8. Írja be a következő értékeket:

    - **Erőforráscsoport**: válasszon **új létrehozása** és nevezze el az erőforráscsoport egyedi nevére.
    - **Hely**: válasszon egy helyet az erőforráscsoportnak. Ha például **USA középső RÉGIÓJA**. 
    - **Hely**: válasszon egy helyet a tárfióknak. Ha például **USA középső RÉGIÓJA**.
    - **Fiók típusa**: Adja meg **Standard_LRS** ebben a rövid útmutatóban.
    - **Milyen**: Adja meg **StorageV2** ebben a rövid útmutatóban.
    - **Hozzáférési szint**: Adja meg **interaktív** ebben a rövid útmutatóban.
    - **Csak HTTPS-forgalom engedélyezett**.  Ennél a rövid útmutatónál válassza a **true** (igaz) értéket.
    - **Elfogadom a fenti feltételeket és kikötéseket**: (kiválasztás)

    Az alábbi képen egy minta üzembe helyezés látható:

    ![Azure Resource Manager-sablonok üzembe helyezése](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. Válassza a **Beszerzés** lehetőséget.
11. Az üzembe helyezés állapotát úgy nézheti meg, ha a képernyő felső részén kiválasztja a harang (értesítések) ikont. Meg kell jelennie **üzembe helyezés folyamatban**. Várjon, amíg az üzembe helyezés befejeződik.

    ![Azure Resource Manager-sablonok üzembehelyezési értesítése](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. Válassza az **Ugrás az erőforráscsoportra** lehetőséget az értesítési panelen. A következőhöz hasonló képernyő jelenik meg:

    ![Azure Resource Manager-sablonok üzembehelyezési erőforráscsoportja](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Láthatja, hogy az üzembe helyezés állapota sikeres, és csak egyetlen tárfiók található az erőforráscsoportban. A tárfiók neve egy, a sablon által létrehozott egyedi sztring. Az Azure storage-fiókok használatával kapcsolatos további tudnivalókért lásd: [a rövid útmutató: Blobok feltöltése, letöltése, és listát az Azure portal használatával](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Az erőforráscsoportban megjelenik a tárfiók.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan generálható sablon az Azure Portalon, és hogyan helyezhető üzembe a sablon a Portal használatával. Ebben a rövid útmutatóban egy egyszerű sablont hoztunk létre, amelyben egyetlen Azure-erőforrás szerepel. Ha összetettebb sablonnal dolgozik, egyszerűbb a Visual Studio Code-ot vagy a Visual Studiót használni a sablon létrehozásához. A következő rövid útmutató azt is bemutatja, hogyan helyezhet üzembe sablonokat az Azure PowerShell és az Azure parancssori felület (CLI) használatával.

> [!div class="nextstepaction"]
> [Sablonok létrehozása a Visual Studio Code használatával](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)

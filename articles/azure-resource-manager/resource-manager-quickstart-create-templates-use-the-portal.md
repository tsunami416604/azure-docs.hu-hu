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
ms.date: 10/16/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: 8094105fd14ef13dd5f6b892425608806ca4dbd2
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378033"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-azure-portal"></a>Rövid útmutató: Azure Resource Manager-sablon létrehozása és üzembe helyezése az Azure Portalon

Ismerje meg, hogyan hozhatja létre első Azure Resource Manager-sablonját az Azure Portal használatával, és hogy hogyan szerkesztheti és helyezheti üzembe az Azure Portalról. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. A jelen oktatóanyag útmutatója alapján Azure Storage-fiókot fog létrehozni. Ugyanezzel a folyamattal más Azure-erőforrásokat is létre lehet hozni.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="generate-a-template-using-the-portal"></a>Sablon generálása az Azure Portal használatával

Ebben a szakaszban tárfiókot fog létrehozni az Azure Portal használatával. A tárfiók üzembe helyezése előtt lehetősége lesz ellenőrizni a sablont, amelyet a Portal az Ön konfigurációja alapján generált. A sablont későbbi használatra mentheti is.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza az **Erőforrás létrehozása** > **Tárolás** > **Tárfiók - blob, fájl, tábla, üzenetsor** elemet.

    ![Azure-tárfiókok létrehozása az Azure Portalon](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
3. Adja meg a következő információkat: 

    - **Erőforráscsoport**: hozzon létre egy új Azure-erőforráscsoportot egy Ön által választott névvel. A képernyőképen az erőforráscsoport neve *mystorage1016rg*.
    - **Név**: adjon meg egy egyedi nevet a tárfióknak. A képernyőképen ez a név a *mystorage1016*.

    A többi tulajdonság esetén használhatja az alapértelmezett értékeket.

    ![Azure-tárfiók konfigurációjának létrehozása az Azure Portalon](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Üzembe helyezés előtt egyes exportált sablonokat szerkeszteni szükséges.

4. Válassza a képernyő alján a **Felülvizsgálat + létrehozás** lehetőséget. 
5. Válassza az **Automatizációs sablon letöltése** lehetőséget a képernyő alján. A portál megjeleníti a létrehozott sablont:

    ![Sablon generálása a Portal használatával](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    A sablon a főoldalon látható. Ez egy JSON-fájl négy legfelső szintű elemmel: `schema`, `contentVersion`, `parameters` és `resources`. További információt az [Azure Resource Manager-sablonok struktúrája és szintaxisa](./resource-group-authoring-templates.md) című témakörben talál.

    Hat paraméter van definiálva. Az egyikük neve **storageAccountName**. A második kiemelt rész mutatja be, hogyan használható ez a paraméter a sablonban. A következő szakaszban úgy szerkeszti a sablont, hogy létrehozott nevet használjon a tárfiók neveként.

    A sablonban egy Azure-erőforrás van definiálva. A típusa [Microsoft.Storage/storageAccounts]. Tekintse meg az erőforrás definiálásának módját és a definíció szerkezetét.
6. Válassza a **Download** (Letöltés) lehetőséget. Mentse a **template.json** fájlt a letöltött csomagból a számítógépre. A következő szakaszban egy üzembehelyezési sablon eszközzel fogja szerkeszteni a sablont.
7. A **Paraméterek** lapon tekintheti meg a paraméterekhez megadott értékeket. Jegyezze fel ezeket az értékeket, mivel a következő szakaszban, a sablon üzembe helyezésekor szükség lesz rájuk.

    ![Sablon generálása a Portal használatával](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    A sablon és a paraméterfájl együttes használatával létrehozható egy Azure-tárfiók.

## <a name="edit-and-deploy-the-template"></a>Sablon szerkesztése és üzembe helyezése

Az Azure Portal is használható egyes alapvető szerkesztési műveletekhez. Ebben a rövid útmutatóban a portál *Template deployment* nevű eszközét használjuk. Ha összetettebb sablont szeretne szerkeszteni, érdemes a [Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md)-ot használni, amely kiterjedtebb szerkesztési funkciókat kínál.

Az Azure megköveteli, hogy minden Azure-szolgáltatás egyedi névvel rendelkezzen. Az üzembe helyezés meghiúsul, ha olyan tárfióknevet ad meg, amely már létezik. A hiba elkerülése érdekében a `uniquestring()` sablonfüggvényhívással egyedi tárfiókneveket hozhat létre.

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

    Itt két függvényt, a `concat()` és a `uniqueString()` függvényeket használjuk.

8. Távolítsa el az előző képernyőképen kiemelt **storageAccountName** paramétert.
9. Frissítse a **Microsoft.Storage/storageAccounts** erőforrás név elemét, és a paraméter helyett használja az újonnan definiált változót:

    ```json
    "name": "[variables('storageAccountName')]",
    ```

    A végső sablon így fog kinézni:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

    - **Erőforráscsoport**: adjon egy egyedi nevet az erőforráscsoportnak.
    - **Hely**: válasszon egy helyet az erőforráscsoportnak.
    - **Hely**: válasszon egy helyet a tárfióknak.  Választhatja ugyanazt a helyet is, amelyet az erőforráscsoportnál megadott.
    - **Fiók típusa**: Ennél az útmutatónál adja meg a **Standard_LRS** nevet.
    - **Típus**: Ennél a rövid útmutatónál a **StorageV2** típust adja meg.
    - **Hozzáférési szint**: Ennél a rövid útmutatónál a **Hot** (Gyors elérésű) szintet adja meg.
    - **Csak HTTPS-forgalom engedélyezett**.  Ennél a rövid útmutatónál válassza a **true** (igaz) értéket.
    - **Elfogadom a fenti feltételeket és kikötéseket**: (kiválasztás)

    Az alábbi képen egy minta üzembe helyezés látható:

    ![Azure Resource Manager-sablonok üzembe helyezése](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

10. Válassza a **Beszerzés** lehetőséget.
11. Az üzembe helyezés állapotát úgy nézheti meg, ha a képernyő felső részén kiválasztja a harang (értesítések) ikont. Várjon, amíg az üzembe helyezés befejeződik.

    ![Azure Resource Manager-sablonok üzembehelyezési értesítése](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

12. Válassza az **Ugrás az erőforráscsoportra** lehetőséget az értesítési panelen. A következőhöz hasonló képernyő jelenik meg:

    ![Azure Resource Manager-sablonok üzembehelyezési erőforráscsoportja](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Láthatja, hogy az üzembe helyezés állapota sikeres, és csak egyetlen tárfiók található az erőforráscsoportban. A tárfiók neve egy, a sablon által létrehozott egyedi sztring. Az Azure-tárfiókokkal kapcsolatos további információkért lásd: [Rövid útmutató: blobok feltöltése, letöltése és listázása az Azure Portal használatával](../storage/blobs/storage-quickstart-blobs-portal.md).

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

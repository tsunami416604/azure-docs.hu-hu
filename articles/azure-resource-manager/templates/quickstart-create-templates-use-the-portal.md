---
title: Sablon telepítése – Azure Portal
description: Megismerheti, hogyan hozhatja létre első Azure Resource Manager-sablonját az Azure Portalon, és hogyan helyezheti üzembe.
author: mumian
ms.date: 06/12/2019
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: dd3d9caa8184b8637b509fc3318851751b211405
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80131877"
---
# <a name="quickstart-create-and-deploy-arm-templates-by-using-the-azure-portal"></a>Rövid útmutató: ARM-sablonok létrehozása és üzembe helyezése az Azure Portal használatával

Ismerje meg, hogyan hozhat létre egy Azure Resource Manager (ARM) sablont az Azure Portalhasználatával, és a folyamat a sablon szerkesztése és üzembe helyezése a portálról. Arm sablonok JSON-fájlok, amelyek meghatározzák a megoldás üzembe helyezéséhez szükséges erőforrásokat. Az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmak megismeréséhez olvassa el a [sablonüzembe helyezés áttekintése című témakört.](overview.md)

![Erőforrás-kezelő sablon rövid útmutatóportál-diagramja](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Az oktatóanyag befejezése után üzembe helyez egy Azure Storage-fiókot. Ugyanez a folyamat más Azure-erőforrások üzembe helyezéséhez is használható.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="generate-a-template-using-the-portal"></a>Sablon generálása az Azure Portal használatával

Arm-sablon létrehozása a semmiből nem könnyű feladat, különösen akkor, ha az Azure-telepítés, és nem ismeri a JSON formátumot. Az Azure Portal használatával konfigurálhat egy erőforrást, például egy Azure Storage-fiókot. Az erőforrás üzembe helyezése előtt exportálhatja a konfigurációt egy sablonba. A sablont későbbi használatra mentheti is.

Számos tapasztalt sablonfejlesztő használja ezt a módszert sablonok létrehozásához, amikor olyan Azure-erőforrásokat próbálnak üzembe helyezni, amelyeket nem ismernek. A sablonok portál használatával történő exportálásáról az [Erőforráscsoportok exportálása sablonokba című](../management/manage-resource-groups-portal.md#export-resource-groups-to-templates)témakörben olvashat bővebben. A működő sablonok megkeresésének másik módja az [Azure gyorsindítási sablonjaiból származik.](https://azure.microsoft.com/resources/templates/)

1. Egy webböngészőben nyissa meg az [Azure Portalon,](https://portal.azure.com) és jelentkezzen be.
1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.

    ![Válassza az Erőforrás létrehozása az Azure Portal menüből lehetőséget](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-a-resource.png)

1. Válassza **a Storage** > **Storage fiók lehetőséget.**

    ![Azure Storage-fiók létrehozása](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-portal.png)
1. Adja meg a következő információkat:

    |Név|Érték|
    |----|----|
    |**Erőforráscsoport**|Válassza **az Új létrehozása**lehetőséget, és adja meg a kívánt erőforráscsoport nevét. A képernyőképen az erőforráscsoport neve *mystorage1016rg*. Az erőforráscsoport az Azure-erőforrások tárolója. Az erőforráscsoport megkönnyíti az Azure-erőforrások kezelését. |
    |**Név**|Adjon egyedi nevet a tárfióknak. A tárfiók nevének egyedinek kell lennie az Azure egészében, és csak kisbetűket és számokat tartalmaz. A név nek 3 és 24 karakter között kell lennie. Ha a következő hibaüzenet jelenik meg: "A tárfiók neve már foglalt", próbálja meg ** &lt;használni a nevét>tárhelyét&lt;A mai dátum az MMDD>, **például a **johndolestorage1016**. További információt az Elnevezési szabályok és korlátozások című [témakörben talál.](/azure/architecture/best-practices/resource-naming)|

    A többi tulajdonság esetén használhatja az alapértelmezett értékeket.

    ![Azure-tárfiók konfigurációjának létrehozása az Azure Portalon](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account.png)

    > [!NOTE]
    > Üzembe helyezés előtt egyes exportált sablonokat szerkeszteni szükséges.

1. Válassza a képernyő alján a **Felülvizsgálat + létrehozás** lehetőséget. A következő lépésben ne válassza a **Létrehozás** lehetőséget.
1. Válassza az **Automatizációs sablon letöltése** lehetőséget a képernyő alján. A portál megjeleníti a létrehozott sablont:

    ![Sablon generálása a Portal használatával](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template.png)

    A sablon a főoldalon látható. Ez egy JSON fájl hat legfelső `schema`szintű `contentVersion` `parameters`elemmel `resources`- `output`, , , `variables`, , és . További [információ: Az ARM sablonok szerkezetének és szintaxisának megértése](./template-syntax.md)

    Hat paraméter van definiálva. Az egyikük neve **storageAccountName**. Az előző képernyőkép második kiemelt része bemutatja, hogyan hivatkozhat erre a paraméterre a sablonban. A következő szakaszban úgy szerkeszti a sablont, hogy létrehozott nevet használjon a tárfiók neveként.

    A sablonban egy Azure-erőforrás van definiálva. A típus `Microsoft.Storage/storageAccounts`a . Vessen egy pillantást az erőforrás definiálására és a definíciós struktúrára.
1. Válassza a **Letöltés** lehetőséget a képernyő felső részén.
1. Nyissa meg a letöltött zip fájlt, majd mentse **a template.json fájlt** a számítógépre. A következő szakaszban egy üzembehelyezési sablon eszközzel fogja szerkeszteni a sablont.
1. A **Paraméterek** lapon tekintheti meg a paraméterekhez megadott értékeket. Jegyezze fel ezeket az értékeket, mivel a következő szakaszban, a sablon üzembe helyezésekor szükség lesz rájuk.

    ![Sablon generálása a Portal használatával](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-create-storage-account-template-parameters.png)

    A sablonfájl és a paraméterfájl használatával ebben az oktatóanyagban létrehozhat egy Azure-tárfiókot.

## <a name="edit-and-deploy-the-template"></a>Sablon szerkesztése és üzembe helyezése

Az Azure Portal is használható egyes alapvető szerkesztési műveletekhez. Ebben a rövid útmutatóban a portál *Template deployment* nevű eszközét használjuk. *A sablonüzembe helyezés ebben* az oktatóanyagban használatos, így a teljes oktatóanyag egyetlen felület – az Azure portal – használatával fejeződhet be. Összetettebb sablon szerkesztéséhez érdemes a [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md), amely gazdagabb szerkesztési funkciókat biztosít.

> [!IMPORTANT]
> Sablon központi telepítése egy felületet biztosít az egyszerű sablonok teszteléséhez. Nem ajánlott ezt a funkciót éles környezetben használni. Ehelyett tárolja a sablonokat egy Azure-tárfiókban, vagy egy forráskód tárházban, például a GitHubon.

Az Azure megköveteli, hogy minden Azure-szolgáltatás egyedi névvel rendelkezzen. A központi telepítés sikertelen lehet, ha olyan tárfiók nevet adott meg, amely már létezik. A probléma elkerülése érdekében módosítsa a sablont `uniquestring()` úgy, hogy sablonfüggvény-hívás használatával létrehozegy egyedi tárfióknevet.

1. Az Azure Portal menüben vagy a **kezdőlapon** válassza az **Erőforrás létrehozása**lehetőséget.
1. A **Keresés a Marketplace-en** mezőbe írja be a **template deployment** kifejezést, majd nyomja le az **ENTER** billentyűt.
1. Válassza a **Template deployment** lehetőséget.

    ![Azure Resource Manager-sablonkönyvtár](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-library.png)
1. Kattintson a **Létrehozás** gombra.
1. Válassza a **Saját sablon készítése a szerkesztőben** lehetőséget.
1. Válassza a **Fájl betöltése** lehetőséget, majd az útmutatásokat követve töltse be az előző szakaszban letöltött template.json fájlt.
1. Hajtsa végre a sablon alábbi három módosítását:

    ![Azure Resource Manager-sablonok](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-edit-storage-account-template-revised.png)

   - Távolítsa el a **storageAccountName** paramétert az előző képernyőképen látható módon.
   - Adjon hozzá egy **storageAccountName** nevű változót az előző képernyőképen látható módon:

       ```json
       "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
       ```

       Itt két sablonfüggvény `concat()` thasználja: és `uniqueString()`a .
   - Frissítse a **Microsoft.Storage/storageAccounts** erőforrás név elemét, és a paraméter helyett használja az újonnan definiált változót:

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
           "storageAccountName": "[concat(uniqueString(subscription().subscriptionId), 'storage')]"
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
1. Kattintson a **Mentés** gombra.
1. Írja be a következő értékeket:

    |Név|Érték|
    |----|----|
    |**Erőforráscsoport**|Válassza ki az utolsó szakaszban létrehozott erőforráscsoport nevét. |
    |**Helyen**|Válassza ki a tárfiók helyét. Például **az USA középső régiója**. |
    |**Fiók típusa**|Írja be **a Standard_LRS** ehhez a rövid útmutatóhoz. |
    |**Fajta**|A rövid útmutatóhoz adja meg a **StorageV2** értéket. |
    |**Hozzáférési szint**|Adja meg a **Gyorsindítás** t a rövid útmutatóhoz. |
    |**Csak https traffic engedélyezve**| Ennél a rövid útmutatónál válassza a **true** (igaz) értéket. |
    |**Elfogadom a fent említett feltételeket**|(kiválasztás)|

    Az alábbi képen egy minta üzembe helyezés látható:

    ![Azure Resource Manager-sablonok üzembe helyezése](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-deploy.png)

1. Válassza a **Beszerzés** lehetőséget.
1. Az üzembe helyezés állapotát úgy nézheti meg, ha a képernyő felső részén kiválasztja a harang (értesítések) ikont. A **telepítés folyamatban van.** Várjon, amíg az üzembe helyezés befejeződik.

    ![Azure Resource Manager-sablonok üzembehelyezési értesítése](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-notification.png)

1. Válassza az **Ugrás az erőforráscsoportra** lehetőséget az értesítési panelen. A következőhöz hasonló képernyő jelenik meg:

    ![Azure Resource Manager-sablonok üzembehelyezési erőforráscsoportja](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-template-tutorial-portal-deployment-resource-group.png)

    Láthatja, hogy az üzembe helyezés állapota sikeres, és csak egyetlen tárfiók található az erőforráscsoportban. A tárfiók neve egy, a sablon által létrehozott egyedi sztring. Az Azure-tárfiókokkal kapcsolatos további információkért lásd: [Rövid útmutató: blobok feltöltése, letöltése és listázása az Azure Portal használatával](../../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoportok** lehetőséget a bal menüben.
1. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
1. Válassza ki az erőforráscsoport nevét.  Az erőforráscsoportban megjelenik a tárfiók.
1. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan generálható sablon az Azure Portalon, és hogyan helyezhető üzembe a sablon a Portal használatával. Ebben a rövid útmutatóban egy egyszerű sablont hoztunk létre, amelyben egyetlen Azure-erőforrás szerepel. Ha összetettebb sablonnal dolgozik, egyszerűbb a Visual Studio Code-ot vagy a Visual Studiót használni a sablon létrehozásához. Ha többet szeretne megtudni a sablonfejlesztésről, tekintse meg az új kezdő oktatósorozatunkat:

> [!div class="nextstepaction"]
> [Kezdő szintű oktatóanyagok](./template-tutorial-create-first-template.md)
---
title: A sablonreferencia felhasználása
description: Használja az Azure Resource Manager sablon hivatkozás sablon létrehozásához.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b742982121a20a2b057eba4211584b0386dde411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373427"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Oktatóanyag: Használja ki az ARM sablon hivatkozását

Ismerje meg, hogyan található meg a sablonséma-információk, és az információk segítségével hozzon létre Az Azure Resource Manager (ARM) sablonokat.

Ebben az oktatóanyagban egy alapszintű sablont fog használni az Azure-gyorssablonok közül. A sablon referenciadokumentációjának használatával testreszabhatja a sablont.

![Az Erőforrás-kezelő sablonhivatkozás a tárfiók telepítése](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * Gyorsindítási sablon megnyitása
> * A sablon ismertetése
> * A sablonreferencia megkeresése
> * A sablon szerkesztése
> * A sablon üzembe helyezése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio CodeResource Manager Tools bővítménnyel. Lásd: [A Visual Studio-kód használata ARM-sablonok létrehozásához.](use-vs-code-to-create-template.md)

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

[Az Azure quickstart sablonok](https://azure.microsoft.com/resources/templates/) egy tárház ARM sablonok. Teljesen új sablon létrehozása helyett kereshet egy mintasablont, és testre szabhatja azt. Az ebben a rövid útmutatóban használt sablon neve a következő: [Standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/). A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio-kódból válassza a **Fájlmegnyitása**>**fájl**lehetőséget.
1. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.
1. Válassza a **Fájlmentés**>**másként** lehetőséget a fájl **azuredeploy.json néven** a helyi számítógépre történő mentéséhez.

## <a name="understand-the-schema"></a>A séma bemutatása

1. A VS Code-ban csukja össze a sablont a gyökérszintig. Az ekkor előálló legegyszerűbb struktúra a következő elemeket tartalmazza:

    ![Resource Manager-sablon – Legegyszerűbb struktúra](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: adja meg a sablon nyelvének verziószámát tartalmazó JSON-sémafájl helyét.
    * **contentVersion**: adjon meg egy tetszőleges értéket ehhez az elemhez a sablon lényeges módosításainak dokumentálásához.
    * **parameters**: adja meg az üzembe helyezéskor beállított értékeket az erőforrás üzembe helyezésének testreszabásához.
    * **variables**: adja meg a sablonban JSON-töredékekként használt értékeket a sablonnyelvi kifejezések leegyszerűsítéséhez.
    * **resources**: adja meg az erőforráscsoportban üzembe helyezett vagy frissített erőforrástípusokat.
    * **outputs**: adja meg az üzembe helyezés után visszaadott értékeket.

1. Bontsa ki a **resources** elemet. Itt `Microsoft.Storage/storageAccounts` nevű erőforrás van meghatározva.

    ![Resource Manager-sablon, tárfiók-definíció](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

## <a name="find-the-template-reference"></a>A sablonreferencia megkeresése

1. Tallózással keresse meg [az Azure-sablon hivatkozását.](https://docs.microsoft.com/azure/templates/)
1. A **Szűrés cím** szerint mezőbe írja be a **tárfiókokat,** és válassza ki az első **tárfiókokat** **> tárház csoportban.**

    ![Resource Manager-sablonreferencia – tárfiók](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Az erőforrás-szolgáltató általában több API-verzióval rendelkezik:

    ![Az Erőforrás-kezelő sablonja hivatkozási tárfiók verzióira](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. A bal oldali ablaktáblából válassza a **Tárolás** csoport **összes erőforrása** jelölőnégyzetét. Ez a lap a tárolóerőforrás-szolgáltató erőforrástípusait és verzióit sorolja fel. Javasoljuk, hogy a legújabb API-verziók a sablonban definiált erőforrástípusok.

    ![Az Erőforrás-kezelő sablonhivatkozási tárfiók-típusok verziói](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Válassza ki a **storageAccount** erőforrástípus legújabb verzióját.  A legújabb verzió **a 2019-06-01,** amikor ez a cikk meg van írva.

1. Ez a lap a storageAccount erőforrástípus részleteit sorolja fel.  Például felsorolja a **Sku** objektum megengedett értékeit. Több skus van, mint ami a korábban megnyitott rövid útmutató sablonban szerepel. Testreszabhatja a rövid útmutató sablont, hogy tartalmazza az összes rendelkezésre álló tárolási típust.

    ![Az Erőforrás-kezelő sablon hivatkozási tárfiók-skus](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>A sablon szerkesztése

A Visual Studio-kódból adja hozzá a további tárfióktípusokat az alábbi képernyőképen látható módon:

![Erőforrás-kezelő sablontárfiók-erőforrások](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A Visual Studio Code üzembehelyezési eljárásról szóló rövid útmutatójában tekintse meg [A sablon üzembe helyezése](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) című szakaszt. A sablon telepítésekor adja meg a **storageAccountType** paramétert egy újonnan hozzáadott értékkel, például **Premium_ZRS**. A telepítés sikertelen lenne, ha az eredeti gyorsindítási sablont **használja,** mert Premium_ZRS nem engedélyezett érték.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza a bal oldali menü **Erőforráscsoport** lehetőséget.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. Válassza a felső menü **Erőforráscsoport törlése** parancsát.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag azt ismertette, hogyan használhatja a sablonreferenciát egy létező sablon testreszabására. Több tárfiókpéldány létrehozása:

> [!div class="nextstepaction"]
> [Több példány létrehozása](./template-tutorial-create-multiple-instances.md)

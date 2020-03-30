---
title: Sablon telepítése - IntelliJ IDEA
description: Ismerje meg, hogyan hozhatja létre az első Azure Resource Manager-sablont az IntelliJ IDEA használatával, és hogyan telepítheti azt.
ms.devlang: java
ms.date: 08/01/2019
ms.topic: conceptual
ms.openlocfilehash: a5015a018f905b4353afd6bf25a48a2d942b3b97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153352"
---
# <a name="create-and-deploy-arm-templates-by-using-the-intellij-idea"></a>Arm-sablonok létrehozása és üzembe helyezése az IntelliJ IDEA használatával

Ismerje meg, hogyan telepíthet egy Azure Resource Manager (ARM) sablont az Azure-ba az IntelliJ IDEA használatával, és hogyan szerkesztheti és frissítheti a sablont közvetlenül az IDE-ből. Arm sablonok JSON-fájlok, amelyek meghatározzák a megoldás üzembe helyezéséhez szükséges erőforrásokat. Az Azure-megoldások üzembe helyezésével és kezelésével kapcsolatos fogalmak megismeréséhez olvassa el a [sablonüzembe helyezés áttekintése című témakört.](overview.md)

![Erőforrás-kezelő sablonportáljának diagramja](./media/quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Az oktatóanyag befejezése után üzembe helyez egy Azure Storage-fiókot. Ugyanez a folyamat más Azure-erőforrások üzembe helyezéséhez is használható.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Telepített [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition vagy Community Edition
* Telepített [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053), további információért lásd az [IntelliJ beépülő moduljainak felügyeleti útmutatóját](https://www.jetbrains.com/help/idea/managing-plugins.html)
* [Legyen bejelentkezve](/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) az Azure-fiókjába az Azure Toolkit for IntelliJ eléréséhez

## <a name="deploy-a-quickstart-template"></a>Rövid útmutató sablon telepítése

Teljesen új sablon létrehozása helyett megnyithat egy sablont az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) közül. Az Azure quickstart sablonok egy tárház ARM sablonok. A cikkben használt sablon neve [Normál tárfiók létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/). Egy Azure Storage-fiók erőforrás határozza meg.

1. Kattintson a jobb [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) gombbal, és mentse a és a helyi számítógépre.

1. Ha az Azure Toolkit megfelelően telepítve van, és bejelentkezett, az Azure Explorer az IntelliJ IDEA oldalsávján. Kattintson a jobb gombbal az **Erőforrás-gazdálkodásra,** és válassza **a Telepítés létrehozása parancsot.**

    ![Az Erőforrás-kezelő sablon jobb gombbal a telepítés létrehozásához](./media/create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Adja meg a **központi telepítés nevét,** **az előfizetést,** az **erőforráscsoportot**és **a régiót.** Itt telepítjük a sablont `testRG`egy új erőforráscsoportba. Ezután válassza ki az `azuredeploy.json` elérési utat az **erőforrássablonhoz** és **az erőforrás-paraméterekhez** a letöltés közben. `azuredeploy.parameters.json`

    ![Az Erőforrás-kezelő sablon kijelöli a központi telepítéslétrehozásához szükséges fájlokat](./media/create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Miután az OK gombra kattintott, a központi telepítés elindul. A míg a telepítés befejezéséig, megtalálja a haladást IntelliJ IDEA **állapotsor** alján.

    ![Az Erőforrás-kezelő sablon telepítési állapota](./media/create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Meglévő központi telepítés tallózása

1. A központi telepítés befejezése után láthatja `testRG` az új erőforráscsoportot és egy új központi telepítést. Kattintson a jobb gombbal a központi telepítésre, és megtekintheti a lehetséges műveletek listáját. Most válassza **a Tulajdonságok megjelenítése**lehetőséget.

    ![Az Erőforrás-kezelő sablon tallózhat a központi telepítésben](./media/create-templates-use-intellij/resource-manager-deployment-browse.png)

1. A lapnézet meg nyílik, hogy megjelenítsen néhány hasznos tulajdonságot, például a központi telepítés állapotát és a sablonszerkezetet.

    ![Az Erőforrás-kezelő sablon jattot jelenít meg a központi telepítés tulajdonságairól](./media/create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Meglévő központi telepítés szerkesztése és frissítése

1. Válassza a **Telepítés szerkesztése parancsot** a jobb gombbal elérhető menüben vagy a megjelenítési tulajdonságok nézetben. Egy másik lap nézet nyílik meg, amely bemutatja a sablon és a paraméter fájlokat az Azure-ban való üzembe helyezés. Ha helyi re kattintva szeretné menteni a fájlokat, kattintson a **Sablonfájl exportálása** vagy **a Paraméterfájlok exportálása gombra.**

    ![Erőforrás-kezelő sablon szerkesztése központi telepítés](./media/create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Ezen a lapon szerkesztheti a két fájlt, és telepítheti a módosításokat az Azure-ba. Itt módosítjuk a **storageAccountType** értékét a `Standard_LRS` `Standard_GRS`paraméterfájlokban, a -tól a -ig Ezután kattintson a **Telepítés frissítése** alján, és erősítse meg a frissítést.

    ![Erőforrás-kezelő sablon szerkesztése központi telepítés](./media/create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. A frissítés telepítésének befejezése után a portálon ellenőrizheti, `Standard_GRS`hogy a létrehozott tárfiók megváltozott-e .

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Ezt az Azure Portalon vagy az Azure CLI-n teheti meg. Az Azure Explorer ben az IntelliJ IDEA, kattintson a jobb gombbal a létrehozott **erőforráscsoport,** és válassza a törlés.

    ![Erőforráscsoport törlése az Azure Explorerben az IntelliJ IDEA szolgáltatásból](./media/create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Figyelje meg, hogy a központi telepítés törlése nem törli a központi telepítés által létrehozott erőforrásokat. Törölje a megfelelő erőforráscsoportot vagy adott erőforrásokat, ha már nincs rájuk szüksége.

## <a name="next-steps"></a>További lépések

A cikk fő célja, hogy az IntelliJ IDEA használatával telepítsen egy meglévő sablont az Azure gyorsútmutató-sablonjaiból. Azt is megtanulta, hogyan tekintheti meg és frissítheti az Azure-beli meglévő központi telepítést. Az Azure-gyorssablonok nem biztos, hogy minden tekintetben megfelelnek Önnek. Ha többet szeretne megtudni a sablonfejlesztésről, tekintse meg az új kezdő oktatósorozatunkat:

> [!div class="nextstepaction"]
> [Kezdő szintű oktatóanyagok](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Látogasson el a Java alkalmazásra az Azure fejlesztői központban](https://docs.microsoft.com/azure/java)

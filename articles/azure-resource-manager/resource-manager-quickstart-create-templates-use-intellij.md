---
title: Azure Resource Manager sablon üzembe helyezése – IntelliJ IDEA
description: Megtudhatja, hogyan hozhatja létre első Azure Resource Manager-sablonját a IntelliJ IDEA használatával, és hogyan helyezheti üzembe.
services: azure-resource-manager
documentationcenter: ''
author: yucwan
manager: ''
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: java
ms.date: 08/01/2019
ms.topic: quickstart
ms.author: yucwan
ms.openlocfilehash: 137a87ad0314fad08f9af0eb567e1d31032ddcb9
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72533659"
---
# <a name="quickstart-create-and-deploy-azure-resource-manager-templates-by-using-the-intellij-idea"></a>Rövid útmutató: Azure Resource Manager-sablonok létrehozása és telepítése a IntelliJ IDEA használatával

Ismerje meg, hogyan helyezhet üzembe egy Resource Manager-sablont az Azure-ban a IntelliJ IDEA használatával, valamint a sablon szerkesztésének és frissítésének folyamatát közvetlenül az IDE-ből. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. Az Azure-megoldások telepítésével és kezelésével kapcsolatos fogalmak megismeréséhez lásd: [Az Azure Resource Manager áttekintése](resource-group-overview.md).

![Resource Manager-sablon – rövid útmutató portál](./media/resource-manager-quickstart-create-templates-use-the-portal/azure-resource-manager-export-deploy-template-portal.png)

Az oktatóanyag befejezése után üzembe kell helyeznie egy Azure Storage-fiókot. Ugyanez a folyamat használható más Azure-erőforrások üzembe helyezésére is.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Az [INTELLIJ Idea](https://www.jetbrains.com/idea/download/) Ultimate Edition vagy a Community Edition telepítve van
* A [Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053) telepítve van, tekintse [meg a IntelliJ beépülő modul kezelési útmutatóját](https://www.jetbrains.com/help/idea/managing-plugins.html) . További információ
* [Jelentkezzen](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-sign-in-instructions) be az Azure-fiókjába a Azure Toolkit for IntelliJ

## <a name="deploy-a-quickstart-template"></a>Gyorsindítás sablon üzembe helyezése

Teljesen új sablon létrehozása helyett megnyithat egy sablont az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) közül. Az Azure Gyorsindítás sablonjai a Resource Manager-sablonok tárháza. Az ebben a rövid útmutatóban használt sablon [standard szintű tárfiók létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-storage-account-create/) néven található meg. Egy Azure Storage-fiók erőforrását határozza meg.

1. Kattintson a jobb gombbal, és mentse a [`azuredeploy.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) és [`azuredeploy.parameters.json`](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json) beállítást a helyi számítógépre.

1. Ha az Azure-eszközkészlet megfelelően van telepítve és bejelentkezve, az Azure Explorer az IntelliJ IDEA oldalsávjában jelenik meg. Kattintson a jobb gombbal az **Erőforrás-kezelés** elemre, és válassza a **központi telepítés létrehozása**elemet.

    ![Resource Manager-sablon – kattintson a jobb gombbal az üzembe helyezés létrehozásához](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-right-click.png)

1. Adja meg a **központi telepítési nevet**, az **előfizetést**, az **erőforráscsoportot**és a **régiót**. Itt telepítjük a sablont egy új erőforráscsoport-`testRG`ba. Ezután válassza ki az **erőforrás-sablon** elérési útját `azuredeploy.json` és **erőforrás-paraméterekként** , ahogy `azuredeploy.parameters.json` letöltötte.

    ![Resource Manager-sablon a központi telepítés létrehozására szolgáló fájlok kiválasztása](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-select-files.png)

1. Miután rákattintott az OK gombra, a rendszer elindítja a központi telepítést. Az üzembe helyezés befejezéséig a IntelliJ ötlet alján található **állapotjelző sáv** előrehaladása látható.

    ![Resource Manager-sablon telepítési állapota](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-create-deployment-status.png)

## <a name="browse-an-existing-deployment"></a>Meglévő üzemelő példány tallózása

1. Az üzembe helyezés után megtekintheti az új erőforráscsoportot `testRG` és egy új üzemelő példányt. Kattintson a jobb gombbal a központi telepítésre, és megtekintheti a lehetséges műveletek listáját. Most válassza a **Tulajdonságok megjelenítése**lehetőséget.

    ![Resource Manager-sablon – Tallózás központi telepítés](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-browse.png)

1. Ekkor megnyílik egy lap nézet, amely néhány hasznos tulajdonságot, például a telepítési állapotot és a sablon szerkezetét jeleníti meg.

    ![Resource Manager-sablon a központi telepítési tulajdonságokat jeleníti meg](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-deployment-show-properties.png)

## <a name="edit-and-update-an-existing-deployment"></a>Meglévő üzemelő példány szerkesztése és frissítése

1. Válassza a **központi telepítés szerkesztése** lehetőséget a jobb gombbal, vagy a Tulajdonságok megjelenítése nézetet előtt. Megnyílik egy másik lap nézet, amely megjeleníti az Azure-beli üzembe helyezés sablonját és paramétereit. Ha a fájlokat a helyi fájlba szeretné menteni, kattintson a **sablonfájl exportálása** vagy a **paraméterérték exportálása**lehetőségre.

    ![Resource Manager-sablon – telepítés szerkesztése](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment.png)

1. Ezen a lapon szerkesztheti a két fájlt, és üzembe helyezheti az Azure-ban történt módosításokat. Itt módosítjuk a **tárfióktípus** értékét a `Standard_LRS`tól a `Standard_GRS`ig. Ezután kattintson az alul lévő **központi telepítés frissítése** elemre, és erősítse meg a frissítést.

    ![Resource Manager-sablon – telepítés szerkesztése](./media/resource-manager-quickstart-create-templates-use-intellij/resource-manager-edit-deployment-update.png)

1. A frissítés telepítésének befejezése után ellenőrizheti a portálon, hogy a létrehozott Storage-fiók `Standard_GRS`re módosult-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. Ezt elvégezheti Azure Portal vagy az Azure CLI-vel. Az Azure Explorerben a IntelliJ ÖTLETből kattintson a jobb gombbal a létrehozott **erőforráscsoport** elemre, és válassza a Törlés lehetőséget.

    ![Erőforráscsoport törlése az Azure Explorerben a IntelliJ IDEA-ből](./media/resource-manager-quickstart-create-templates-use-intellij/delete-resource-group.png)

> [!NOTE]
> Figyelje meg, hogy a központi telepítés törlése nem törli az üzemelő példány által létrehozott erőforrásokat. Ha már nincs szüksége rájuk, törölje a megfelelő erőforráscsoportot vagy adott erőforrásokat.

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a fő témája a IntelliJ IDEA használata egy meglévő sablon üzembe helyezéséhez az Azure Gyorsindítás sablonjaiból. Azt is megtanulta, hogyan tekintheti meg és frissítheti a meglévő üzembe helyezést az Azure-ban. Az Azure-gyorssablonok nem biztos, hogy minden tekintetben megfelelnek Önnek. A sablonok fejlesztésével kapcsolatos további tudnivalókért tekintse meg az új kezdő oktatóanyag-sorozatot:

> [!div class="nextstepaction"]
> [Kezdő oktatóanyagok](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [A Java megnyitása az Azure fejlesztői központban](https://docs.microsoft.com/azure/java)

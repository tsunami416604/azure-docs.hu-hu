---
title: Azure Resource Manager-sablon létrehozása a Visual Studio Code használatával | Microsoft Docs
description: A Resource Manager-sablonokban Azure Resource Manager Tools-bővítményekkel dolgozhat.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 07/17/2018
ms.topic: quickstart
ms.author: jgao
ms.openlocfilehash: a66d845fcf7507613cda475bbc96225f8a7cc7eb
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126851"
---
# <a name="quickstart-create-azure-resource-manager-templates-by-using-visual-studio-code"></a>Rövid útmutató: Azure Resource Manager-sablonok létrehozása a Visual Studio Code használatával

Útmutató Azure Resource Manager-sablonok létrehozásához a Visual Studio Code és az Azure Resource Manager Tools bővítmény használatával. Resource Manager-sablonokat a Visual Studio Code-ban a bővítmény nélkül is létrehozhat, a bővítmény azonban rendelkezik olyan automatikus kiegészítési szolgáltatásokkal, amelyek megkönnyítik a sablonok fejlesztését. Az Azure-megoldások telepítésével és kezelésével kapcsolatos fogalmak megismeréséhez lásd: [Az Azure Resource Manager áttekintése](resource-group-overview.md).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

- [Visual Studio Code](https://code.visualstudio.com/).
- A Resource Manager Tools bővítmény. A telepítéshez kövesse az alábbi lépéseket:

    1. Nyissa meg a Visual Studio Code-ot.
    2. A **CTRL+SHIFT+X** billentyűkombinációval nyissa meg a Bővítmények ablaktáblát
    3. Keresse meg az **Azure Resource Manager Tools** elemet, és válassza a **Telepítés** lehetőséget.
    4. A bővítmény telepítésének befejezéséhez válassza az **Újrabetöltés** lehetőséget.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Teljesen új sablon létrehozása helyett megnyithat egy sablont az [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/) közül. Az Azure gyorsindítási sablonok a Resource Manager-sablonok tárházaként szolgálnak.

Az ebben a rövid útmutatóban használt sablon neve a következő: [Standard szintű tárfiók létrehozása](https://azure.microsoft.com/resources/templates/101-storage-account-create/). A sablon egy Azure Storage-fiókhoz tartozó erőforrást határoz meg.

1. A Visual Studio Code-ban válassza a **Fájl**>**Fájl megnyitása** elemet.
2. A **Fájlnév** mezőbe illessze be a következő URL-t:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. A **Megnyitás** elem kiválasztásával nyissa meg a fájlt.
4. A **Fájl**>**Mentés másként** elem kiválasztásával mentse a fájlt a helyi számítógépre **azuredeploy.json** néven.

## <a name="edit-the-template"></a>A sablon szerkesztése

A Visual Studio Code-dal történő sablonszerkesztés megismeréséhez egy további elemet kell felvenni a kimeneti szakaszba.

1. Egy további kimenet felvétele az exportált sablonba a Visual Studio Code használatával:

    ```json
    "storageUri": {
      "type": "string",
      "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
    }
    ```

    Ha elkészült, a kimeneti szakasz az alábbihoz hasonlóan néz ki:

    ```json
    "outputs": {
      "storageAccountName": {
        "type": "string",
        "value": "[variables('storageAccountName')]"
      },
      "storageUri": {
        "type": "string",
        "value": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

    Ha másolta és beillesztette a kódot a Visual Studio Code-ban, a **value** elem újbóli beírásával tapasztalhatja meg a Resource Manager Tools bővítmény IntelliSense képességét.

    ![IntelliSense a Visual Studio Code-ban egy Resource Manager-sablon használatakor](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/resource-manager-templates-visual-studio-code-intellisense.png)

2. A fájl mentéséhez válassza a **Fájl**>**Mentés** lehetőséget.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablonok üzembe helyezésének számos módszere van.  Ebben a rövid útmutatóban az Azure Portal Cloud Shell szolgáltatását használja. A Cloud Shell az Azure CLI és az Azure PowerShell szolgáltatást is támogatja. Az itt található utasítások a CLI használatára vonatkoznak.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) webhelyre
2. Válassza ki a jobb felső sarokban a **Cloud Shell** elemet az alábbi képen látható módon:

    ![Az Azure Portal Cloud Shell szolgáltatása](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell.png)

3. Válassza a lefelé mutató nyilat, majd a **Bash** elem kiválasztásával váltson át a PowerShellből a parancssori felületre.

    ![Az Azure Portal Cloud Shell CLI szolgáltatása](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-choose-cli.png)
4. Az **Újraindítás** elem kiválasztásával indítsa újra a felületet.
5. Válassza a **Fájlok feltöltése/letöltése**, majd a **Feltöltés** elemet.

    ![Fájl feltöltése az Azure Portal Cloud Shell szolgáltatásával](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-upload-file.png)
4. Válassza az e rövid útmutatóban korábban mentett fájlt. Alapértelmezés szerint a fájl neve a következő: **azuredeploy.json**.
5. A Cloud Shell szolgáltatásban az **ls** parancs futtatásával ellenőrizheti, hogy a fájl feltöltése sikeresen megtörtént-e. A **cat** paranccsal pedig a sablon tartalmát ellenőrizheti.

    ![Fájl listázása az Azure Portal Cloud Shell szolgáltatásával](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-list-file.png)
6. Futtassa az alábbi parancsokat a Cloud Shellben:

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file <TemplateFileName>
    ```
    Az alábbi képernyőképen egy üzembe helyezési minta látható:

    ![Sablon üzembe helyezése az Azure Portal Cloud Shell szolgáltatásával](./media/resource-manager-quickstart-create-templates-use-visual-studio-code/azure-portal-cloud-shell-deploy-template.png)

    A képernyőképen az alábbi értékek láthatók:

    - **&lt;ResourceGroupName>**: myresourcegroup0709. A paraméter két helyen jelenik meg.  Ellenőrizze, hogy ugyanezt az értéket használja-e.
    - **&lt;AzureLocation>**: eastus2
    - **&lt;DeployName>**: mydeployment0709
    - **&lt;TemplateFile>**: azuredeploy.json

    A képernyőképen a tárfiók neve a következő: *3tqebj3slyfyestandardsa*. 

7. Az alábbi PowerShell-parancs futtatásával listázhatja az újonnan létrehozott tárfiókot:

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell megjelennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozható létre sablon a Visual Studio Code használatával, és hogyan helyezhető üzembe a sablon az Azure Portal Cloud Shell szolgáltatással. Ebben a rövid útmutatóban egy olyan sablont hoztunk létre, amelyben csak egyetlen Azure-erőforrás szerepel.  A következő oktatóanyagban több erőforrást tartalmazó sablont fog készíteni.  Egyes erőforrások függő erőforrásokkal rendelkeznek.

> [!div class="nextstepaction"]
> [Sablonok létrehozása a Visual Studio használatával](./vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

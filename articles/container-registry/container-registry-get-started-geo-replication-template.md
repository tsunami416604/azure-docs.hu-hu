---
title: Rövid útmutató – földrajzilag replikált beállításjegyzék létrehozása – Resource Manager-sablon
description: Megtudhatja, hogyan hozhat létre földrajzilag replikált Azure Container registryt Azure Resource Manager sablon használatával.
services: azure-resource-manager
author: dlepow
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: danlep
ms.date: 05/26/2020
ms.openlocfilehash: 1345cc67137a4fb3b6d54443e71f1a8813b5b06c
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84122813"
---
# <a name="quickstart-create-a-geo-replicated-container-registry-by-using-a-resource-manager-template"></a>Gyors útmutató: földrajzilag replikált tároló-beállításjegyzék létrehozása Resource Manager-sablon használatával

Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Container Registry-példányt egy Azure Resource Manager sablon használatával. A sablon egy [földrajzilag replikált](container-registry-geo-replication.md) beállításjegyzéket állít be, amely automatikusan szinkronizálja a beállításjegyzék tartalmát több Azure-régión belül. A Geo-replikáció lehetővé teszi, hogy a hálózatról a helyi környezetből származó rendszerképekhez hozzáférjenek, miközben egyetlen felügyeleti élményt biztosítanak. Ez a [prémium](container-registry-skus.md) szintű beállításjegyzék szolgáltatási szintjének egyik funkciója. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

Nincsenek.

## <a name="create-a-geo-replicated-registry"></a>Földrajzilag replikált beállításjegyzék létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-container-registry-geo-replication/)származik. A sablon egy beállításjegyzéket és egy további regionális replikát állít be.

:::code language="json" source="~/quickstart-templates/101-container-registry-geo-replication/azuredeploy.json" range="1-81" highlight="45-74" :::

A következő erőforrások vannak definiálva a sablonban:

* **[Microsoft. ContainerRegistry/nyilvántartások](/azure/templates/microsoft.containerregistry/registries)**: Azure Container Registry létrehozása
* **[Microsoft. ContainerRegistry/beállításjegyzékek/replikálások](/azure/templates/microsoft.containerregistry/registries/replications)**: tároló beállításjegyzék-replikájának létrehozása

További Azure Container Registry-sablonok a gyorsindítási [sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerregistry&pageNumber=1&sort=Popular)katalógusában találhatók.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

 1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-container-registry-geo-replication%2Fazuredeploy.json)

 2. Válassza ki vagy adja meg a következő értékeket.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása**lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK gombra**.
    * **Hely**: válasszon egy helyet az erőforráscsoportnak. Példa: **USA középső**régiója.
    * **ACR neve**: fogadja el a beállításjegyzék generált nevét, vagy adjon meg egy nevet. Globálisan egyedinek kell lennie.
    * **Hely**: fogadja el a beállításjegyzék Kezdőlap replikájának létrehozott helyét, vagy adjon meg egy helyet, például az **USA középső**régióját. 
    * **ACR-replika helye**: adjon meg egy helyet a beállításjegyzék-replika számára a régió rövid nevének használatával. Ettől eltérőnek kell lennie a Kezdőlap beállításjegyzékének helyétől. Példa: **westeurope**.
    * **Elfogadom a fenti feltételeket és kikötéseket**: Select.

        :::image type="content" source="media/container-registry-get-started-geo-replication-template/template-properties.png" alt-text="Sablon tulajdonságai":::

 3. Ha elfogadja a használati feltételeket, válassza a **vásárlás**lehetőséget. A beállításjegyzék sikeres létrehozása után értesítést kap:

     :::image type="content" source="media/container-registry-get-started-geo-replication-template/deployment-notification.png" alt-text="Portál értesítése":::

 A Azure Portal a sablon üzembe helyezéséhez használható. A Azure Portalon kívül a Azure PowerShell, az Azure CLI és a REST API is használható. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A tároló-beállításjegyzék tulajdonságainak áttekintéséhez használja a Azure Portal vagy egy eszközt, például az Azure CLI-t.

1. A portálon keressen a tároló-beállításjegyzékek kifejezésre, és válassza ki a létrehozott tároló-beállításjegyzéket.

1. Az **Áttekintés** lapon jegyezze fel a beállításjegyzék **bejelentkezési kiszolgálóját** . Akkor használja ezt az URI-t, ha a Docker használatával címkézi és leküldi a lemezképeket a beállításjegyzékbe. További információ: az [első rendszerkép leküldése a Docker CLI használatával](container-registry-get-started-docker-cli.md).

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-overview.png" alt-text="A beállításjegyzék áttekintése":::

1. A **replikálások** lapon erősítse meg a Kezdőlap replika és a sablonon keresztül hozzáadott replika helyét. Ha kívánja, adjon hozzá további replikákat ezen a lapon.

    :::image type="content" source="media/container-registry-get-started-geo-replication-template/registry-replications.png" alt-text="Beállításjegyzék-replikálások":::

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs szüksége rájuk, törölje az erőforráscsoportot, a beállításjegyzéket és a beállításjegyzék-replikát. Ehhez nyissa meg a Azure Portal, válassza ki a beállításjegyzéket tartalmazó erőforráscsoportot, majd válassza az **erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container Registry egy Resource Manager-sablonnal, és konfigurált egy beállításjegyzék-replikát egy másik helyen. Folytassa a Azure Container Registry oktatóanyagokkal, és tekintse meg az ACR mélyebb megjelenését.

> [!div class="nextstepaction"]
> [Oktatóanyagok Azure Container Registry](container-registry-tutorial-prepare-registry.md)

A sablonok létrehozásának folyamatát ismertető, lépésenkénti oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [Oktatóanyag: az első Azure Resource Manager-sablon létrehozása és üzembe helyezése](/azure/azure-resource-manager/templates/template-tutorial-create-first-template.md)
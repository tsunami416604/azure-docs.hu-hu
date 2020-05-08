---
title: Rövid útmutató – földrajzilag replikált beállításjegyzék létrehozása – Resource Manager-sablon
description: Ebben a rövid útmutatóban egy Azure Resource Manager sablon használatával gyorsan üzembe helyezhet egy elkülönített Azure Container-példányon futó tároló-webalkalmazást.
services: azure-resource-manager
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/30/2020
ms.openlocfilehash: f9f789d2f2c6c21e38f241b445c72b330c689a8d
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930420"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-a-resource-manager-template"></a>Gyors útmutató: tároló-példány üzembe helyezése az Azure-ban Resource Manager-sablon használatával

A Azure Container Instances használatával a kiszolgáló nélküli Docker-tárolókat az Azure-ban, az egyszerűség és a gyorsaság segítségével futtathatja. Igény szerint üzembe helyezhet egy alkalmazást egy tároló-példányon, ha nincs szüksége a teljes Container-előkészítési platformra, például az Azure Kubernetes szolgáltatásra.

Ebben a rövid útmutatóban egy Azure Resource Manager sablont használ egy elkülönített Docker-tároló üzembe helyezéséhez és a webalkalmazás nyilvános IP-címmel való elérhetővé tételéhez. 

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

Nincsenek.

## <a name="create-a-container-instance"></a>Tárolópéldány létrehozása

### <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-linuxcontainer-public-ip)származik.

[!code-json[<Azure Resource Manager template create geo-replicated registry>](~/quickstart-templates/101-aci-linuxcontainer-public-ip/azuredeploy.json)]

A következő erőforrás van definiálva a sablonban:

* **[Microsoft. ContainerInstance/containerGroups](/azure/templates/microsoft.containerinstance/containergroups)**: hozzon létre egy Azure Container-csoportot. Ez a sablon egyetlen tároló példányból álló csoportot határoz meg.

További Azure Container Instances-sablonok a gyorsindítási [sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Containerinstance&pageNumber=1&sort=Popular)katalógusában találhatók.

### <a name="deploy-the-template"></a>A sablon üzembe helyezése

 1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy beállításjegyzéket és egy replikát egy másik helyen.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aci-linuxcontainer-public-ip%2Fazuredeploy.json)

 2. Válassza ki vagy adja meg a következő értékeket.

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása**lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK gombra**.
    * **Hely**: válasszon egy helyet az erőforráscsoportnak. Példa: **USA középső**régiója.
    * **Név**: fogadja el a példány generált nevét, vagy adjon meg egy nevet.
    * **Rendszerkép**: fogadja el az alapértelmezett rendszerkép nevét. Ez a minta linuxos rendszerkép egy, a Node. js-ben írt kisméretű webalkalmazást csomagol, amely egy statikus HTML-oldalt szolgál ki. 

    Fogadja el az alapértelmezett értékeket a többi tulajdonsághoz.

    Tekintse át a használati feltételeket. Ha elfogadja, jelölje be **az Elfogadom a fenti feltételeket és kikötéseket**.

    ![Sablon tulajdonságai](media/container-instances-quickstart-template/template-properties.png)

 3. A példány sikeres létrehozása után értesítést kap:

    ![Portál értesítése](media/container-instances-quickstart-template/deployment-notification.png)

 A Azure Portal a sablon üzembe helyezéséhez használható. A Azure Portalon kívül a Azure PowerShell, az Azure CLI és a REST API is használható. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../azure-resource-manager/templates/deploy-cli.md).

## <a name="review-deployed-resources"></a>Üzembe helyezett erőforrások áttekintése

A tároló példány tulajdonságainak áttekintéséhez használja a Azure Portal vagy egy eszközt, például az [Azure CLI](container-instances-quickstart.md) -t.

1. A portálon keressen rá a Container Instancesra, és válassza ki a létrehozott Container-példányt.

1. Az **Áttekintés** lapon jegyezze fel a példány **állapotát** és az **IP-címét**.

    ![Példányok áttekintése](media/container-instances-quickstart-template/aci-overview.png)

2. Miután az állapota *fut*, navigáljon az IP-címre a böngészőben. 

    ![Az Azure Container Instances használatával üzembe helyezett alkalmazás képe a böngészőben](media/container-instances-quickstart-template/view-application-running-in-an-azure-container-instance.png)

### <a name="view-container-logs"></a>Tárolónaplók megtekintése

A tárolópéldányok naplóinak megtekintése hasznos lehet a tárolóval vagy az azon futtatott alkalmazással kapcsolatos hibák elhárítása során.

A tároló naplóinak megtekintéséhez a **Beállítások**területen válassza a **tárolók** > **naplók**lehetőséget. Látnia kell a HTTP GET kérést is, amely akkor jött létre, amikor megtekintette az alkalmazást a böngészőjében.

![Tárolónaplók az Azure Portalon](media/container-instances-quickstart-template/aci-logs.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a tárolóval, a tároló példányának **Áttekintés** lapján válassza a **Törlés**lehetőséget. A rendszer kérésére erősítse meg a törlést.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Azure Container-példányt egy nyilvános Microsoft-rendszerképből. Ha szeretne létrehozni és üzembe helyezni egy tárolórendszerképet egy privát Azure-tárolóregisztrációs adatbázisból, lépjen tovább az Azure Container Instances oktatóanyagára.

> [!div class="nextstepaction"]
> [Oktatóanyag: tároló-rendszerkép létrehozása a Azure Container Instances való üzembe helyezéshez](./container-instances-tutorial-prepare-app.md)

A sablonok létrehozásának folyamatát ismertető, lépésenkénti oktatóanyagért lásd:

> [!div class="nextstepaction"]
> [Oktatóanyag: az első Azure Resource Manager-sablon létrehozása és üzembe helyezése](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
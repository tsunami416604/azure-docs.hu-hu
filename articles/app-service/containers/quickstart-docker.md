---
title: 'Gyors útmutató: egyéni Linux-tároló futtatása'
description: Ismerkedjen meg a Linux-tárolókkal Azure App Service az első egyéni tároló üzembe helyezésével az Azure Container-jegyzékek használatával.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.openlocfilehash: 62e6b007e89fc6be726d3d971ca838770db9cb6e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422156"
---
# <a name="deploy-a-custom-linux-container-to-azure-app-service"></a>Egyéni Linux-tároló üzembe helyezése Azure App Service

A Linuxon futó App Service a Linuxon előre definiált alkalmazásokat biztosít, amelyek támogatják a .NET, a PHP, a Node. js és más nyelveket. Használhat egyéni Docker rendszerképet is, hogy a webalkalmazást egy, az Azure-ban nem meghatározott alkalmazáscsoportban futtassa. Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy rendszerképet egy [Azure Container Registryból](/azure/container-registry) (ACR) a app Serviceba.

## <a name="prerequisites"></a>Előfeltételek

* Egy [Azure-fiók](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio Code](https://code.visualstudio.com/)
* A [vs Code Azure app Service kiterjesztése](vscode:extension/ms-azuretools.vscode-azureappservice). Ezzel a bővítménnyel linuxos Web Apps hozhat létre, kezelhet és telepíthet az Azure platform szolgáltatásként (Péter).
* A [vs Code Docker-bővítménye](vscode:extension/ms-azuretools.vscode-docker). Ezzel a bővítménnyel egyszerűsítheti a helyi Docker-rendszerképek és-parancsok kezelését, valamint az Azure-ba épített alkalmazás-lemezképek üzembe helyezését.

## <a name="create-an-image"></a>Rendszerkép létrehozása

A rövid útmutató elvégzéséhez szüksége lesz egy [Azure Container Registryban](/azure/container-registry)tárolt megfelelő webalkalmazás-rendszerképre. Kövesse a rövid útmutató [: Private Container Registry létrehozása a Azure Portal segítségével](/azure/container-registry/container-registry-get-started-portal), de az `hello-world` rendszerkép helyett használja a `mcr.microsoft.com/azuredocs/go` rendszerképet. A [minta Docker az Azure Samples-](https://github.com/Azure-Samples/go-docs-hello-world)tárházban található.

> [!IMPORTANT]
> Ügyeljen arra, hogy a **rendszergazda felhasználó** beállítással **engedélyezze** a tároló-beállításjegyzék létrehozását. Azt is megteheti, hogy a beállításjegyzék oldalának **hozzáférési kulcsok** szakasza is be van állítva a Azure Portal. Ez a beállítás App Service hozzáféréshez szükséges.

## <a name="sign-in"></a>Bejelentkezés

Ezután indítsa el a VS Code-ot, és jelentkezzen be az Azure-fiókjába az App Service bővítmény használatával. Ehhez válassza ki az Azure-emblémát a tevékenység sávjában, navigáljon az **app Service** Explorerrel, majd válassza a **Bejelentkezés az Azure** -ba lehetőséget, és kövesse az utasításokat.

![Bejelentkezés az Azure-ba](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Előfeltételek ellenőrzése

Most megtekintheti, hogy az összes előfeltétel telepítve van-e, és megfelelően van-e konfigurálva.

A VS Code-ban az Azure-beli e-mail-cím jelenik meg az állapotsorban és az előfizetés az **app Service** Explorerben.

Ezután ellenőrizze, hogy a Docker telepítve van-e és fut-e. A következő parancs megjeleníti a Docker-verziót, ha az fut.

```bash
docker --version
```

Végezetül ellenőrizze, hogy a Azure Container Registry csatlakoztatva van-e. Ehhez válassza ki a Docker-emblémát a tevékenység sávjában, majd navigáljon **a**beállításjegyzékek elemre.

![Beállításjegyzékek](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>A rendszerkép üzembe helyezése Azure App Service

Most, hogy minden be van állítva, telepítheti a lemezképet [Azure app Service](https://azure.microsoft.com/services/app-service/) közvetlenül a Docker bővítmény Explorerrel.

Keresse meg a rendszerképet **a** **Docker** Explorer beállításjegyzékek csomópontjában, és bontsa ki a címkék megjelenítéséhez. Kattintson a jobb gombbal a címkére, majd válassza a **lemezkép központi telepítése Azure app Service**lehetőséget.

Itt az alábbi utasításokat követve választhatja ki az előfizetést, egy globálisan egyedi alkalmazás nevét, egy erőforráscsoportot és egy App Service tervet. Válassza a **B1 alapszintű** lehetőséget a díjszabási szinthez és a régióhoz.

Az üzembe helyezés után az alkalmazás a következő címen érhető el: `http://<app name>.azurewebsites.net`.

Az **erőforráscsoport** az alkalmazás összes erőforrásának elnevezett gyűjteménye az Azure-ban. Egy erőforráscsoport például tartalmazhat egy webhelyre, egy adatbázisra és egy Azure-függvényre mutató hivatkozást is.

A **app Service-csomag** határozza meg a webhely üzemeltetéséhez használt fizikai erőforrásokat. Ez a rövid útmutató egy **alapszintű** üzemeltetési csomagot használ a **Linux** -infrastruktúrán, ami azt jelenti, hogy a webhely más webhelyeken található Linux-gépen lesz tárolva. Ha az **alapszintű** csomaggal kezdődik, akkor a Azure Portal a vertikális felskálázáshoz használhatja, hogy a tiéd legyen a gépen futó egyetlen hely.

## <a name="browse-the-website"></a>Böngészés a webhelyen

A rendszer az üzembe helyezés során megnyílik a **kimeneti** panel, hogy jelezze a művelet állapotát. Ha a művelet befejeződik, keresse meg az App **Service** Explorerben létrehozott alkalmazást, kattintson rá a jobb gombbal, majd válassza a **Tallózás webhely** lehetőséget a webhely böngészőben való megnyitásához.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Következő lépések

Gratulálunk, sikeresen elvégezte ezt a rövid útmutatót!

Ezután tekintse meg a többi Azure-bővítményt.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI-eszközök](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Eszközök Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Az [Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) bővítmény csomag telepítésével vagy az összes beszerzésével.

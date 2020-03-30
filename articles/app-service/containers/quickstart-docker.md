---
title: 'Rövid útmutató: Egyéni Linux-tároló futtatása'
description: Az Azure App Service Linux-tárolóinak üzembe helyezésével kezdheti az első egyéni tárolóüzembe helyezését az Azure Container Regista használatával.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.openlocfilehash: 62e6b007e89fc6be726d3d971ca838770db9cb6e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75422156"
---
# <a name="deploy-a-custom-linux-container-to-azure-app-service"></a>Egyéni Linux-tároló üzembe helyezése az Azure App Service szolgáltatásban

Az App Service linuxos szolgáltatás a Linuxon előre definiált alkalmazáskötegeket biztosít olyan nyelvek támogatásával, mint a .NET, a PHP, a Node.js és mások. Használhat egyéni Docker rendszerképet is, hogy a webalkalmazást egy, az Azure-ban nem meghatározott alkalmazáscsoportban futtassa. Ez a rövid útmutató bemutatja, hogyan telepíthet egy lemezképet egy [Azure Container Registry](/azure/container-registry) (ACR) az App Service-ből.

## <a name="prerequisites"></a>Előfeltételek

* [Egy Azure-fiók](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker között](https://www.docker.com/community-edition)
* [Visual Studio kód](https://code.visualstudio.com/)
* Az [Azure App Service-bővítmény a VS-kódhoz.](vscode:extension/ms-azuretools.vscode-azureappservice) Ezzel a kiterjesztéssel linuxos webalkalmazásokat hozhat létre, kezelhet és helyezhet üzembe az Azure Platformszolgáltatáson (PaaS).
* A [VS-kód Docker-bővítménye.](vscode:extension/ms-azuretools.vscode-docker) Ezzel a kiterjesztéssel egyszerűsítheti a helyi Docker-lemezképek és -parancsok felügyeletét, és üzembe helyezheti a beépített alkalmazásképeket az Azure-ba.

## <a name="create-an-image"></a>Kép létrehozása

A rövid útmutató végrehajtásához szüksége lesz egy megfelelő webalkalmazás-lemezképre, amelyet egy [Azure Container Registry](/azure/container-registry)tárol. Kövesse a [rövid útmutató utasításait: Hozzon létre egy privát tároló beállításjegyzéket az Azure Portalon,](/azure/container-registry/container-registry-get-started-portal)de használja a `mcr.microsoft.com/azuredocs/go` lemezképet a `hello-world` rendszerkép helyett. A referencia, a [minta Dockerfile található Azure Minták tártára.](https://github.com/Azure-Samples/go-docs-hello-world)

> [!IMPORTANT]
> Ügyeljen arra, hogy a tároló beállításjegyzékének létrehozásakor állítsa be a **Rendszergazda felhasználó** **beállítást engedélyezésre.** Azt is beállíthatja, hogy az **Access-kulcsok** szakasza a beállításjegyzék-lap az Azure Portalon. Ez a beállítás az App Service-hozzáféréshez szükséges.

## <a name="sign-in"></a>Bejelentkezés

Ezután indítsa el a VS-kódot, és jelentkezzen be az Azure-fiókjába az App Service-bővítmény használatával. Ehhez válassza ki az Azure-emblémát a tevékenységsávon, keresse meg az APP SERVICE-kezelőt, majd válassza **a Bejelentkezés az Azure-ba** lehetőséget, és kövesse az utasításokat. **APP SERVICE**

![bejelentkezés az Azure-ba](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Előfeltételek ellenőrzése

Most ellenőrizheti, hogy az összes előfeltétel telepítve van-e és megfelelően van-e konfigurálva.

A VS-kódban az Azure-beli e-mail-címét az állapotsorban és az előfizetést az **APP SERVICE-kezelőben** kell látnia.

Ezután ellenőrizze, hogy telepítette-e és futtatta-e a Dockert. A következő parancs megjeleníti a Docker-verziót, ha fut.

```bash
docker --version
```

Végül győződjön meg arról, hogy az Azure Container Registry csatlakoztatva van. Ehhez jelölje ki a Docker emblémát a tevékenységsávon, majd keresse meg a **BEÁLLÍTÁSJEGYZÉK**et.

![Beállításjegyzékek](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>A lemezkép üzembe helyezése az Azure App Service szolgáltatásban

Most, hogy minden konfigurálva van, telepítheti a lemezképet az [Azure App Service-be](https://azure.microsoft.com/services/app-service/) közvetlenül a Docker-bővítmény-kezelőből.

Keresse meg a lemezképet a **Regisztika-csomópont** alatt a **DOCKER** explorerben, és bontsa ki a címkék megjelenítéséhez. Kattintson a jobb gombbal egy címkére, és válassza **a Lemezkép telepítése az Azure App Service szolgáltatásba parancsot.**

Itt kövesse az utasításokat egy előfizetés, egy globálisan egyedi alkalmazásnév, egy erőforráscsoport és egy App Service-csomag kiválasztásához. Válassza a **B1 Alapszintű** a tarifacsomag, és a régió.

A telepítés után az `http://<app name>.azurewebsites.net`alkalmazás elérhető a.

Az **erőforráscsoport** az alkalmazás összes erőforrásának elnevezett gyűjteménye az Azure-ban. Például egy erőforráscsoport tartalmazhat egy webhelyre, egy adatbázisra és egy Azure-függvényre mutató hivatkozást.

Az **App Service-csomag** határozza meg a webhely üzemeltetéséhez használt fizikai erőforrásokat. Ez a rövid útmutató egy **alapszintű** üzemeltetési tervet használ Linux-infrastruktúrán, ami azt jelenti, hogy a webhely más webhelyek mellett egy Linux gépen lesz tárolva. **Linux** Ha az **alapszintű** csomaggal kezdi, az Azure Portal segítségével skálázhatja úgy, hogy a tiéd az egyetlen számítógépen futó hely.

## <a name="browse-the-website"></a>Böngészés a honlapon

A **kimeneti** panel az üzembe helyezés során megnyílik a művelet állapotának jelzésére. Amikor a művelet befejeződött, keresse meg a létrehozott alkalmazást az **APP SERVICE** intézőben, kattintson rá a jobb gombbal, majd válassza a **Webhely tallózása** parancsot a webhely böngészőben való megnyitásához.

> [!div class="nextstepaction"]
> [Belefutottam egy problémába.](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>További lépések

Gratulálunk, sikeresen befejezte ezt a rövid útmutatót!

Ezután tekintse meg a többi Azure-bővítményt.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI-eszközök](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Az Azure Resource Manager eszközei](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Vagy mindet beszerezheti az [Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) bővítménycsomag telepítésével.

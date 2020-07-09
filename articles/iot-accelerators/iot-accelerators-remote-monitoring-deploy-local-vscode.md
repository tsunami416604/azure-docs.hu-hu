---
title: Távoli figyelési megoldás üzembe helyezése helyileg – Visual Studio Code – Azure | Microsoft Docs
description: Ez a útmutató bemutatja, hogyan helyezheti üzembe a távoli figyelési megoldás gyorsítása a helyi gépen a Visual Studio Code használatával a teszteléshez és fejlesztéshez.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "73890961"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>A távoli figyelési megoldás-gyorsító helyi telepítése – Visual Studio Code

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ebből a cikkből megtudhatja, hogyan helyezheti üzembe a távoli figyelési megoldás Gyorssegédjét a helyi gépre tesztelés és fejlesztés céljából. Megtudhatja, hogyan futtathatja a szolgáltatásait a Visual Studio Code-ban. A helyi fürtszolgáltatások üzembe helyezése a következő felhőalapú szolgáltatásokat használja: IoT Hub, Cosmos DB, Azure streaming Analytics és Azure Time Series Insights.

## <a name="prerequisites"></a>Előfeltételek

A távoli figyelési megoldás által használt Azure-szolgáltatások üzembe helyezéséhez aktív Azure-előfizetésre van szükség.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés befejezéséhez a következő eszközökre van szükség a helyi fejlesztési gépen:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [VS Code C#-bővítménye](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js V8](https://nodejs.org/) – ez a szoftver előfeltétele annak a PC CLI-nek, amelyet a parancsfájlok az Azure-erőforrások létrehozásához használnak. Ne használja a Node.js V10-es

> [!NOTE]
> A Visual Studio Code Windows, Mac és Ubuntu rendszerekhez érhető el.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>A szervizcsomagok futtatása

Ebben a szakaszban a távoli monitorozási szolgáltatásait futtatja. A webes KEZELŐFELÜLETet natív módon futtatja, az eszköz szimulációs szolgáltatása a Docker-ben és a Visual Studio Code-ban.

### <a name="build-the-code"></a>A kód létrehozása

A Azure-IOT-PCs-Remote-Monitoring-dotnet\services navigáljon a parancssorban, és futtassa a következő parancsokat a kód felépítéséhez.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Az összes többi webszolgáltatás üzembe helyezése a helyi gépen

A következő lépések bemutatják, hogyan futtathatja a távoli monitorozási szolgáltatásait a Visual Studio Code-ban:

1. Indítsa el a Visual Studio Code-ot.
1. A VS Code-ban nyissa meg az **Azure-IOT-PC-Remote-Monitoring-DotNet** mappát.
1. Hozzon létre egy **. vscode** nevű új mappát az **Azure-IOT-PC-Remote-Monitoring-DotNet** mappában.
1. Másolja a **launch.json** és a **tasks.json** services\scripts\local\launch\idesettings\vscode az imént létrehozott **. vscode** mappába.
1. Nyissa meg a **hibakeresési panelt** a vs Code-ban, és futtassa az **összes szolgáltatás futtatása** konfigurációt. Ez a konfiguráció futtatja az eszköz szimulációs szolgáltatását a Docker-ben, és futtatja a hibakeresőben lévő többi szolgáltatást.

A hibakeresési konzolon futó **összes Microsoervices futtatásának** kimenete a következőhöz hasonlóan néz ki:

[![Helyi-szolgáltatás üzembe helyezése](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>A webes felhasználói felület futtatása

Ebben a lépésben elindítja a webes felhasználói felületet. A helyi másolatban navigáljon a **Azure-IOT-PCs-Remote-Monitoring-dotnet\webui** mappához, és futtassa a következő parancsokat:

```cmd
npm install
npm start
```

A kezdés befejezésekor a böngésző a **http: \/ /localhost: 3000/Dashboard**lapot jeleníti meg. A rendszer a lapon szereplő hibákat várta. Ha hiba nélkül szeretné megtekinteni az alkalmazást, hajtsa végre a következő lépést.

### <a name="configure-and-run-nginx"></a>Az NGINX konfigurálása és futtatása

Hozzon létre egy fordított proxykiszolgálót a helyi gépen futó webalkalmazás és-szolgáltatások összekapcsolásához:

* Másolja az **Nginx. conf** fájlt a **webui\scripts\localhost** mappából a **nginx\conf** telepítési könyvtárába.
* Az **Nginx**futtatása.

További információ az **Nginx**futtatásáról: [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Kapcsolódás az irányítópulthoz

A távoli figyelési megoldás irányítópultjának eléréséhez navigáljon a böngészőben a http: \/ /localhost: 9000 elemre.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A szükségtelen díjak elkerüléséhez, ha befejezte a tesztelést, távolítsa el a Cloud Servicest az Azure-előfizetésből. A szolgáltatások eltávolításához navigáljon a [Azure Portal](https://ms.portal.azure.com) , és törölje a **Start. cmd** parancsfájl által létrehozott erőforráscsoportot.

Törölheti a távoli figyelési tárház helyi példányát is, amikor a forráskódot a GitHubról klónozotta.

## <a name="next-steps"></a>További lépések

Most, hogy üzembe helyezte a távoli figyelési megoldást, a következő lépés a [megoldás irányítópultjának képességeinek megismerése](quickstart-remote-monitoring-deploy.md).

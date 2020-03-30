---
title: Távoli figyelési megoldás helyi telepítése - Visual Studio-kód - Azure | Microsoft dokumentumok
description: Ez az útmutató bemutatja, hogyan telepítheti a távoli figyelési megoldásgyorsítót a helyi számítógépre a Visual Studio-kód használatával teszteléshez és fejlesztéshez.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d20e9a6a78d99a23fe4b98aeb4f3eb8359da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890961"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>A távfigyelési megoldás gyorsítójának helyi telepítése – Visual Studio-kód

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ez a cikk bemutatja, hogyan telepítheti a távoli figyelési megoldás gyorsítót a helyi számítógépre tesztelésre és fejlesztésre. Megtudhatja, hogyan futtathatja a mikroszolgáltatásokat a Visual Studio-kódban. A helyi mikroszolgáltatások központi telepítése a következő felhőszolgáltatásokat használja: IoT Hub, Cosmos DB, Azure Streaming Analytics és Azure Time Series Insights.

## <a name="prerequisites"></a>Előfeltételek

A távfigyelési megoldásgyorsító által használt Azure-szolgáltatások üzembe helyezéséhez aktív Azure-előfizetésre van szüksége.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés végrehajtásához a következő eszközökre van szükség a helyi fejlesztői gépen:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker között](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio kód](https://code.visualstudio.com/)
* [A VS Code C# kiterjesztése](https://code.visualstudio.com/docs/languages/csharp)
* [Node.js v8](https://nodejs.org/) – ez a szoftver előfeltétele a PCS CLI, hogy a parancsfájlok azure-erőforrások létrehozásához használt. Ne használja a Node.js v10-et

> [!NOTE]
> A Visual Studio Code windowsos, Mac és Ubuntu rendszerhez érhető el.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>A mikroszolgáltatások futtatása

Ebben a szakaszban futtatja a távoli figyelési mikroszolgáltatások. A webes felhasználói felületet natívmódon futtatja, a Docker eszközszimulációs szolgáltatását és a Visual Studio-kódban lévő mikroszolgáltatásokat.

### <a name="build-the-code"></a>A kód létrehozása

Keresse meg az azure-iot-pcs-remote-monitoring-dotnet\services mappában a parancssorban, és futtassa a következő parancsokat a kód létrehozásához.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>Az összes többi mikroszolgáltatás telepítése helyi számítógépen

Az alábbi lépések bemutatják, hogyan futtathatja a távoli figyelési mikroszolgáltatásokat a Visual Studio-kódban:

1. Indítsa el a Visual Studio Code-ot.
1. A VS Code alkalmazásban nyissa meg az **azure-iot-pcs-remote-monitoring-dotnet mappát.**
1. Hozzon létre egy új mappát **,vscode** néven az **azure-iot-pcs-remote-monitoring-dotnet** mappában.
1. Másolja a **launch.json** és **tasks.json** fájlokat a services\scripts\local\launch\idesettings\vscode fájlból az imént létrehozott **.vscode** mappába.
1. Nyissa meg a **Hibakeresési panelt** a VS-kódban, és futtassa az **Összes mikroszolgáltatás-konfiguráció futtatása segédprogramot.** Ez a konfiguráció futtatja az eszközszimulációs mikroszolgáltatást a Dockerben, és futtatja a többi mikroszolgáltatást a hibakeresőben.

A **Debug console-ban futó Összes mikrosoervice s** kimenete a következőképpen néz ki:

[![Helyi-mikroszolgáltatások telepítése](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>A webes felhasználói felület futtatása

Ebben a lépésben indítsa el a webes felhasználói felületet. Nyissa meg **az azure-iot-pcs-remote-monitoring-dotnet\webui** mappát a helyi példányban, és futtassa a következő parancsokat:

```cmd
npm install
npm start
```

Amikor a start befejeződött, a böngésző megjeleníti az oldalt **\/http: /localhost:3000/dashboard**. A hibák ezen az oldalon várható. Az alkalmazás hibamentes megtekintéséhez hajtsa végre a következő lépést.

### <a name="configure-and-run-nginx"></a>Az NGINX konfigurálása és futtatása

Fordított proxykiszolgáló beállítása a helyi számítógépen futó webalkalmazás és mikroszolgáltatások összekapcsolására:

* Másolja a **nginx.conf** fájlt a **webui\scripts\localhost** mappából a **nginx\conf** telepítési könyvtárba.
* Fuss **nginx**.

A **nginx**futtatásáról további információt [a Nginx for Windows](https://nginx.org/en/docs/windows.html)című témakörben talál.

### <a name="connect-to-the-dashboard"></a>Csatlakozás az irányítópulthoz

A távoli figyelési megoldás irányítópultjának eléréséhez keresse meg a http:\//localhost:9000 lapot a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A szükségtelen díjak elkerülése érdekében, amikor befejezte a tesztelést, távolítsa el a felhőszolgáltatásokat az Azure-előfizetésből. A szolgáltatások eltávolításához keresse meg az [Azure Portalon,](https://ms.portal.azure.com) és törölje a **start.cmd** parancsfájl által létrehozott erőforráscsoportot.

Törölheti is a távoli figyelési tárház helyi példányát, amelyet akkor hozott létre, amikor klónozta a forráskódot a GitHubról.

## <a name="next-steps"></a>További lépések

Most, hogy telepítette a távoli figyelési megoldást, a következő lépés [a megoldásirányítópult képességeinek feltárása.](quickstart-remote-monitoring-deploy.md)

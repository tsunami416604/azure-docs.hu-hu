---
title: A távoli figyelési megoldás üzembe helyileg (Visual Studio Code) – Azure |} A Microsoft Docs
description: Ez az útmutató bemutatja, hogyan lehet a távoli figyelési megoldásgyorsító üzembe helyezése a helyi gépen a Visual Studio Code teszteléshez és fejlesztéshez.
author: avneet723
manager: hegate
ms.author: avneets
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: ed3301eb0e723e05e2a642ffea2f1609032553b4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730173"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---visual-studio-code"></a>A távoli figyelési megoldásgyorsító helyileg – Visual Studio Code telepítése

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ez a cikk bemutatja, hogyan való teszteléshez és fejlesztéshez a helyi gépre a távoli figyelési megoldásgyorsító üzembe helyezése. Megismerheti, hogyan futtathatja a mikroszolgáltatás-alapú Visual Studio Code-ban. Egy helyi mikroszolgáltatások üzembe helyezés az alábbi cloud-szolgáltatásokat használja: IoT Hub, Cosmos DB, Azure Streaming Analytics, and Azure Time Series Insights.

## <a name="prerequisites"></a>Előfeltételek

Az Azure-szolgáltatások használják a távoli figyelési megoldásgyorsító üzembe helyezéséhez aktív Azure-előfizetés szükséges.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés befejezéséhez, szüksége van a helyi fejlesztői gépen telepítve van a következő eszközöket:

* [Git](https://git-scm.com/)
* [.NET Core](https://dotnet.microsoft.com/download)
* [Docker](https://www.docker.com)
* [Nginx](https://nginx.org/en/download.html)
* [Visual Studio Code](https://code.visualstudio.com/)
* [A VS Code C# bővítmény](https://code.visualstudio.com/docs/languages/csharp)
* [NODE.js v8](https://nodejs.org/) – Ez a szoftver előfeltétele a számítógépek parancssori felület, amely a parancsfájlok használata Azure-erőforrások létrehozásához. Ne használja a Node.js v10

> [!NOTE]
> A Visual Studio Code Windows, Mac és Ubuntu érhető el.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices"></a>A mikroszolgáltatások futtatása

Ebben a szakaszban a távoli figyelési mikroszolgáltatások fogja futtatni. A webes felhasználói felület futtatása natív módon az Eszközszimuláció service a Docker és a mikroszolgáltatás-alapú Visual Studio Code-ban.

### <a name="build-the-code"></a>A kód létrehozása

Azure-iot-pcs-remote-monitoring-dotnet\services a parancssorban keresse meg, és futtassa az alábbi parancsokat a kód felépítéséhez.

```cmd
dotnet restore
dotnet build -c Release
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>A helyi számítógépen az összes többi mikroszolgáltatások üzembe helyezéséhez

A következő lépések bemutatják, hogyan futtathatja a távoli figyelési mikroszolgáltatás-alapú Visual Studio Code-ban:

1. Indítsa el a Visual Studio Code-ot.
1. A VS Code-ban nyissa meg a **azure-iot-pcs-remote-monitoring-dotnet** mappát.
1. Hozzon létre egy új nevű **.vscode** a a **azure-iot-pcs-remote-monitoring-dotnet** mappát.
1. Másolja a fájlokat **launch.json** és **tasks.json** services\scripts\local\launch\idesettings\vscode, az a **.vscode** imént létrehozott mappára.
1. Nyissa meg a **hibakeresési panel** a VS Code, és futtassa a **összes futtatása mikroszolgáltatások** konfigurációja. Ez a konfiguráció fut, az eszköz szimulálása mikroszolgáltatások Docker, és az egyéb mikroszolgáltatások fut, a hibakereső.

Futó kimenete **összes futtatása microsoervices** a Debug-konzolon a következőhöz hasonló:

[![Deploy-Local-Microservices](./media/deploy-locally-vscode/auth-debug-results-inline.png)](./media/deploy-locally-vscode/auth-debug-results-expanded.png#lightbox)

### <a name="run-the-web-ui"></a>A webes felhasználói felület futtatása

Ebben a lépésben indítsa el a webes felhasználói felületen. Navigáljon a **azure-iot-pcs-remote-monitoring-dotnet\webui** a helyi mappájába másolja, és futtassa a következő parancsokat:

```cmd
npm install
npm start
```

A kezdő befejeződése után a böngésző megjeleníti-e az oldal **http:\//localhost:3000 / irányítópult**. Ezen az oldalon a hibák várhatóan. Hibák nélkül az alkalmazás megtekintéséhez hajtsa végre a következő lépéssel.

### <a name="configure-and-run-nginx"></a>Konfigurálása és futtatása az nginx-et

Állítsa be a fordított proxykiszolgáló mutató hivatkozást a webalkalmazás és a helyi gépen futó mikroszolgáltatásokat:

* Másolás a **nginx.conf** fájlt a **webui\scripts\localhost** mappát a **nginx\conf** telepítési könyvtár.
* Futtatás **nginx**.

További információ a futó **nginx**, lásd: [nginx-et a Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Csatlakozás az irányítópulton

A távoli figyelési megoldás irányítópultján eléréséhez lépjen a http:\//localhost:9000 a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Kerülje a szükségtelen költségek, a tesztelés befejezése után távolítsa el a cloud services az Azure-előfizetésből. Távolítsa el a szolgáltatásokat, lépjen a [az Azure portal](https://ms.portal.azure.com) és törölje az erőforrást, amely a **start.cmd** létrehozott parancsfájlt.

A távoli figyelési jön létre, amikor a forráskódját a Githubról klónozott tárház helyi példányának is törölheti.

## <a name="next-steps"></a>További lépések

Most, hogy a távoli figyelési megoldás üzembe helyezte, a következő lépés, hogy [Fedezze fel a megoldás irányítópultján képességeit](quickstart-remote-monitoring-deploy.md).

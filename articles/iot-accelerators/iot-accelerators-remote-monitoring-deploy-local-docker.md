---
title: A távoli figyelési megoldás helyileg – Docker - üzembe helyezése Azure |} A Microsoft Docs
description: Ez az útmutató bemutatja, hogyan lehet a távoli figyelési megoldásgyorsító üzembe helyezése a helyi gépen a Docker használatával teszteléshez és fejlesztéshez.
author: avneet723
manager: hegate
ms.author: avneet723
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 252aacfeb85f23699c6a2e2ac1f457f9e2b59b0e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997183"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---docker"></a>A távoli figyelési megoldásgyorsító helyileg – a Docker üzembe helyezése

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ez a cikk bemutatja, hogyan való teszteléshez és fejlesztéshez a helyi gépre a távoli figyelési megoldásgyorsító üzembe helyezése. Elsajátíthatja a mikroszolgáltatások helyi Docker-tárolók üzembe helyezése. Egy helyi mikroszolgáltatások üzembe helyezés az alábbi cloud-szolgáltatásokat használja: Az IoT Hub, Cosmos DB, az Azure Stream Analytics és az Azure Time Series Insights-szolgáltatások a felhőben.

Ha meg szeretné futtatni a távoli figyelési megoldásgyorsító IDE-ben a helyi gépén, [üzembe helyezése a távoli figyelési megoldásgyorsító helyileg – Visual Studio](iot-accelerators-remote-monitoring-deploy-local.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure-szolgáltatások használják a távoli figyelési megoldásgyorsító üzembe helyezéséhez aktív Azure-előfizetés szükséges.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés befejezéséhez, szüksége van a helyi fejlesztői gépen telepítve van a következő eszközöket:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [A Visual Studio](https://visualstudio.microsoft.com/) – Ha azt tervezi, hogy módosítja a mikroszolgáltatás-alapú.
* [NODE.js v8](https://nodejs.org/) – Ez a szoftver előfeltétele a számítógépek parancssori felület, amely a parancsfájlok használata Azure-erőforrások létrehozásához. Ne használja a Node.js v10.

> [!NOTE]
> Ezek az eszközök számos platformon, beleértve a Windows, Linux- és IOS-es érhetők el.

[!INCLUDE [iot-accelerators-local-setup](../../includes/iot-accelerators-local-setup.md)]

## <a name="run-the-microservices-in-docker"></a>Futtassa a mikroszolgáltatások a Dockerben

Hozzá kell férnie a által beállított környezeti változókat, hogy nyisson meg egy új parancssort a **start.cmd** parancsfájlt. A Windows a környezeti változók vannak beállítva a következő parancs futtatásával ellenőrizheti:

```cmd
set PCS
```

A parancs által beállított környezeti változókat jeleníti meg a **start.cmd** parancsfájlt.

Győződjön meg arról, hogy a Docker fut-e a helyi gépen.
> [!NOTE]
> Docker futnia kell [Linux-tárolók](https://docs.docker.com/docker-for-windows/) Windows rendszeren fut, ha.

A helyi Docker-tárolókban futó mikroszolgáltatásokat az Azure cloud services eléréséhez szükséges. Az internetkapcsolat működését a Docker-környezet pingelni a tárolókon belül egy internet-címét a következő paranccsal tesztelheti:

```cmd/sh
docker run --rm -ti library/alpine ping google.com
```

A megoldásgyorsító futtatja, lépjen a **szolgáltatások\\parancsfájlok\\helyi** mappát a parancssori környezetben, és futtassa az alábbi parancsot:

```cmd/sh
docker-compose up
```

> [!NOTE] 
> Győződjön meg arról, hogy [egy helyi meghajtó megosztásához](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/issues/115) docker futtatása előtt `docker-compose up`.

Ez a parancs első futtatásakor Docker tölti le a mikroszolgáltatás-lemezképeket hozhat létre a helyi tárolókat a Docker hubból. A következő futtatásakor a Docker a tárolók a rendszer azonnal futtatja.

> [!TIP]
> A Microsoft új Docker-rendszerképek gyakran közzéteszi az új funkciókkal. Használhatja következő csoportját cleanup parancsokat a helyi Docker-tárolók és a megfelelő rendszerképek előtt, kérje le a legújabb azokat:

```cmd/sh
docker list
docker rm <list_of_containers>
docker rmi <list_of_images>
```

Egy külön rendszerhéj használatával a tároló naplóinak megtekintéséhez. Először keresse ki a tárolót azonosító használatával a `docker ps` parancsot. Ezután `docker logs {container-id} --tail 1000` tekintheti meg a megadott tároló utolsó 1000 bejegyzéseket.

### <a name="start-the-stream-analytics-job"></a>A Stream Analytics-feladat indítása

Kövesse az alábbi lépéseket a Stream Analytics-feladat indítása:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a **erőforráscsoport** létrehozott a megoldáshoz. Az erőforráscsoport neve nem a választott név a megoldáshoz, ha futtatta a **start.cmd** parancsfájlt.
1. Kattintson a **Stream Analytics-feladat** az erőforrások listájában.
1. A Stream Analytics-feladat a **áttekintése** lap, kattintson a **Start** gombra. Kattintson a **Start** a feladat elindításához.

### <a name="connect-to-the-dashboard"></a>Csatlakozás az irányítópulton

A távoli figyelési megoldás irányítópultján eléréséhez keresse meg [ http://localhost:8080 ](http://localhost:8080) a böngészőben. Mostantól használhatja a webes felhasználói felület és a helyi mikroszolgáltatások.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Kerülje a szükségtelen költségek, a tesztelés befejezése után távolítsa el a cloud services az Azure-előfizetésből. Távolítsa el a szolgáltatásokat, lépjen a [az Azure portal](https://ms.portal.azure.com) és törölje az erőforrást, amely a **start.cmd** létrehozott parancsfájlt.

Használja a `docker-compose down --rmi all` paranccsal távolítsa el a Docker-rendszerképek és szabadítson fel lemezterületet a helyi gépen. A távoli figyelési jön létre, amikor a forráskódját a Githubról klónozott tárház helyi példányának is törölheti.

## <a name="next-steps"></a>További lépések

Most, hogy a távoli figyelési megoldás üzembe helyezte, a következő lépés, hogy [Fedezze fel a megoldás irányítópultján képességeit](quickstart-remote-monitoring-deploy.md).

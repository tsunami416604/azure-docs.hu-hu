---
title: Helyileg – a távoli figyelési megoldásban üzembe Azure |} A Microsoft Docs
description: Ez az útmutató bemutatja, hogyan helyezze üzembe a távoli figyelési megoldásgyorsító teszteléshez és fejlesztéshez a helyi gépen.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/06/2018
ms.topic: conceptual
ms.openlocfilehash: aaaf31d5c1faae8176dd9909f74c70300c3f0b4e
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44716433"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>A távoli figyelési megoldásgyorsító helyileg üzembe helyezése

Ez a cikk bemutatja, hogyan való teszteléshez és fejlesztéshez a helyi gépre a távoli figyelési megoldásgyorsító üzembe helyezése. Ez a megközelítés egy helyi Docker-tárolót üzembe helyezi a mikroszolgáltatásokat és a felhőben az IoT Hub, a Cosmos DB és az Azure Time Series Insights-szolgáltatásokat használ. A megoldásgyorsítók (személyi számítógépek) CLI az Azure cloud services üzembe helyezéséhez használhatja.

## <a name="prerequisites"></a>Előfeltételek

Az Azure-szolgáltatások használják a távoli figyelési megoldásgyorsító üzembe helyezéséhez aktív Azure-előfizetés szükséges.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

A helyi telepítés befejezéséhez, szüksége van a helyi fejlesztői gépen telepítve van a következő eszközöket:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [A docker compose](https://docs.docker.com/compose/install/)
* [NODE.js](https://nodejs.org/) – ezt a szoftvert a számítógépek CLI előfeltétele.

> [!NOTE]
> Ezek az eszközök számos platformon, beleértve a Windows, Linux- és IOS-es érhetők el.

### <a name="download-the-source-code"></a>Letöltheti a forráskódot

 A távoli figyelési GitHub forráskódraktárban kell letölteni, konfigurálásához és futtatásához, amelyek tartalmazzák a mikroszolgáltatások Docker-rendszerképek Docker konfigurációs fájljait tartalmazza. Klónozza, valamint a tárház helyi verzióját használja a parancssori környezetet keresse meg a megfelelő mappát a helyi gépen, és futtassa a következő parancsok egyikét:

A mikroszolgáltatások Java-implementációk telepítéséhez futtassa:

```cmd/sh
git clone --recurse-submodules -j8 https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Telepítse a .net-megvalósítások, mikroszolgáltatások, futtassa:

```cmd\sh
git clone --recurse-submodules -j8 https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

> [!NOTE]
> Ezek a parancsok letöltheti a forráskódot, az a mikroszolgáltatásokat. Már nincs szüksége a forráskódra futtassa a mikroszolgáltatások a Dockerben, bár a forráskód hasznos, ha később szeretné módosítani az előre konfigurált megoldás és a módosítások helyi tesztelése.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások üzembe helyezése

Bár ez a cikk bemutatja, hogyan helyileg történő futtatása a mikroszolgáltatás-alapú, Azure-szolgáltatások a felhőben futó függenek. Telepítheti az Azure-szolgáltatások [manuálisan az Azure Portalon keresztül](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), vagy parancsfájl. A következő parancsfájl példák feltételezik, hogy egy Windows-gépen használja a .NET-adattárban. Ha egy másik környezetben dolgozik, állítsa be az elérési utak, fájlkiterjesztések, és elérési út elválasztók megfelelően. A megadott parancsprogram használata:

1. A parancssori környezetben, keresse meg a **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\launch** mappájához klónozott másolatának a tárházban.

1. Futtassa a **start.cmd** szkriptet, és kövesse az utasításokat. A parancsfájl kérni fogja, hogy a következő információkat:
    * A megoldás nevét.
    * A használandó előfizetés.
    * Használata az Azure-adatközpont helyét.

    A parancsfájl hoz létre, a megoldás nevű erőforráscsoportot az Azure-ban.

1. A parancssori környezetben, keresse meg a **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\launch\os\win** mappájához klónozott másolatának a tárházban.

1. Futtassa a **set-boríték-uri.cmd** parancsfájlt.

## <a name="run-the-microservices-in-docker"></a>Futtassa a mikroszolgáltatások a Dockerben

A megoldásgyorsító futtatja, lépjen a **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local** mappát a parancssori környezetben, és futtassa az alábbi parancsot:

```cmd\sh
docker-compose up
```

Ez a parancs első futtatásakor Docker tölti le a mikroszolgáltatás-lemezképeket hozhat létre a helyi tárolókat a Docker hubból. Utólagosan, Docker a tárolók a rendszer azonnal futtatja.

Egy külön rendszerhéj használatával a tároló naplóinak megtekintéséhez. Először keresse ki a tárolót azonosító használatával a `docker ps -a` parancsot. Ezután `docker logs {container-id} --tail 1000` a legutóbbi 1000 naplóbejegyzéseket a megadott tároló megtekintéséhez.

A távoli figyelési megoldás irányítópultján eléréséhez keresse meg [ http://localhost:8080 ](http://localhost:8080) a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Felesleges díjak elkerüléséhez, ha befejezte a tesztelést, távolítsa el a cloud services az Azure-előfizetésében. Távolítsa el a szolgáltatásokat a legegyszerűbb módja az, hogy nyissa meg a [az Azure portal](https://ms.portal.azure.com) , és törölje az erőforráscsoportot, amely futtatásakor létrejött a **start.cmd** parancsfájlt.

Használja a `docker-compose down --rmi all` paranccsal távolítsa el a Docker-rendszerképek és szabadítson fel lemezterületet a helyi gépen. A távoli figyelési jön létre, amikor a forráskódját a Githubról klónozott tárház helyi példányának is törölheti.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A helyi fejlesztési környezet beállítása
> * A megoldásgyorsító konfigurálása
> * A megoldásgyorsító üzembe helyezése
> * Jelentkezzen be a megoldásgyorsító

Most, hogy a távoli figyelési megoldás telepített, akkor a következő lépés, hogy [Fedezze fel a megoldás irányítópultján képességeit](quickstart-remote-monitoring-deploy.md).

<!-- Next tutorials in the sequence -->
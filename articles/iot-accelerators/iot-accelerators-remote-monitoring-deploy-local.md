---
title: Helyileg – a távoli figyelési megoldásban üzembe Azure |} A Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogy a távoli figyelési megoldásgyorsító üzembe helyezése a helyi gépen a teszteléshez és fejlesztéshez.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 03/07/2018
ms.topic: conceptual
ms.openlocfilehash: 21bc8c27a44c940279b0c5bdcdbe04e579dc4bfa
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188791"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>A távoli figyelési megoldásgyorsító helyileg üzembe helyezése

Ez a cikk bemutatja, hogyan való teszteléshez és fejlesztéshez a helyi gépre a távoli figyelési megoldásgyorsító üzembe helyezése. Ez a megközelítés egy helyi Docker-tárolót üzembe helyezi a mikroszolgáltatásokat, és használja az IoT Hub, a Cosmos DB és az Azure storage-szolgáltatások a felhőben. A megoldásgyorsítók (személyi számítógépek) CLI az Azure cloud services üzembe helyezéséhez használhatja.

## <a name="prerequisites"></a>Előfeltételek

Az Azure-szolgáltatások használják a távoli figyelési megoldásgyorsító üzembe helyezéséhez aktív Azure-előfizetés szükséges.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

A helyi telepítés befejezéséhez, szüksége van a helyi fejlesztői gépen telepítve van a következő eszközöket:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [A docker compose](https://docs.docker.com/compose/install/)
* [NODE.js](https://nodejs.org/) – ezt a szoftvert a számítógépek CLI előfeltétele.
* SZÁMÍTÓGÉPEK CLI
* Helyi tárház a Forráskód

> [!NOTE]
> Ezek az eszközök számos platformon, beleértve a Windows, Linux- és IOS-es érhetők el.

### <a name="install-the-pcs-cli"></a>A számítógépek parancssori felület telepítése

A számítógépek CLI npm telepítéséhez futtassa a következő parancsot a parancssori környezetben:

```cmd/sh
npm install iot-solutions -g
```

A parancssori felület kapcsolatos további információkért lásd: [a parancssori felület használata](https://github.com/Azure/pcs-cli/blob/master/README.md).

### <a name="download-the-source-code"></a>Letöltheti a forráskódot

 A távoli figyelési forráskód adattára kell letölteni, konfigurálásához és futtatásához, amelyek tartalmazzák a mikroszolgáltatások Docker-rendszerképek Docker konfigurációs fájljait tartalmazza. Klónozza, és hozza létre a tárház helyi verzióját, keresse meg a megfelelő mappát a kedvenc parancssori vagy a terminálban a helyi gépen, és futtassa a következő parancsok egyikét:

A mikroszolgáltatások Java-implementációk telepítéséhez futtassa:

```cmd/sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-java
```

Telepítse a .net-megvalósítások, mikroszolgáltatások, futtassa:

```cmd\sh
git clone --recursive https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet
```

Távoli Monioring előre konfigurált megoldás tárház & almodulok [ [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) | [.Net](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) ]

> [!NOTE]
> Ezek a parancsok letöltheti a forráskódot, az a mikroszolgáltatásokat. Már nincs szüksége a forráskódra futtassa a mikroszolgáltatások a Dockerben, bár a forráskód hasznos, ha később szeretné módosítani az előre konfigurált megoldás és a módosítások helyi tesztelése.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások üzembe helyezése

Bár ez a cikk bemutatja, hogyan a mikroszolgáltatások helyi futtatását, három Azure-szolgáltatások a felhőben futó függenek. Telepítheti az Azure-szolgáltatások [manuálisan az Azure Portalon keresztül](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), vagy a számítógépek CLI használatával. Ez a cikk bemutatja, hogyan használható a `pcs` eszközt.

### <a name="sign-in-to-the-cli"></a>Jelentkezzen be a parancssori felület

A megoldásgyorsító központi telepítése, jelentkezzen be az Azure-előfizetéshez a CLI használatával az alábbiak szerint:

```cmd/sh
pcs login
```

Kövesse a képernyőn megjelenő utasításokat a bejelentkezési folyamat befejezéséhez. Győződjön meg arról, hogy Ön nem kattintson bárhova a belső, a parancssori felület vagy a bejelentkezés sikertelen lehet. Sikeres bejelentkezési üzenet a parancssori felületen látni fogja, ha a bejelentkezés elvégzése. 

### <a name="run-a-local-deployment"></a>Egy helyi központi telepítés futtatása

A következő paranccsal a helyi üzembe helyezésének megkezdéséhez. Ez szükséges azure-erőforrások létrehozását, és nyomtassa ki a környezeti változókat a konzolhoz. 

```cmd/pcs
pcs -s local
```

A parancsfájl kérni fogja, hogy a következő információkat:

* A megoldás nevét.
* A használandó előfizetés.
* Használata az Azure-adatközpont helyét.

> [!NOTE]
> A szkript létrehoz az IoT Hub-példány, egy Cosmos DB-példány és egy Azure storage-fiók egy erőforráscsoportot az Azure-előfizetésében. Az erőforráscsoport neve nem választotta a futtatásakor a megoldás nevére a `pcs` fenti eszköz. 

> [!IMPORTANT]
> A parancsfájl futtatása néhány percet vesz igénybe. Amikor ez befejeződik, megjelenik egy üzenet `Copy the following environment variables to /scripts/local/.env file:`. Másolás a környezet le az üzenet a következő változó definíciók használhatja őket egy későbbi lépésben.

## <a name="run-the-microservices-in-docker"></a>Futtassa a mikroszolgáltatások a Dockerben

Futtassa a mikroszolgáltatások a Dockerben, először szerkessze a **parancsfájlok\\helyi\\.env** fájlt a fenti korábbi lépésben klónozott tárház helyi példányának. Cserélje le a fájl teljes tartalmát a futtatásakor lejegyzett környezeti változó definíciókat a `pcs` parancsot az előző lépésben. Ezeket a környezeti változókat a mikroszolgáltatásokat a Docker-tároló által létrehozott Azure-szolgáltatásokhoz való csatlakozás engedélyezése a `pcs` eszközt.

A megoldásgyorsító futtatja, lépjen a **scripts\local** mappát a parancssori környezetben, és futtassa az alábbi parancsot:

```cmd\sh
docker-compose up
```

Ez a parancs első futtatásakor Docker tölti le a mikroszolgáltatás-lemezképeket hozhat létre a helyi tárolókat a Docker hubból. Utólagosan, Docker a tárolók a rendszer azonnal futtatja.

Egy külön rendszerhéj használatával a tároló naplóinak megtekintéséhez. Először keresse ki a tárolót azonosító használatával a `docker ps -a` parancsot. Ezután `docker logs {container-id} --tail 1000` a legutóbbi 1000 naplóbejegyzéseket a megadott tároló megtekintéséhez.

A távoli figyelési megoldás irányítópultján eléréséhez keresse meg [ http://localhost:8080 ](http://localhost:8080) a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Felesleges díjak elkerüléséhez, ha befejezte a tesztelést, távolítsa el a cloud services az Azure-előfizetésében. Távolítsa el a szolgáltatásokat a legegyszerűbb módja az, hogy nyissa meg a [az Azure portal](https://ms.portal.azure.com) keresztül létrehozott erőforráscsoport törléséhez, és a `pcs` eszközt.

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
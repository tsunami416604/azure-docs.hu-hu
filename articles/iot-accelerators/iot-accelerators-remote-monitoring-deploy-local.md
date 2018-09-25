---
title: Helyileg – a távoli figyelési megoldásban üzembe Azure |} A Microsoft Docs
description: Ez az útmutató bemutatja, hogyan helyezze üzembe a távoli figyelési megoldásgyorsító teszteléshez és fejlesztéshez a helyi gépen.
author: asdonald
manager: timlt
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/17/2018
ms.topic: conceptual
ms.openlocfilehash: 5853730a5e3408e33deb483f6ce6652c1c22efab
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034977"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally"></a>A távoli figyelési megoldásgyorsító helyileg üzembe helyezése

Ez a cikk bemutatja, hogyan való teszteléshez és fejlesztéshez a helyi gépre a távoli figyelési megoldásgyorsító üzembe helyezése. Ez a megközelítés egy helyi Docker-tárolót üzembe helyezi a mikroszolgáltatásokat és a felhőben az IoT Hub, a Cosmos DB és az Azure Time Series Insights-szolgáltatásokat használ.

## <a name="prerequisites"></a>Előfeltételek

Az Azure-szolgáltatások használják a távoli figyelési megoldásgyorsító üzembe helyezéséhez aktív Azure-előfizetés szükséges.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](http://azure.microsoft.com/pricing/free-trial/).

A helyi telepítés befejezéséhez, szüksége van a helyi fejlesztői gépen telepítve van a következő eszközöket:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [A docker compose](https://docs.docker.com/compose/install/)
* [NODE.js v8](https://nodejs.org/) – Ez a szoftver előfeltétele a számítógépek parancssori felület, amely a parancsfájlok használata Azure-erőforrások létrehozásához. Ne használja a Node.js v10.

> [!NOTE]
> Ezek az eszközök számos platformon, beleértve a Windows, Linux- és IOS-es érhetők el.

### <a name="download-the-source-code"></a>Letöltheti a forráskódot

A távoli figyelési GitHub forráskódraktárban kell letölteni, konfigurálásához és futtatásához, amelyek tartalmazzák a mikroszolgáltatások Docker-rendszerképek Docker konfigurációs fájljait tartalmazza. Klónozza, valamint a tárház helyi verzióját használja a parancssori környezetet keresse meg a megfelelő mappát a helyi gépen, és a következő parancsok egyikét futtatja:

Töltse le a legújabb verzióját a Java-mikroszolgáltatások megvalósításokhoz, futtassa:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

Töltse le a legújabb verzióját a .NET mikroszolgáltatás-megvalósítások, futtassa:

```cmd\sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Ezek a parancsok letöltheti a forráskódot, a mikroszolgáltatások helyi futtatásához használja a parancsfájlok mellett a mikroszolgáltatások. Már nincs szüksége a forráskódra futtassa a mikroszolgáltatások a Dockerben, bár a forráskód hasznos, ha később kívánja módosítani a megoldásgyorsító és módosítások helyi tesztelése.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások üzembe helyezése

Bár ez a cikk bemutatja, hogyan helyileg történő futtatása a mikroszolgáltatás-alapú, Azure-szolgáltatások a felhőben futó függenek. Telepítheti az Azure-szolgáltatások [manuálisan az Azure Portalon keresztül](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), vagy parancsfájl. A következő parancsfájl példák feltételezik, hogy egy Windows-gépen használja a .NET-adattárban. Ha egy másik környezetben dolgozik, állítsa be az elérési utak, fájlkiterjesztések, és elérési út elválasztók megfelelően. A megadott parancsfájlok használata:

### <a name="create-new-azure-resources"></a>Új Azure-erőforrások létrehozása

Ha még nem hozott a szükséges Azure-erőforrásokat, kövesse az alábbi lépéseket:

1. A parancssori környezetben, keresse meg a **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\launch** mappájához klónozott másolatának a tárházban.

2. Futtassa a **start.cmd** szkriptet, és kövesse az utasításokat. A parancsfájl kérni fogja, hogy jelentkezzen be az Azure-fiókjával, és indítsa újra a parancsfájlt. A parancsprogram kéri a következő információkat:
    * A megoldás nevét.
    * A használandó előfizetés.
    * Használata az Azure-adatközpont helyét.

    A parancsfájl hoz létre, a megoldás nevű erőforráscsoportot az Azure-ban. Ez az erőforráscsoport tartalmazza az Azure-erőforrások a megoldásgyorsító használja.

3. Miután a parancsfájl futása befejeződött, a környezeti változók listáját jeleníti meg. Kövesse az utasításokat menteni ezeket a változókat, hogy a **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\.env** fájlt.

### <a name="use-existing-azure-resources"></a>A meglévő Azure-erőforrások

Ha már létrehozta a szükséges Azure-erőforrások szerkesztése a környezeti változó definíciókat a **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\.env** fájlt a kívánt értékekkel. A **.env** fájl tartalmaz részletes információkat, hogy hol található a szükséges értékeket.

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

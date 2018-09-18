---
title: Helyileg – a távoli figyelési megoldásban üzembe Azure |} A Microsoft Docs
description: Ez az útmutató bemutatja, hogyan helyezze üzembe a távoli figyelési megoldásgyorsító teszteléshez és fejlesztéshez a helyi gépen.
author: dominicbetts
manager: timlt
ms.author: asdonald
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 09/17/2018
ms.topic: conceptual
ms.openlocfilehash: e29a21639d3d7c7acb6093bcd8eb9947721273bc
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982542"
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
git clone --recurse-submodules  https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
```

Telepítse a .net-megvalósítások, mikroszolgáltatások, futtassa:

```cmd\sh
git clone --recurse-submodules  https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
```

> [!NOTE]
> Ezek a parancsok letöltheti a forráskódot, az a mikroszolgáltatásokat. Már nincs szüksége a forráskódra futtassa a mikroszolgáltatások a Dockerben, bár a forráskód hasznos, ha később szeretné módosítani az előre konfigurált megoldás és a módosítások helyi tesztelése.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások üzembe helyezése

Bár ez a cikk bemutatja, hogyan helyileg történő futtatása a mikroszolgáltatás-alapú, Azure-szolgáltatások a felhőben futó függenek. Telepítheti az Azure-szolgáltatások [manuálisan az Azure Portalon keresztül](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Manual-steps-to-create-azure-resources-for-local-setup), vagy parancsfájl. A következő parancsfájl példák feltételezik, hogy egy Windows-gépen használja a .NET-adattárban. Ha egy másik környezetben dolgozik, állítsa be az elérési utak, fájlkiterjesztések, és elérési út elválasztók megfelelően. A megadott parancsprogram használata:

### <a name="new-users"></a>Új felhasználók
A felhasználók számára, akik rendelkeznek **még nincs** hozta létre a szükséges Azure-erőforrások, folytassa az alábbi lépések végrehajtásával:

 1. A parancssori környezetben, keresse meg a **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\launch** mappájához klónozott másolatának a tárházban.

 2. Futtassa a **start.cmd vagy start.sh** szkriptet, és kövesse az utasításokat. A parancsfájl kérni fogja, hogy a következő információkat:
    * A megoldás nevét.
    * A használandó előfizetés.
    * Használata az Azure-adatközpont helyét.

    A parancsfájl hoz létre, a megoldás nevű erőforráscsoportot az Azure-ban.

 3. A parancssori környezetben, keresse meg a **azure-iot-pcs-remote-monitoring-dotnet\services\scripts\local\launch\os\win** mappájához klónozott másolatának a tárházban.

 4. Futtassa a **set-boríték-uri.cmd vagy a set-boríték-uri.sh** parancsfájlt.
 
 5. Ahhoz, hogy a legújabb azokat a git-almodulok frissítése: ```cd <repo-name>``` , majd futtassa a következő parancsot ```git submodule foreach git pull origin master```

> [!NOTE]
> Azure-iot-pcs-remote-monitoring-dotnet tárház rendelkeznie klónozta, a parancsfájlok mappájába hogy található-e a szolgáltatások submodule (mappa).
A kezdő a szkripthez **Node.js** végrehajtásához, telepítse a legújabb stabil csomópont 8 (nem használja a 10 csomópont) Ez a szkript használata előtt. Emellett ez a szkript szükség lehet felügyeleti jogosultságokkal vagy sudo jogosultsági szerint próbál meg telepíteni [számítógépek – parancssori felület](https://github.com/Azure/pcs-cli) egy parancssori felületen, a remote-monitoring központi telepítések.  

### <a name="existing-users"></a>A meglévő felhasználók
A felhasználók, akik már létrehozta a szükséges azure-erőforrásokat, és csak azokat kell, végezze el csak **egy** a következő lépésekből áll:

 * Állítsa be a környezeti változókat globálisan a gépen.
 * **A VS Code:** az IDE, azaz launch.json indítási konfigurációit a a környezeti változók beállítása
 * **A Visual Studio:** tulajdonságok → hibakeresési → környezeti változók hozzáadásával webszolgáltatás projekt mikroszolgáltatások a környezeti változók beállítása
 
Végül frissítse a git-almodulok ahhoz, hogy a legújabb azokat: ```cd <repo-name>``` , majd futtassa a következő parancsot ```git submodule foreach git pull origin master```
 
Noha nem ajánlott, de a környezeti változók is megadható az egyes mikroszolgáltatások a webszolgáltatás mappában található appsettings.ini fájlban.

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

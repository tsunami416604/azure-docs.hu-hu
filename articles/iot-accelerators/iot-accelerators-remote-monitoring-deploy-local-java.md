---
title: Távoli figyelési megoldás helyi telepítése - IntelliJ IDE - Azure | Microsoft dokumentumok
description: Ez az útmutató bemutatja, hogyan telepítheti a távoli figyelési megoldásgyorsítót a helyi számítógépre az IntelliJ használatával tesztelésre és fejlesztésre.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888813"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>A távfigyelési megoldásgyorsító helyi telepítése - IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ez a cikk bemutatja, hogyan telepítheti a távoli figyelési megoldás gyorsítót a helyi számítógépre tesztelésre és fejlesztésre. Megtudhatja, hogyan kell futtatni a mikroszolgáltatások IntelliJ. A helyi mikroszolgáltatások üzembe helyezése a következő felhőszolgáltatásokat fogja használni: IoT Hub, Azure Cosmos DB, Azure Streaming Analytics és Azure Time Series Insights.

Ha a Docker ben szeretné futtatni a távoli figyelési megoldásgyorsítót a helyi számítógépen, [olvassa el a Távoli figyelési megoldásgyorsító helyi telepítése – Docker című témakört.](iot-accelerators-remote-monitoring-deploy-local-docker.md)

## <a name="prerequisites"></a>Előfeltételek

A távfigyelési megoldásgyorsító által használt Azure-szolgáltatások üzembe helyezéséhez aktív Azure-előfizetésre van szüksége.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés végrehajtásához a következő eszközökre van szükség a helyi fejlesztői gépen:

* [Git](https://git-scm.com/)
* [Docker között](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ közösségi kiadás](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala plugin](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT plugin](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT Végrehajtó plugin](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node.js v8](https://nodejs.org/)

Node.js v8 előfeltétele a PCS CLI, hogy a parancsfájlok azure-erőforrások létrehozásához használt. Ne használja a Node.js v10-et.

> [!NOTE]
> Az IntelliJ IDE Windows és Mac rendszeren érhető el.

## <a name="download-the-source-code"></a>A forráskód letöltése

A távoli figyelés forráskód tárházak tartalmazzák a forráskódot és a Docker konfigurációs fájlokat a Docker-rendszerképek futtatásához.

Klónozni és a tárház helyi verziójának létrehozásához használja a parancssori környezetet a helyi számítógép megfelelő mappájának megugrásához. Ezután futtassa az alábbi parancskészletek egyikét a Java-tárház klónozásához:

* A Java mikroszolgáltatás-implementációk legújabb verziójának letöltéséhez futtassa a következő parancsot:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* A legújabb almodulok beolvasásához futtassa a következő parancsokat:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Ezek a parancsok a mikroszolgáltatások helyi futtatásához használt parancsfájlok mellett az összes mikroszolgáltatás forráskódját is letöltik. Nincs szükség a forráskód a docker-ben a mikroszolgáltatások futtatásához. A forráskód azonban akkor hasznos, ha később módosítani szeretné a megoldásgyorsítót, és helyileg szeretné tesztelni a módosításokat.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások üzembe helyezése

Bár ez a cikk bemutatja, hogyan futtathatja a mikroszolgáltatások helyileg, ezek függenek a felhőben futó Azure-szolgáltatások. Használja a következő parancsfájlt az Azure-szolgáltatások üzembe helyezéséhez. A parancsfájlpéldák feltételezik, hogy a Java-tárházat használja egy Windows-gépen. Ha más környezetben dolgozik, megfelelően módosítsa a görbéket, a fájlkiterjesztéseket és az útvonalelválasztókat.

### <a name="create-new-azure-resources"></a>Új Azure-erőforrások létrehozása

Ha még nem hozta létre a szükséges Azure-erőforrásokat, kövesse az alábbi lépéseket:

1. A parancssori környezetben lépjen a tárház klónozott példányának **\services\scripts\local\launch** mappájába.

1. Futtassa a következő parancsokat a **PCCLI** eszköz telepítéséhez és az Azure-fiókba való bejelentkezéshez:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Futtassa a **start.cmd** parancsfájlt. A parancsfájl a következő információkat kéri:

   * A megoldás neve.
   * A használandó előfizetés.
   * A használandó Azure-adatközpont helye.

   A parancsfájl létrehoz egy erőforráscsoportot az Azure-ban, amely rendelkezik a megoldás neve. Ez az erőforráscsoport a megoldásgyorsító által használt Azure-erőforrásokat tartalmazza. Törölheti ezt az erőforráscsoportot, miután már nincs szüksége a megfelelő erőforrásokra.

   A parancsfájl környezeti változók készletét is hozzáadja a helyi számítógéphez. Minden változónév **pcs előtaggal rendelkezik.** Ezek a környezeti változók olyan részleteket tartalmaznak, amelyek lehetővé teszik a távoli figyelés számára, hogy egy Azure Key Vault-erőforrásból olvassa be a konfigurációs értékeket.

   > [!TIP]
   > Amikor a parancsfájl befejeződik, a környezeti változókat a ** \<kezdőmappa\>\\\\\<.env\>** nevű fájljába menti. Használhatja őket a jövőbeli megoldásgyorsító központi telepítések. Vegye figyelembe, hogy a helyi számítógépen beállított környezeti változók felülírják a **szolgáltatási\\parancsfájlok\\helyi\\.env** fájljának értékeit a **docker-compose futtatásakor.**

1. Zárja be a parancssori környezetet.

### <a name="use-existing-azure-resources"></a>Meglévő Azure-erőforrások használata

Ha már létrehozta a szükséges Azure-erőforrásokat, állítsa be a megfelelő környezeti változókat a helyi számítógépen:
* **PCS_KEYVAULT_NAME**: A Key Vault erőforrás neve.
* **PCS_AAD_APPID**: Az Azure Active Directory (Azure AD) alkalmazásazonosítója.
* **PCS_AAD_APPSECRET**: Az Azure AD-alkalmazás titkos.

A konfigurációs értékeket ebből a Key Vault-erőforrásból olvassa be a rendszer. Ezek a környezeti változók a ** \<saját\>\\mappába\\\<menthetők .pcs megoldás név\>.env** fájlba a központi telepítésből. Vegye figyelembe, hogy a helyi számítógépen beállított környezeti változók felülbírálják a **szolgáltatási\\parancsfájlok helyi\\\\.env** fájljának értékeit a **docker-compose futtatásakor.**

A mikroszolgáltatás által szükséges konfiguráció egy része a Key Vault egy példányában van tárolva, amely a kezdeti üzembe helyezéskor jött létre. A megfelelő változók at a key vault szükség szerint módosítani kell.

## <a name="run-the-microservices"></a>A mikroszolgáltatások futtatása

Ebben a szakaszban futtatja a távoli figyelési mikroszolgáltatások. Ön fut:

* A webes felhasználói felület natív módon.
* Az Azure IoT-eszközszimuláció, az auth és az Azure Stream Analytics Manager-szolgáltatások a Dockerben.
* A mikroszolgáltatások intellij.

### <a name="run-the-device-simulation-service"></a>Az Eszközszimulációszolgáltatás futtatása

Nyisson meg egy új parancssori ablakot. Ellenőrizze, hogy rendelkezik-e hozzáféréssel az előző szakaszban a **start.cmd** parancsfájl által beállított környezeti változókhoz.

Futtassa a következő parancsot az Eszközszimulációs szolgáltatás Docker-tárolójának megnyitásához. A szolgáltatás szimulálja a távoli figyelési megoldás eszközeit.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Az auth szolgáltatás futtatása

Nyisson meg egy új parancssori ablakot, majd futtassa a következő parancsot a Docker-tároló megnyitásához az Auth szolgáltatáshoz. Ezzel a szolgáltatással kezelheti az Azure IoT-megoldások elérésére jogosult felhasználókat.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>A Stream Analytics Manager szolgáltatás futtatása

Nyisson meg egy új parancssori ablakot, majd futtassa a következő parancsot a Stream Analytics-kezelő szolgáltatás Docker-tárolójának megnyitásához. Ezzel a szolgáltatással kezelheti a Stream Analytics-feladatokat. Ezek a felügyelet magában foglalja a feladat konfigurációjának és indításának, leállításának és a feladat állapotának figyelése.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Az összes többi mikroszolgáltatás telepítése a helyi számítógépen

A következő lépések bemutatják, hogyan futtathatja a távfigyelési mikroszolgáltatásokat az IntelliJ-ben.

#### <a name="import-a-project"></a>Projekt importálása

1. Nyissa meg az IntelliJ IDE-t.
1. Válassza **a Project importálása**lehetőséget.
1. Válassza **az azure-iot-pcs-remote-monitoring-java\services\build.sbt lehetőséget.**

#### <a name="create-run-configurations"></a>Futtatási konfigurációk létrehozása

1. Válassza **a Konfigurációk futtatása** > **Edit Configurations**lehetőséget.
1. Válassza **az Új konfiguráció** > **sbt hozzáadása feladat lehetőséget.**
1. Írja be **a Nevet**, majd írja be a **Feladatok** **futtatásként parancsot.**
1. Válassza ki a **munkakönyvtárat** a futtatni kívánt szolgáltatás alapján.
1. A beállítások mentéséhez válassza az**Ok gombra.** **Apply** > 
1. Hozzon létre futtatási konfigurációkat a következő webszolgáltatásokhoz:
    * WebSzolgáltatás (szolgáltatások\config)
    * Webszolgáltatás (szolgáltatások\eszköz-telemetria)
    * WebSzolgáltatás (szolgáltatások\iothub-manager)
    * WebService (szolgáltatások\tárolóadapter)

Az alábbi képen például egy szolgáltatás konfigurációjának hozzáadása látható:

[![Képernyőkép az IntelliJ IDE Futtatás/Hibakeresési beállítások ablakáról, amelyen a bal oldali ablaktáblában a Sbt feladatok listájában kiemelt storageAdapter beállítás, a jobb oldali ablaktábla Név, Feladatok, Munkakönyvtár és VM-paraméterek mezőiben látható a bejegyzés.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Összetett konfiguráció létrehozása

1. Az összes szolgáltatás együttes futtatásához válassza **az Új konfigurációs** > összetett modul hozzáadása**lehetőséget.**
1. Írja be **a Név**parancsot, majd válassza **a Sbt-feladatok hozzáadása**lehetőséget.
1. A beállítások mentéséhez válassza az**Ok gombra.** **Apply** > 

Az alábbi képen például az látható, hogyan adhat hozzá minden sbt-feladatot egyetlen konfigurációhoz:

[![Képernyőkép az IntelliJ IDE futtatása/hibakeresési konfigurációk ablakáról, amelyen a bal oldali ablaktábla Összetett listájában kiemelt AllServices beállítás, a jobb oldali ablaktáblában kiemelt sbt-feladat "deviceTelemetry" beállítás látható.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Válassza a **Futtatás** lehetőséget a webszolgáltatások helyi számítógépen való létrehozásához és futtatásához.

Minden webszolgáltatás megnyit egy parancssorablakot és webböngésző ablakot. A parancssorban a futó szolgáltatás kimenete látható. A böngészőablak lehetővé teszi az állapot figyelése. Ne zárja be a parancssori ablakokat vagy weblapokat, mert ezek a műveletek leállítják a webszolgáltatást.

A szolgáltatások állapotának eléréséhez nyissa meg a következő URL-címeket:

* IoT-Hub kezelő:[http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Eszköz telemetriai adatai:[http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* Config:[http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* tároló-adapter:[http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>A Stream Analytics feladat indítása

A Stream Analytics-feladat elindításához kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portalt.](https://portal.azure.com)
1. Nyissa meg a megoldáshoz létrehozott **Erőforrás csoportot.** Az erőforráscsoport neve az a név, amelyet a **start.cmd** parancsfájl futtatásakor választott a megoldáshoz.
1. Válassza ki a **Stream Analytics feladatot** az erőforrások listájában.
1. A Stream Analytics feladat **áttekintése** lapon válassza a **Start** gombot, majd a Feladat elindításához válassza az **Indítás** lehetőséget.

### <a name="run-the-web-ui"></a>A webes felhasználói felület futtatása

Ebben a lépésben indítsa el a webes felhasználói felületet. Nyisson meg egy új parancssori ablakot. Ellenőrizze, hogy rendelkezik-e a **start.cmd** parancsfájl által beállított környezeti változókkal. Nyissa meg a tárház helyi példányának **webui** mappáját, majd futtassa a következő parancsokat:

```cmd
npm install
npm start
```

Amikor a **start** parancs befejeződött, a böngésző [http://localhost:3000/dashboard](http://localhost:3000/dashboard)megjeleníti az oldalt a címén. A hibák ezen az oldalon várható. Az alkalmazás hibamentes megtekintéséhez hajtsa végre az alábbi lépéseket.

### <a name="configure-and-run-nginx"></a>A Nginx konfigurálása és futtatása

Hozzon létre egy fordított proxykiszolgálót, amely a webalkalmazást a helyi számítógépen futó mikroszolgáltatásokhoz kapcsolja:

1. Másolja a **nginx.conf** fájlt a tárház helyi példányának **webui\scripts\localhost** mappájából a **nginx\conf** telepítési könyvtárba.
1. Futtasd le a Nginx-et.

A Nginx futtatásáról további információt [a Nginx for Windows](https://nginx.org/en/docs/windows.html)című témakörben talál.

### <a name="connect-to-the-dashboard"></a>Csatlakozás az irányítópulthoz

A Távfigyelési megoldás irányítópultjának eléréséhez nyissa meg http://localhost:9000 a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A szükségtelen díjak elkerülése érdekében távolítsa el a felhőszolgáltatásokat az Azure-előfizetéséből a tesztelés befejezése után. A szolgáltatások eltávolításához nyissa meg az [Azure Portalon,](https://ms.portal.azure.com)és törölje a **start.cmd** parancsfájl által létrehozott erőforráscsoportot.

Törölheti is a távoli figyelési tárház helyi példányát, amely akkor jött létre, amikor klónozta a forráskódot a GitHubról.

## <a name="next-steps"></a>További lépések

Most, hogy telepítette a távoli figyelési megoldást, a következő lépés [a megoldásirányítópult képességeinek feltárása.](quickstart-remote-monitoring-deploy.md)

---
title: Távoli figyelési megoldás üzembe helyezése helyileg – IntelliJ IDE – Azure | Microsoft Docs
description: Ez a útmutató bemutatja, hogyan helyezheti üzembe a távoli figyelési megoldás gyorsítása a helyi gépen a IntelliJ használatával tesztelés és fejlesztés céljából.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 779ee1e057d74b11c5e0ba58dc2fd32b803f1e0e
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888813"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>A távoli figyelési megoldás helyi telepítése – IntelliJ

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ebből a cikkből megtudhatja, hogyan helyezheti üzembe a távoli figyelési megoldás Gyorssegédjét a helyi gépre tesztelés és fejlesztés céljából. Megtudhatja, hogyan futtathatja a IntelliJ a Service szolgáltatásait. A helyi fürtszolgáltatások üzembe helyezése a következő felhőalapú szolgáltatásokat fogja használni: IoT Hub, Azure Cosmos DB, Azure streaming Analytics és Azure Time Series Insights.

Ha a távoli figyelési megoldás gyorssegédét a Docker-ben szeretné futtatni a helyi gépen, tekintse meg [a távoli figyelési megoldás központi telepítése helyileg – Docker](iot-accelerators-remote-monitoring-deploy-local-docker.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A távoli figyelési megoldás által használt Azure-szolgáltatások üzembe helyezéséhez aktív Azure-előfizetésre van szükség.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés befejezéséhez a következő eszközökre van szükség a helyi fejlesztési gépen:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [IntelliJ Scala beépülő modul](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ SBT beépülő modul](https://plugins.jetbrains.com/plugin/5007-sbt)
* [IntelliJ SBT végrehajtó beépülő modul](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [Node. js V8](https://nodejs.org/)

A Node. js V8 előfeltétele annak a PC CLI-nek, amelyet a parancsfájlok az Azure-erőforrások létrehozásához használnak. Ne használja a Node. js v10-et.

> [!NOTE]
> A IntelliJ IDE a Windows és a Mac rendszerhez érhető el.

## <a name="download-the-source-code"></a>Forráskód letöltése

A távfelügyelet forráskódja tartalmazza a forráskódot és a Docker konfigurációs fájljait, amelyekre szüksége van a Service Docker-rendszerképek futtatásához.

A tárház helyi verziójának klónozásához és létrehozásához használja a parancssori környezetet, hogy a megfelelő mappába lépjen a helyi gépen. Ezután futtassa a következő parancsok egyikét a Java-tárház klónozásához:

* A Java-szolgáltatások implementációjának legújabb verziójának letöltéséhez futtassa a következő parancsot:

  ```cmd/sh
  git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git
  ```

* A legújabb almodulok beolvasásához futtassa a következő parancsokat:

   ```cmd/sh
   cd azure-iot-pcs-remote-monitoring-java
   git submodule foreach git pull origin master
    ```

> [!NOTE]
> Ezek a parancsok az összes szolgáltatás forráskódját letöltik a helyi webszolgáltatás futtatásához használt parancsfájlokon kívül. Nincs szüksége a forráskód futtatására a Docker szolgáltatásban. A forráskód azonban akkor is hasznos, ha később módosítani szeretné a megoldás-gyorssegédet, és helyileg teszteli a módosításokat.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások üzembe helyezése

Bár ez a cikk bemutatja, hogyan futtathatja helyileg a Service-t, a felhőben futó Azure-szolgáltatásoktól függenek. Az Azure-szolgáltatások üzembe helyezéséhez használja az alábbi szkriptet. A parancsfájl például feltételezi, hogy a Java-tárházat használja a Windows rendszerű gépen. Ha más környezetben dolgozik, állítsa be megfelelően az elérési utakat, a fájlkiterjesztések és az elérési út elválasztóit.

### <a name="create-new-azure-resources"></a>Új Azure-erőforrások létrehozása

Ha még nem hozta létre a szükséges Azure-erőforrásokat, kövesse az alábbi lépéseket:

1. A parancssori környezetben lépjen a tárház klónozott példányának **\services\scripts\local\launch** mappájába.

1. Futtassa a következő parancsokat a **számítógépek** CLI-eszköz telepítéséhez, és jelentkezzen be az Azure-fiókjába:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Futtassa a **Start. cmd** parancsfájlt. A parancsfájl a következő információk megadását kéri:

   * Egy megoldás neve.
   * A használandó előfizetés.
   * A használni kívánt Azure-Adatközpont helye.

   A szkript létrehoz egy erőforráscsoportot az Azure-ban, amelyben a megoldás neve szerepel. Ez az erőforráscsoport tartalmazza a megoldás-gyorsító által használt Azure-erőforrásokat. Ezt az erőforráscsoportot akkor törölheti, ha már nincs szüksége a megfelelő erőforrásokra.

   A parancsfájl környezeti változókat is hozzáad a helyi géphez. Mindegyik változó neve a **számítógépek**előtagját adja meg. Ezek a környezeti változók olyan részleteket biztosítanak, amelyek lehetővé teszik a távoli megfigyelés számára, hogy beolvassák a konfigurációs értékeket egy Azure Key Vault erőforrásból.

   > [!TIP]
   > A parancsfájl befejeződése után a környezeti változókat egy **\<saját mappájába menti\>\\. pcs\\\<-megoldás neve\>. env**. Használhatja őket a jövőbeli megoldás-gyorsító üzembe helyezésekhez. Vegye figyelembe, hogy a helyi gépen beállított környezeti változók felülbírálják a **szolgáltatások\\parancsfájlok\\helyi\\. env** fájljában lévő értékeket a **Docker-összeállítás**futtatásakor.

1. A parancssori környezet lezárása.

### <a name="use-existing-azure-resources"></a>Meglévő Azure-erőforrások használata

Ha már létrehozta a szükséges Azure-erőforrásokat, állítsa be a megfelelő környezeti változókat a helyi gépen:
* **PCS_KEYVAULT_NAME**: a Key Vault erőforrás neve.
* **PCS_AAD_APPID**: a Azure Active Directory (Azure ad) alkalmazás azonosítója.
* **PCS_AAD_APPSECRET**: az Azure ad-alkalmazás titka.

A konfigurációs értékek beolvasása ebből a Key Vault erőforrásból történik. Ezek a környezeti változók menthetők a **\<saját kezdőmappa\>\\. pcs\\\<megoldás neve\>. env** fájl a telepítésből. Vegye figyelembe, hogy a helyi gépen beállított környezeti változók felülbírálják a **szolgáltatások\\parancsfájlok\\helyi\\. env** fájlt a **Docker-összeállítás**futtatásakor.

A webszolgáltatás által igényelt néhány konfiguráció a kezdeti telepítéskor létrehozott Key Vault egy példányában van tárolva. A Key Vault megfelelő változóit szükség szerint módosítani kell.

## <a name="run-the-microservices"></a>A szervizcsomagok futtatása

Ebben a szakaszban a távoli monitorozási szolgáltatásait futtatja. Futtatás:

* A webes KEZELŐFELÜLET natív módon.
* Az Azure IoT-eszköz szimulációja, hitelesítése és Azure Stream Analytics Manager szolgáltatások a Docker-ben.
* A IntelliJ található szolgáltatások.

### <a name="run-the-device-simulation-service"></a>Az eszköz szimulációs szolgáltatásának futtatása

Nyisson meg egy új parancssori ablakot. Győződjön meg arról, hogy van hozzáférése az előző szakaszban található **Start. cmd** parancsfájl által beállított környezeti változókhoz.

A következő parancs futtatásával nyissa meg az eszköz szimulációs szolgáltatásának Docker-tárolóját. A szolgáltatás szimulálja az eszközöket a távoli figyelési megoldáshoz.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>Az Auth szolgáltatás futtatása

Nyisson meg egy új parancssorablakot, majd futtassa a következő parancsot az Auth szolgáltatás Docker-tárolójának megnyitásához. A szolgáltatás használatával felügyelheti azokat a felhasználókat, akik jogosultak az Azure IoT-megoldások elérésére.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-stream-analytics-manager-service"></a>A Stream Analytics Manager szolgáltatás futtatása

Nyisson meg egy új parancssorablakot, majd futtassa a következő parancsot a Docker-tároló megnyitásához a Stream Analytics Manager szolgáltatáshoz. Ezzel a szolgáltatással kezelheti Stream Analytics feladatait. Ilyen felügyelet magában foglalja a feladatok konfigurációjának beállítását, valamint a feladatok állapotának indítását, leállítását és figyelését.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-your-local-machine"></a>Az összes többi szolgáltatás üzembe helyezése a helyi gépen

A következő lépések bemutatják, hogyan futtathatja a távoli monitorozási szolgáltatásait a IntelliJ-ben.

#### <a name="import-a-project"></a>Projekt importálása

1. Nyissa meg a IntelliJ IDE.
1. Válassza a **Projekt importálása**lehetőséget.
1. Válassza a **Azure-IOT-PCs-Remote-Monitoring-java\services\build.SBT**lehetőséget.

#### <a name="create-run-configurations"></a>Futtatási konfigurációk létrehozása

1. Válassza a **futtatás** > a **konfigurációk szerkesztése**lehetőséget.
1. Válassza az **Új konfiguráció hozzáadása** > **SBT feladat**lehetőséget.
1. Adja meg a **nevet**, majd adja meg a **feladatokat** **futtatásként**.
1. Válassza ki a **munkakönyvtárat** a futtatni kívánt szolgáltatás alapján.
1. Válassza az **alkalmaz** > **az OK gombra** a beállítások mentéséhez.
1. Hozzon létre futtatási konfigurációkat a következő webszolgáltatásokhoz:
    * Webszolgáltatás (services\config)
    * Webszolgáltatás (services\device-telemetry)
    * Webszolgáltatás (services\iothub-Manager)
    * Webszolgáltatás (services\storage-adapter)

Az alábbi képen például egy szolgáltatás konfigurációjának hozzáadása látható:

[![képernyőkép a IntelliJ IDE Run/debug konfigurációk ablakáról, amely a bal oldali ablaktábla SBT feladatok listájában, a jobb oldali ablaktáblában pedig a név, a feladatok, a munkakönyvtár és a virtuális gép paraméterek bejegyzései között látható.](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)

#### <a name="create-a-compound-configuration"></a>Összetett konfiguráció létrehozása

1. Az összes szolgáltatás együttes futtatásához válassza az **Új konfiguráció hozzáadása** > **összetett**elemet.
1. Adja meg a **nevet**, majd kattintson a **SBT-feladatok hozzáadása**lehetőségre.
1. Válassza az **alkalmaz** > **az OK gombra** a beállítások mentéséhez.

Az alábbi képen látható, hogyan adhat hozzá az összes SBT-feladatot egyetlen konfigurációhoz:

[![képernyőkép a IntelliJ IDE Run/debug konfigurációk ablakáról, amely a bal oldali ablaktábla összetett listájában, a jobb oldali ablaktáblán pedig a "deviceTelemetry" SBT feladat kiemelését mutatja.](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Válassza a **Futtatás** lehetőséget a webszolgáltatások helyi számítógépen való létrehozásához és futtatásához.

Minden webszolgáltatás megnyílik egy parancssori ablak és egy webböngésző ablak. A parancssorban megjelenik a futó szolgáltatás kimenete. A böngészőablak lehetővé teszi az állapot figyelését. Ne zárjunk be Windows vagy weblapokat, mivel ezek a műveletek leállítják a webszolgáltatást.

A szolgáltatások állapotának eléréséhez nyissa meg a következő URL-címeket:

* IoT-hub Manager: [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Eszköz telemetria: [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* konfiguráció: [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* Storage-adapter: [http://localhost:9022/v1/status](http://localhost:9022/v1/status)

### <a name="start-the-stream-analytics-job"></a>A Stream Analytics-feladatok elindítása

A Stream Analytics feladat elindításához kövesse az alábbi lépéseket:

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
1. Lépjen a megoldáshoz létrehozott **erőforráscsoporthoz** . Az erőforráscsoport neve az a név, amelyet a megoldáshoz választott a **Start. cmd** parancsfájl futtatásakor.
1. Válassza ki a **stream Analytics feladatot** az erőforrások listájában.
1. A Stream Analytics feladatok **Áttekintés** lapján kattintson a **Start** gombra, majd válassza a Start ( **Indítás** ) lehetőséget a feladatok elindításához.

### <a name="run-the-web-ui"></a>A webes felhasználói felület futtatása

Ebben a lépésben elindítja a webes felhasználói felületet. Nyisson meg egy új parancssori ablakot. Győződjön meg arról, hogy van hozzáférése a **Start. cmd** parancsfájl által beállított környezeti változókhoz. Nyissa meg a **WebUI** mappát a tárház helyi példányában, majd futtassa a következő parancsokat:

```cmd
npm install
npm start
```

A **Start** parancs befejezése után a böngésző megjeleníti az oldalt [http://localhost:3000/dashboard](http://localhost:3000/dashboard)címen. A rendszer a lapon szereplő hibákat várta. Ha hiba nélkül szeretné megtekinteni az alkalmazást, hajtsa végre az alábbi lépéseket.

### <a name="configure-and-run-nginx"></a>Az Nginx konfigurálása és futtatása

Állítson be egy fordított proxykiszolgálót, amely a webalkalmazást a helyi gépen futó Service-hez kapcsolja:

1. Másolja az **Nginx. conf** fájlt a **webui\scripts\localhost** mappából a tárház helyi példányában a **nginx\conf** telepítési könyvtárába.
1. Az Nginx futtatása.

További információ az Nginx futtatásáról: [Nginx for Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Kapcsolódás az irányítópulthoz

A távoli figyelési megoldás irányítópultjának eléréséhez nyissa meg a http://localhost:9000t a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A szükségtelen díjak elkerüléséhez távolítsa el a Cloud Servicest az Azure-előfizetésből a tesztelés befejezése után. A szolgáltatások eltávolításához nyissa meg a [Azure Portal](https://ms.portal.azure.com), és törölje a **Start. cmd** parancsfájl által létrehozott erőforráscsoportot.

Törölheti a távoli figyelési tárház helyi példányát is, amely a forráskódnak a GitHubról történő klónozásakor jött létre.

## <a name="next-steps"></a>További lépések

Most, hogy üzembe helyezte a távoli figyelési megoldást, a következő lépés a [megoldás irányítópultjának képességeinek megismerése](quickstart-remote-monitoring-deploy.md).

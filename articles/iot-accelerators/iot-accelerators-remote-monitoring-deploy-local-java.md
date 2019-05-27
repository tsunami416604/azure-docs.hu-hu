---
title: Üzembe helyezése a távoli figyelési megoldás helyileg (keresztül IntelliJ IDE) – Azure |} A Microsoft Docs
description: Ez az útmutató bemutatja, a távoli figyelési megoldásgyorsító üzembe helyezése IntelliJ használatával teszteléshez és fejlesztéshez a helyi gépen.
author: v-krghan
manager: dominicbetts
ms.author: v-krghan
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/24/2019
ms.topic: conceptual
ms.openlocfilehash: 2b55fea69fe1affb6cab5d360f1e8355c3bb720d
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "66015432"
---
# <a name="deploy-the-remote-monitoring-solution-accelerator-locally---intellij"></a>A távoli figyelési megoldásgyorsító helyileg - IntelliJ telepítése

[!INCLUDE [iot-accelerators-selector-local](../../includes/iot-accelerators-selector-local.md)]

Ez a cikk bemutatja, hogyan való teszteléshez és fejlesztéshez a helyi gépre a távoli figyelési megoldásgyorsító üzembe helyezése. Ismerteti a mikroszolgáltatási az intellij-ben való futtatásához. Egy helyi mikroszolgáltatások üzembe helyezés az alábbi cloud-szolgáltatásokat használja: Az IoT Hub, Cosmos DB, az Azure Stream Analytics és az Azure Time Series Insights-szolgáltatások a felhőben.

Ha szeretné a távoli figyelési megoldásgyorsító Futtatás a Dockerben a helyi gépén, [a Docker üzembe helyezése a távoli figyelési megoldásgyorsító helyileg -](iot-accelerators-remote-monitoring-deploy-local-docker.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure-szolgáltatások használják a távoli figyelési megoldásgyorsító üzembe helyezéséhez aktív Azure-előfizetés szükséges.

Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

### <a name="machine-setup"></a>Gép beállítása

A helyi telepítés befejezéséhez, szüksége van a helyi fejlesztői gépen telepítve van a következő eszközöket:

* [Git](https://git-scm.com/)
* [Docker](https://www.docker.com)
* [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [IntelliJ Community Edition](https://www.jetbrains.com/idea/download/)
* [Az IntelliJ beépülő modul Scala](https://plugins.jetbrains.com/plugin/1347-scala)
* [IntelliJ Plugin SBT](https://plugins.jetbrains.com/plugin/5007-sbt)
* [Az IntelliJ beépülő modul SBT végrehajtó](https://plugins.jetbrains.com/plugin/7247-sbt-executor)
* [Nginx](https://nginx.org/en/download.html)
* [NODE.js v8](https://nodejs.org/) – Ez a szoftver előfeltétele a számítógépek parancssori felület, amely a parancsfájlok használata Azure-erőforrások létrehozásához. Ne használja a Node.js v10.

> [!NOTE]
> IntelliJ IDE érhető el a Windows és Mac rendszerre.

## <a name="download-the-source-code"></a>Letöltheti a forráskódot

A távoli figyelési forráskódtárházak forráskódját és a Docker konfigurációs fájlokat kell futtatnia a mikroszolgáltatások Docker-rendszerképeket tartalmazza.

Klónozza, és hozza létre a tárház helyi verzióját, a parancssori környezetet használatával keresse meg a megfelelő mappát a helyi gépen. A következő eljárások egyikét a java-tárház klónozásához parancsok egyikét futtatja:

Töltse le a legújabb verzióját a java-mikroszolgáltatások megvalósításokhoz, futtassa:


```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-java.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-java
git submodule foreach git pull origin master
```

> [!NOTE]
> Ezek a parancsok letöltheti a forráskódot, a mikroszolgáltatások helyi futtatásához használja a parancsfájlok mellett a mikroszolgáltatások. Már nincs szüksége a forráskódra futtassa a mikroszolgáltatások a Dockerben, bár a forráskód hasznos, ha később kívánja módosítani a megoldásgyorsító és módosítások helyi tesztelése.

## <a name="deploy-the-azure-services"></a>Az Azure-szolgáltatások üzembe helyezése

Bár ez a cikk bemutatja, hogyan helyileg történő futtatása a mikroszolgáltatás-alapú, Azure-szolgáltatások a felhőben futó függenek. Használja a következő szkriptet az Azure-szolgáltatások üzembe helyezéséhez. A következő parancsfájl példák feltételezik, hogy használja a java-tárház egy Windows-gépen. Ha egy másik környezetben dolgozik, állítsa be az elérési utak, fájlkiterjesztések, és elérési út elválasztók megfelelően.

### <a name="create-new-azure-resources"></a>Új Azure-erőforrások létrehozása

Ha még nem hozott a szükséges Azure-erőforrásokat, kövesse az alábbi lépéseket:

1. A parancssori környezetben, keresse meg a **\services\scripts\local\launch** mappájához klónozott másolatának a tárházban.

1. Futtassa az alábbi parancsokat a **számítógépek** parancssori eszközt, és jelentkezzen be az Azure-fiókjával:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Futtassa a **start.cmd** parancsfájlt. A parancsfájl kérni fogja, hogy a következő információkat:
   * A megoldás nevét.
   * A használandó előfizetés.
   * Használata az Azure-adatközpont helyét.

     A parancsfájl hoz létre, a megoldás nevű erőforráscsoportot az Azure-ban. Ez az erőforráscsoport tartalmazza az Azure-erőforrások a megoldásgyorsító használja. Ez az erőforráscsoport már nincs szüksége a megfelelő erőforrások után törölheti.

     A parancsfájl is hozzáad egy előtaggal rendelkező környezeti változók készletét **számítógépek** a helyi gépen. Ezek a környezeti változók adja meg a távoli Monitorozáshoz tudják olvasni az Azure Key Vault erőforrás részleteit. A Key Vault erőforrás, ahol távoli megfigyelés fogja beolvasni a konfigurációs értékeket az.

     > [!TIP]
     > Miután a parancsfájl futása befejeződött, azt is menti a környezeti változók nevű fájlba  **\<a kezdőmappa\>\\.pcs\\\<megoldásnevet\>.env** . A jövőbeli megoldás megoldásgyorsító központi telepítések használhatja őket. Vegye figyelembe, hogy a helyi gépen állítsa be a környezeti változók felülbírálási értékeket a **szolgáltatások\\parancsfájlok\\helyi\\.env** fájl futtatásakor **docker-compose**.

1. Lépjen ki a parancssori környezetből.

### <a name="use-existing-azure-resources"></a>A meglévő Azure-erőforrások

Ha már létrehozta a szükséges Azure-erőforrások, a helyi gépén hozzon létre a megfelelő környezeti változókat.
Állítsa be a környezeti változókat a következőket:
* **PCS_KEYVAULT_NAME** – az Azure Key Vault-erőforrás neve
* **PCS_AAD_APPID** – az AAD-Alkalmazásazonosító
* **PCS_AAD_APPSECRET** – az AAD-alkalmazás titkos kulcs

Konfigurációs értékeket az Azure Key Vault erőforrás lesz olvasható. Előfordulhat, hogy menteni ezeket a környezeti változókat a  **\<a kezdőmappa\>\\.pcs\\\<megoldásnevet\>.env** a központi telepítési fájlt. Vegye figyelembe, hogy a helyi gépen beállított környezeti változókat felülbírálási értékeket a **szolgáltatások\\parancsfájlok\\helyi\\.env** fájl futtatásakor **docker-compose**.

Az egyes konfigurációs szükség szerint a mikroszolgáltatás egy példányát tárolja **Key Vault** , amely a kezdeti telepítés jött létre. Igény szerint módosítani kell a megfelelő változók kulcstartóban.

## <a name="run-the-microservices"></a>A mikroszolgáltatások futtatása

Ebben a szakaszban a távoli figyelési mikroszolgáltatások fogja futtatni. A webes felhasználói felület futtatása natív módon, az Eszközszimuláció, hitelesítés és az ASA-kezelő szolgáltatás, a Docker és a mikroszolgáltatás-alapú, az intellij-ben.

### <a name="run-the-device-simulation-service"></a>Eszköz szimulálása szolgáltatás futtatásához

Nyisson meg egy új parancsablakot győződjön meg arról, hogy a által beállított környezeti változókat a hozzáférést a **start.cmd** parancsfájlt az előző szakaszban.

Futtassa a következő parancsot a Docker-tárolót, az eszköz szimulálása szolgáltatás elindításához. A szolgáltatás a távoli figyelési megoldásban az eszközök szimulálja.

```cmd
<path_to_cloned_repository>\services\device-simulation\scripts\docker\run.cmd
```

### <a name="run-the-auth-service"></a>A hitelesítési szolgáltatás

Nyisson meg egy új parancsablakot, és futtassa a következő parancsot a hitelesítési szolgáltatás a Docker-tároló elindításához. A szolgáltatás lehetővé teszi, hogy a felhasználók férhetnek hozzá az Azure IoT-megoldások kezelése.

```cmd
<path_to_cloned_repository>\services\auth\scripts\docker\run.cmd
```

### <a name="run-the-asa-manager-service"></a>Futtassa az ASA-kezelő szolgáltatás

Nyisson meg egy új parancsablakot, és futtassa a következő parancsot a Docker-tároló az ASA-kezelő szolgáltatás elindításához. A szolgáltatás lehetővé teszi, hogy az Azure Stream Analytics (ASA) feladatok, például a konfigurációs beállítást és indítása, leállítása és azok állapotát a figyelési kezelését.

```cmd
<path_to_cloned_repository>\services\asa-manager\scripts\docker\run.cmd
```

### <a name="deploy-all-other-microservices-on-local-machine"></a>A helyi számítógépen az összes többi mikroszolgáltatások üzembe helyezéséhez

A következő lépések bemutatják, hogyan az intellij-ben a távoli figyelési mikroszolgáltatások futtatásához:

#### <a name="import-project"></a>Projekt importálása

1. Indítsa el az intellij-vel IDE
1. Válassza ki **projekt importálása** válassza **azure-iot-pcs-remote-monitoring-java\services\build.sbt**

#### <a name="create-run-configurations"></a>Futtatási beállítások létrehozása

1. Válassza ki **Futtatás > konfigurációk szerkesztése**
1. Válassza ki **adja hozzá az új konfiguráció > sbt feladat** 
1. Adja meg **neve** , és adja meg **feladatok** futtatásakor 
1. Válassza ki a **Working Directory** a futtatni kívánt szolgáltatás alapján
1. Kattintson a **alkalmaz > Ok** menteni a beállításokat.
1. Hozzon létre futtatási konfigurációt a következő szolgáltatásokat:
    * WebService (services\config)
    * Webszolgáltatás (services\device-telemetria)
    * WebService (services\iothub-manager)
    * WebService (services\storage-adapter)

Tegyük fel, az alábbi képen egy szolgáltatás konfiguráció hozzáadása:

[![Add-Configuration](./media/deploy-locally-intellij/run-configurations.png)](./media/deploy-locally-intellij/run-configurations.png#lightbox)


#### <a name="create-compound-configuration"></a>Összetett konfiguráció létrehozása

1. Összes szolgáltatás futtatásához együtt válassza **adja hozzá az új konfiguráció > összetett**
1. Adja meg a **neve** és **sbt tevékenységek hozzáadása**
1. Kattintson a **alkalmaz > Ok** menteni a beállításokat.

Tegyük fel az alábbi képen látható, minden sbt feladatot ad hozzá egyetlen konfigurációs:

[![Add-All-Services](./media/deploy-locally-intellij/all-services.png)](./media/deploy-locally-intellij/all-services.png#lightbox)

Kattintson a **futtatása** létrehozását és futtatását a webes szolgáltatások a helyi gépen.

Minden webszolgáltatáshoz nyitja meg egy parancssort, és a webes böngészőablakban. Parancsot a parancssorba láthatja a futó szolgáltatás, és a böngésző ablakához lehetővé teszi, hogy monitorozhatja az állapotát. Ne zárja be a parancs felszólítja vagy weblapokhoz, ez a művelet leállítja a webszolgáltatást.


Hozzáférhet a szolgáltatások állapotát, a következő URL-címek ugorhat:
* IoT-Hub Manager [http://localhost:9002/v1/status](http://localhost:9002/v1/status)
* Eszköztelemetria  [http://localhost:9004/v1/status](http://localhost:9004/v1/status)
* config [http://localhost:9005/v1/status](http://localhost:9005/v1/status)
* Storage-adapter [http://localhost:9022/v1/status](http://localhost:9022/v1/status)


### <a name="start-the-stream-analytics-job"></a>A Stream Analytics-feladat indítása

Kövesse az alábbi lépéseket a Stream Analytics-feladat indítása:

1. Lépjen az [Azure Portalra](https://portal.azure.com).
1. Keresse meg a **erőforráscsoport** létrehozott a megoldáshoz. Az erőforráscsoport neve nem a választott név a megoldáshoz, ha futtatta a **start.cmd** parancsfájlt.
1. Kattintson a **Stream Analytics-feladat** az erőforrások listájában.
1. A Stream Analytics-feladat a **áttekintése** lap, kattintson a **Start** gombra. Kattintson a **Start** a feladat elindításához.

### <a name="run-the-web-ui"></a>A webes felhasználói felület futtatása

Ebben a lépésben indítsa el a webes felhasználói felületen. Nyisson meg egy új parancsablakot győződjön meg arról, hogy a által beállított környezeti változókat a hozzáférést a **start.cmd** parancsfájlt. Keresse meg a **mire** mappát a helyi tárház másolja, és futtassa a következő parancsokat:

```cmd
npm install
npm start
```

A kezdő befejeződése után a böngésző megjeleníti-e az oldal **http:\//localhost:3000 / irányítópult**. Ezen az oldalon a hibák várhatóan. Hibák nélkül az alkalmazás megtekintéséhez hajtsa végre a következő lépéssel.

### <a name="configure-and-run-nginx"></a>Konfigurálása és futtatása az nginx-et

Állítsa be a fordított proxykiszolgáló mutató hivatkozást a webalkalmazás és a helyi gépen futó mikroszolgáltatásokat:

* Másolás a **nginx.conf** fájlt a **webui\scripts\localhost** a tárház helyi példányának mappájában a **nginx\conf** telepítési könyvtár.
* Futtatás **nginx**.

További információ a futó **nginx**, lásd: [nginx-et a Windows](https://nginx.org/en/docs/windows.html).

### <a name="connect-to-the-dashboard"></a>Csatlakozás az irányítópulton

A távoli figyelési megoldás irányítópultján eléréséhez lépjen a http:\//localhost:9000 a böngészőben.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Kerülje a szükségtelen költségek, a tesztelés befejezése után távolítsa el a cloud services az Azure-előfizetésből. Távolítsa el a szolgáltatásokat, lépjen a [az Azure portal](https://ms.portal.azure.com) és törölje az erőforrást, amely a **start.cmd** létrehozott parancsfájlt.

A távoli figyelési jön létre, amikor a forráskódját a Githubról klónozott tárház helyi példányának is törölheti.

## <a name="next-steps"></a>További lépések

Most, hogy a távoli figyelési megoldás üzembe helyezte, a következő lépés, hogy [Fedezze fel a megoldás irányítópultján képességeit](quickstart-remote-monitoring-deploy.md).

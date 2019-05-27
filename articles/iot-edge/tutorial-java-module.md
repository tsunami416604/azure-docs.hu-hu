---
title: Egyéni Java modul oktatóanyag – Azure IoT Edge |} A Microsoft Docs
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Java-kóddal, és hogyan helyezheti üzembe azt peremhálózati eszközökön.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: a105e59007543ffaf31b586707390954643e8bee
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66239635"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Oktatóanyag: Egy Linux rendszerű eszközök esetében a Java IoT Edge-modul fejlesztése

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. A szimulált IoT Edge-eszköz, amelyet az Azure IoT Edge üzembe helyezése a szimulált eszközön fogja használni [Linux](quickstart-linux.md) rövid. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * IoT Edge Java-modul létrehozása a Visual Studio Code-dal az Azure IoT Edge Maven-sabloncsomag és az Azure IoT Java eszközoldali SDK alapján.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatókör

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy moduljában lévő **Java** használatával **Visual Studio Code**, és hogyan helyezhet üzembe, hogy egy **Linux eszköz**. IoT Edge nem támogatja a Java-modulok Windows-eszközökhöz. 

A következő táblázat segítségével fejleszteni és üzembe helyezni a Java-modulok a lehetőségek megismerése: 

| Java | Visual Studio Code | Visual Studio 2017 | 
| - | ------------------ | ------------------ |
| **Linux-AMD64** | ![A VS Code használata a Linux-AMD64 Java-modulok](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![A VS Code használata a Linux ARM32 Java-modulok](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elkezdéséhez kell elvégezte az előző oktatóanyagban tároló Linux-fejlesztéshez a fejlesztési környezet beállítása: [A Linux rendszerű eszközök IoT Edge-modulok fejlesztése](tutorial-develop-for-linux.md). Ezek az oktatóanyagok egyikét kitöltésével helyen kell rendelkeznie a következő előfeltételek vonatkoznak: 

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* A [Azure IoT Edge-es Linux rendszerű eszközök](quickstart-linux.md)
* Egy tároló-beállításjegyzéket, pl. [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/).
* [A Visual Studio Code](https://code.visualstudio.com/) konfigurálva a [Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [A docker CE](https://docs.docker.com/install/) konfigurált Linux-tárolók futtatásához.

Fejlesztése javában az IoT Edge-modul, a fejlesztői gépen telepítse a következő további előfeltételeket: 

* A Visual Studio Code-hoz készült [Java-bővítménycsomag](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack).
* A [Java SE Development Kit 10](https://aka.ms/azure-jdks), valamint úgy [állítsa be a `JAVA_HOME` környezeti változót](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/), hogy a JDK-telepítésre mutasson.
* [Maven 3](https://maven.apache.org/)

## <a name="create-a-module-project"></a>A modul projekt létrehozása
A következő lépésekkel hozzon létre egy IoT Edge-modul projekt, amely azon alapul, az Azure IoT Edge-maven sabloncsomag és az Azure IoT-Java eszközoldali SDK. A projekt a Visual Studio Code és az Azure IoT-eszközök használatával hoz létre.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy Java-megoldást, amelyet a saját kódjával testreszabhat. 

1. A Visual Studio Code-ban a VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)**  > **Command Palette (Parancskatalógus)** elemet. 

2. A parancskatalógus, adja meg, és futtassa a parancsot **Azure IoT Edge: Új IoT Edge-megoldás**. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válasszon **Java modul**. |
   | Adjon meg értéket a csoportazonosító | Adja meg a csoport azonosító értéket, vagy fogadja el az alapértelmezett **com.edgemodule**. |
   | Provide a module name (Modulnév megadása) | A modul neve **JavaModule**. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tároló rendszerképének a neve, az előző lépésben megadott van töltve. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>Néz ki a végső lemezképtárban \<beállításjegyzék neve\>.azurecr.io/javamodule. |
 
   ![Docker-rendszerkép adattárának megadása](./media/tutorial-java-module/repository.png)
   
Ha most először hoz létre Java-modult, a Maven-csomagok letöltése eltarthat pár percig. Ekkor a VS Code-ablak betölti az IoT Edge-megoldás munkaterületét. A megoldás munkaterület öt legfelső szintű összetevőt tartalmaz. A **modulok** mappa tartalmazza a modul a Java-kódot, valamint a modul egy tárolórendszerképbe, a Docker-fájlok. Az **\.env** fájl a tárolóregisztrációs adatbázis hitelesítő adatait tárolja. A **deployment.template.json** fájl az IoT Edge-futtatókörnyezet által a modulok eszközön való üzembe helyezéséhez használt információkat tartalmazza. És **deployment.debug.template.json** tárolók a hibakeresési verzió-modulok fájlt. Ebben az oktatóanyagban nem fogja szerkeszteni a **\.vscode** mappát vagy a **\.gitignore** fájlt. 

Ha nem adott meg tárolóregisztrációs adatbázist a megoldás létrehozásakor, de elfogadta az alapértelmezett localhost:5000 értéket, akkor nem lesz \.env fájlja. 

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre. 

1. A VS Code Explorerben nyissa meg a .env fájlt. 
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben. 
3. Mentse el ezt a fájlt. 

### <a name="select-your-target-architecture"></a>Válassza ki a cél-architektúra

Jelenleg a Visual Studio Code fejleszthet Java modulok Linux AMD64- és Linux ARM32v7 eszközökhöz. Kell választania, melyik architektúra célozza az egyes megoldások, mert a tároló összeállítása és futtatása eltérően az egyes architektúra. Az alapértelmezett érték a Linux-AMD64. 

1. A parancskatalógus megnyitásához, és keressen rá a **Azure IoT Edge: Állítsa be alapértelmezett célplatform peremhálózati megoldás**, vagy válassza ki a parancsikont a oldalsó sáv az ablak alján. 

2. A parancskatalógus válassza ki a cél-architektúra a lehetőségek listájából. Ebben az oktatóanyagban használjuk egy Ubuntu virtuális gép legyen az IoT Edge-eszköz, így megtartja az alapértelmezett **amd64**. 

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code Explorerben nyissa meg a **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java** modult.

2. Adja hozzá az alábbi kódot a fájl elejéhez új hivatkozott osztályok importálásához.

    ```java
    import java.io.StringReader;
    import java.util.concurrent.atomic.AtomicLong;
    import java.util.HashMap;
    import java.util.Map;
    
    import javax.json.Json;
    import javax.json.JsonObject;
    import javax.json.JsonReader;
    
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Pair;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.Property;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.TwinPropertyCallBack;
    ```

3. Vegye fel a következő definíciót az **App** osztályba. Ez a változó hőmérséklet küszöbértéket állít be. A mért gép hőmérséklet nem jelentik az IoT Hub csak akkor veszíti keresztül ezt az értéket. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

4. A **MessageCallbackMqtt** végrehajtási metódusát cserélje a következő kódra. Ezt a metódust akkor hívja meg a rendszer, amikor a modul MQTT-üzenetet kap az IoT Edge-központtól. Kiszűri a modul ikerdokumentumán keresztül beállított hőmérsékleti határérték alatti hőmérsékleteket jelentő üzeneteket.

    ```java
    protected static class MessageCallbackMqtt implements MessageCallback {
        private int counter = 0;
        @Override
        public IotHubMessageResult execute(Message msg, Object context) {
            this.counter += 1;
 
            String msgString = new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET);
            System.out.println(
                   String.format("Received message %d: %s",
                            this.counter, msgString));
            if (context instanceof ModuleClient) {
                try (JsonReader jsonReader = Json.createReader(new StringReader(msgString))) {
                    final JsonObject msgObject = jsonReader.readObject();
                    double temperature = msgObject.getJsonObject("machine").getJsonNumber("temperature").doubleValue();
                    long threshold = App.tempThreshold.get();
                    if (temperature >= threshold) {
                        ModuleClient client = (ModuleClient) context;
                        System.out.println(
                            String.format("Temperature above threshold %d. Sending message: %s",
                            threshold, msgString));
                        client.sendEventAsync(msg, eventCallback, msg, App.OUTPUT_NAME);
                    }
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            return IotHubMessageResult.COMPLETE;
        }
    }
    ```

5. Adja hozzá az alábbi két statikus belső osztályt az **App** osztályhoz. Ezeket az osztályokat tempThreshold változó frissíteni, amikor az ikermodul kívánt tulajdonságmódosítás. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback {
        @Override
        public void execute(IotHubStatusCode status, Object context) {
            System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
 
    protected static class OnProperty implements TwinPropertyCallBack {
        @Override
        public void TwinPropertyCallBack(Property property, Object context) {
            if (!property.getIsReported()) {
                if (property.getKey().equals(App.TEMP_THRESHOLD)) {
                    try {
                        long threshold = Math.round((double) property.getValue());
                        App.tempThreshold.set(threshold);
                    } catch (Exception e) {
                        System.out.println("Faile to set TemperatureThread with exception");
                        e.printStackTrace();
                    }
                }
            }
        }
    }
    ```

6. Adja hozzá a következő sorokat a **main** metódushoz a **client.open()** után a modul ikerdokumentum-frissítéseire való feliratkozáshoz.

    ```java
    client.startTwin(new DeviceTwinStatusCallBack(), null, new OnProperty(), null);
    Map<Property, Pair<TwinPropertyCallBack, Object>> onDesiredPropertyChange = new HashMap<Property, Pair<TwinPropertyCallBack, Object>>() {
        {
            put(new Property(App.TEMP_THRESHOLD, null), new Pair<TwinPropertyCallBack, Object>(new OnProperty(), null));
        }
    };
    client.subscribeToTwinDesiredProperties(onDesiredPropertyChange);
    client.getTwin();
    ```

7. Mentse az App.java fájlt.

8. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt.

9. Adja hozzá a **JavaModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **moduleContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után: 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Ikermodul központi telepítési sablon hozzáadása](./media/tutorial-java-module/module-twin.png)

10. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-module"></a>És a modul leküldéses

Az előző szakaszban létrehozott egy IoT Edge-megoldás, és hozzáadja a kódot a **JavaModule** üzenetek kiszűréséhez, ahol az a gép jelentett hőmérséklet fogadható alatt van. Most állítsa össze a megoldást, mint egy tárolórendszerképet, és küldheti el azt a tárolóregisztrációs adatbázis. 

1. A VS Code integrált termináljának megnyitásához válassza a **View** (Nézet)  > **Terminal** (Terminál) elemet.

1. Jelentkezzen be a írja be a következő parancsot a terminálon. Jelentkezzen be a felhasználónév, jelszó és az Azure container Registry bejelentkezési kiszolgáló. Kérheti, hogy ezeket az értéteket a **hozzáférési kulcsok** szakasz a tárolójegyzék az Azure Portalon.
     
   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Előfordulhat, hogy kap egy biztonsági figyelmeztetés használatát javasolja `--password-stdin`. Adott ajánlott üzemi forgatókönyvek esetén ajánlott, bár ebben az oktatóanyagban hatókörén kívül esik. További információkért lásd: a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) hivatkozást.

2. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

   A build és a leküldéses parancs három műveletet indítja el. Először létrehoz egy új mappát a megoldásban nevű **config** , amely társítja a teljes üzembe helyezési jegyzékfájl, beépített ki a központi telepítési sablont információk és egyéb megoldást. Másodszor, futtatásuk `docker build` hozhat létre a tárolórendszerképet a cél-architektúra a megfelelő docker-fájl alapján. Ezt követően futtatja `docker push` paranccsal küldje le a tároló-beállításjegyzék a lemezképtárból.

## <a name="deploy-modules-to-device"></a>Eszköz modulok telepítése

A Visual Studio Code Explorerben és az Azure IoT Tools bővítmény használatával a modul projekt telepítése az IoT Edge-eszköz. Már rendelkezik egy manifest nasazení előkészítve a forgatókönyvnek a **deployment.json** config mappában található fájl. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Győződjön meg arról, hogy az IoT Edge-eszköz üzembe helyezéséig.

1. A Visual Studio Code Explorerben bontsa ki a **Azure IoT Hub-eszközök** szakasz az IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot.

3. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt.

4. Kattintson a frissítés gombra. Látható, hogy az új **JavaModule** fut a **TempSensor** modul, valamint az **$edgeAgent** és az **$edgeHub** mellett.  

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

1. A Visual Studio Code Explorerben kattintson a jobb gombbal az IoT Edge-eszköz neve, és válassza ki **Start beépített esemény végpont**.

2. Az IoT Hub érkező üzenetek megjelenítése. Az üzenetek kimarad, míg igénybe vehet, mert az IoT Edge-eszköz kapni az új központi telepítést, és indítsa el a modult. Azt a JavaModule kóddal végzett módosításokat, majd várjon, amíg a gép hőmérséklet 25 fok eléri üzenetek elküldése előtt. Hozzáadja az üzenettípus **riasztási** , az üzenetek, eléri a hőmérséklet-küszöbérték. 

## <a name="edit-the-module-twin"></a>A modul ikereszköz szerkesztése

JavaModule ikermodulja manifest nasazení használjuk a hőmérséklet küszöbérték 25 fok beállítása. Az ikermodul segítségével anélkül, hogy a modul kódot kellene működésének módosításához.

1. A Visual Studio Code bontsa ki a Részletek területen az IoT Edge-eszköz a futó modulok megtekintéséhez. 

2. Kattintson a jobb gombbal **JavaModule** válassza **szerkesztési ikermodul**. 

3. Keresés **TemperatureThreshold** a kívánt tulajdonságait. Módosítsa az értékét egy új hőmérséklet a legújabb jelentett hőmérséklet meghaladja a 10 értéket 5 fok. 

4. Mentse a modul ikereszköz fájlt.

5. Kattintson a jobb gombbal a Szerkesztés panelre és válassza az ikermodul bárhol **frissítés ikermodul**. 

6. A bejövő eszköz a felhőbe irányuló üzenetek figyeléséhez. Az üzenetek, amíg az új hőmérséklet küszöbérték elérése kell megjelennie. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan kódot tartalmazó IoT Edge-modult hozott létre, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll a saját modulok hozhat létre, hogy többet is megtudhat [saját IoT Edge-modulok fejlesztése](module-development.md) , illetve [fejlesztése a Visual Studio Code-dal modulok](how-to-vs-code-develop-module.md). Továbbra is be a következő oktatóanyagok az Azure IoT Edge segítségével üzembe helyezheti az Azure cloud services, a peremhálózaton adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Függvények](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Custom Vision Service](tutorial-deploy-custom-vision.md)

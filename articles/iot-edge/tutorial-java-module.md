---
title: Oktatóanyag – Egyéni Java-modul oktatóanyag az Azure IoT Edge használatával
description: Az oktatóanyag bemutatja, hogyan hozhat létre IoT Edge-modult Java-kóddal, és hogyan helyezheti üzembe azt peremhálózati eszközökön.
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/04/2019
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc
ms.openlocfilehash: 228e50160e5c13b2d24a504b02c4bb7e3a420a46
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76772914"
---
# <a name="tutorial-develop-a-java-iot-edge-module-for-linux-devices"></a>Oktatóanyag: Java IoT Edge-modul fejlesztése Linux-eszközökhöz

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. Az Azure IoT Edge üzembe helyezéséhez létrehozott szimulált IoT Edge-eszközt fogja használni egy szimulált eszközön [linuxos](quickstart-linux.md) gyorsindításban. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * IoT Edge Java-modul létrehozása a Visual Studio Code-dal az Azure IoT Edge Maven-sabloncsomag és az Azure IoT Java eszközoldali SDK alapján.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.

Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="solution-scope"></a>Megoldás hatóköre

Ez az oktatóanyag bemutatja, hogyan fejleszthet ki egy modult **Java** nyelven a **Visual Studio Code használatával,** és hogyan telepítheti **linuxos eszközre.** Az IoT Edge nem támogatja a Java modulokat Windows-eszközökhöz.

Az alábbi táblázat segítségével megismerheti a Java-modulok fejlesztésének és üzembe helyezésének lehetőségeit:

| Java | Visual Studio Code | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux AMD64** | ![Vs Code for Java modulok használata Linux AMD64 rendszeren](./media/tutorial-c-module/green-check.png) |  |
| **Linux ARM32** | ![Vs Code for Java modulok használata Linux ARM32 rendszeren](./media/tutorial-c-module/green-check.png) |  |

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt át kellett volna mennie az előző oktatóanyagon, hogy beállítsa a linuxos tárolók fejlesztéséhez a fejlesztői környezetet: [IoT Edge-modulok fejlesztése Linux-eszközökhöz](tutorial-develop-for-linux.md). Az oktatóanyagok kitöltésével a következő előfeltételeket kell megtöltenie:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban.
* [Azure IoT Edge-et futtató Linux-eszköz](quickstart-linux.md)
* A tároló beállításjegyzék, mint [az Azure Container Registry.](https://docs.microsoft.com/azure/container-registry/)
* Az [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)konfigurált [Visual Studio-kód.](https://code.visualstudio.com/)
* [A Docker CE](https://docs.docker.com/install/) linuxos tárolók futtatására van konfigurálva.

IoT Edge-modul java-beli fejlesztéséhez telepítse a következő további előfeltételeket a fejlesztői gépre: 

* A Visual Studio Code-hoz készült [Java-bővítménycsomag](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack).
* A [Java SE Development Kit 10](https://aka.ms/azure-jdks), valamint úgy [állítsa be a `JAVA_HOME` környezeti változót](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/), hogy a JDK-telepítésre mutasson.
* [Maven](https://maven.apache.org/)

## <a name="create-a-module-project"></a>Modulprojekt létrehozása

A következő lépések hozzon létre egy IoT Edge-modul projekt, amely az Azure IoT Edge maven sabloncsomag és az Azure IoT Java-eszköz SDK alapul. A projektet a Visual Studio Code és az Azure IoT Tools használatával hozhatja létre.

### <a name="create-a-new-project"></a>Új projekt létrehozása

Létrehozhat egy Java-megoldást, amelyet a saját kódjával testreszabhat.

1. A Visual Studio-kód ban válassza a**Parancspaletta** **megtekintése** > lehetőséget a VS Code parancspaletta megnyitásához.

2. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot. Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Select folder (Mappa kiválasztása) | Válassza ki azt a helyet a fejlesztői gépen, ahol a VS Code létre fogja hozni a megoldásfájlokat. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldásnak, vagy fogadja el az alapértelmezett **EdgeSolution nevet.** |
   | Select module template (Modulsablon kiválasztása) | Válassza a **Java modul lehetőséget.** |
   | Érték megadásáa a groupId-hoz | Adjon meg egy csoportazonosító értéket, vagy fogadja el az alapértelmezett **com.edgemodule modult.** |
   | Provide a module name (Modulnév megadása) | Nevezze el a modul **JavaModule**. |
   | Provide Docker image repository for the module (Docker-rendszerkép adattárának megadása a modulhoz) | Egy rendszerképadattár a tárolóregisztrációs adatbázis nevét és a tárolórendszerkép nevét tartalmazza. A tárolórendszerkép előre ki van töltve az utolsó lépésben megadott névből. Cserélje le a **localhost:5000** értéket az Azure-beli tárolóregisztrációs adatbázis bejelentkezési kiszolgálójának értékére. A bejelentkezési kiszolgálót a tárolóregisztrációs adatbázis Áttekintés lapján kérheti le az Azure Portalon. <br><br>A végső képtár \<úgy\>néz ki, mint a rendszerleíró adatbázis neve .azurecr.io/javamodule. |

   ![Docker-rendszerkép adattárának megadása](./media/tutorial-java-module/repository.png)

Ha ez az első alkalom, hogy java modult hoz létre, több percig is eltarthat a maven csomagok letöltése. Amikor a megoldás készen áll, a VS-kód ablak betölti az IoT Edge-megoldás munkaterületét. A megoldásmunkaterület öt legfelső szintű összetevőt tartalmaz:

* A **modulok mappája** tartalmazza a modul Java-kódját és a Docker-fájlokat a modul tárolórendszerképként való létrehozásához.
* Az ** \.env** fájl tárolja a tároló rendszerleíró hitelesítő adatait.
* A **deployment.template.json** fájl az IoT Edge-futtatókörnyezet által a modulok eszközön való üzembe helyezéséhez használt információkat tartalmazza.
* A **deployment.debug.template.json** fájl a modulok hibakeresési verzióját tárolja.
* Ebben az oktatóanyagban nem fogja szerkeszteni a ** \.vscode** mappát vagy ** \.a gitignore** fájlt.

Ha nem adott meg tárolóregisztrációs adatbázist a megoldás létrehozásakor, de elfogadta az alapértelmezett localhost:5000 értéket, akkor nem lesz \.env fájlja.

### <a name="add-your-registry-credentials"></a>A regisztrációs adatbázis hitelesítő adatainak hozzáadása

A környezeti fájl tárolja a tárolóregisztrációs adatbázis hitelesítő adatait, és megosztja őket az IoT-Edge futtatókörnyezettel. A futtatókörnyezetnek szüksége van ezekre a hitelesítő adatokra a privát rendszerképek letöltéséhez az IoT Edge-eszközre.

1. A VS Code Explorerben nyissa meg a .env fájlt.
2. Adja meg az Azure Container Registryből kimásolt **felhasználónevet** és **jelszót** a megfelelő mezőkben.
3. Mentse el ezt a fájlt.

### <a name="select-your-target-architecture"></a>Válassza ki a célarchitektúrát

Jelenleg a Visual Studio Code java modulokat fejleszthet Linux AMD64 és Linux ARM32v7 eszközökhöz. Ki kell választania, hogy az egyes megoldásokkal melyik architektúrát célozza meg, mert a tároló az egyes architektúratípusokhoz másképp en fut. Az alapértelmezett érték a Linux AMD64.

1. Nyissa meg a parancspalettát, és keressen az **Azure IoT Edge: Set Default Target Platform for Edge Solution (Alapértelmezett célplatform az edge-megoldáshoz) kifejezésre,** vagy válassza ki az ablak alján lévő oldalsávon található parancsikont.

2. A parancspalettán válassza ki a célarchitektúrát a lehetőségek listájából. Ebben az oktatóanyagban egy Ubuntu virtuális gépet használunk IoT Edge eszközként, így megtartja az alapértelmezett **amd64-et.**

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code explorer, nyitott **modulok** > **JavaModule** > **src** > **fő** > **java** > **com** > **edgemodule** > **App.java**.

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

3. Vegye fel a következő definíciót az **App** osztályba. Ez a változó hőmérsékleti küszöbértéket állít be. A mért gép hőmérséklete nem lesz jelentve az IoT Hub, amíg nem megy át ezt az értéket.

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

5. Adja hozzá az alábbi két statikus belső osztályt az **App** osztályhoz. Ezek az osztályok frissítik a tempThreshold változót, amikor az ikermodul kívánt tulajdonsága megváltozik. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

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

8. A VS Code explorer, nyissa meg a **deployment.template.json** fájlt az IoT Edge-megoldás munkaterületén.

9. Adja hozzá a **JavaModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **moduleContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után:

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Ikermodul hozzáadása a telepítési sablonhoz](./media/tutorial-java-module/module-twin.png)

10. Mentse a deployment.template.json fájlt.

## <a name="build-and-push-your-module"></a>Építsd meg és nyomd meg a modult

Az előző szakaszban létrehozott egy IoT Edge-megoldást, és kódot adott hozzá a **JavaModule-hoz** az üzenetek kiszűréséhez, ahol a jelentett géphőmérséklet az elfogadható határérték alatt van. Most készítse el a megoldást tárolórendszerképként, és nyomja le a tároló rendszerleíró adatbázisába.

1. Nyissa meg a VS Code integrált terminált a **Terminál megtekintése** > **Terminal**lehetőséget választva.

1. Jelentkezzen be a Dockerbe a következő parancs megadásával a terminálon. Jelentkezzen be a felhasználónévvel, a jelszóval és a bejelentkezési kiszolgálóval az Azure-tároló beállításjegyzékéből. Ezeket az értékeket az Azure Portalon a beállításjegyzék **Access-kulcsok** szakaszából kérheti le.

   ```bash
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   A használatát a használatát `--password-stdin`javasolja. Bár ez az ajánlott eljárás éles forgatókönyvekhez ajánlott, ez kívül esik az oktatóanyag hatókörén. További információkért tekintse meg a [docker bejelentkezési](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) referencia.

1. A VS Code Explorerben kattintson a jobb gombbal a **deployment.template.json** fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget.

   A build- és leküldéses parancs három műveletet indít el. Először létrehoz egy új mappát a megoldás nevű **config,** amely tartalmazza a teljes központi telepítési jegyzékfájl, amely a telepítési sablonban és más megoldásfájlokban lévő információkból épül fel. Másodszor, fut `docker build` a tárolórendszerkép létrehozása a célarchitektúrának megfelelő dockerfile alapján. Ezután fut `docker push` a rendszerkép-tárház a tároló rendszerleíró adatbázisba.

## <a name="deploy-modules-to-device"></a>Modulok telepítése az eszközre

A Visual Studio Code Explorer és az Azure IoT Tools bővítmény segítségével telepítse a modulprojektet az IoT Edge-eszközre. Már rendelkezik egy központi telepítési jegyzékfájllal a forgatókönyvhöz, a **deployment.json** fájlhoz a konfigurációs mappában. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

Győződjön meg arról, hogy az IoT Edge-eszköz működik.

1. A Visual Studio Kódkezelőben bontsa ki az **Azure IoT Hub-eszközök** szakaszt az IoT-eszközök listájának megtekintéséhez.

2. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot.

3. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt.

4. Kattintson a frissítés gombra. Meg kell jelennie az új **JavaModule** futó együtt **SimulatedTemperatureSensor** modul és a **$edgeAgent** és **$edgeHub**.  

## <a name="view-the-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak.

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája.

1. A Visual Studio Kódkezelőjében kattintson a jobb gombbal az IoT Edge-eszköz nevére, és válassza **a Beépített eseményvégpont indítása parancsot.**

2. Tekintse meg az IoT Hubhoz érkező üzeneteket. Eltarthat egy ideig, amíg az üzenetek megérkeznek. Az IoT Edge-eszköz nek meg kell kapnia az új központi telepítést, és el kell indítania az összes modult. Ezután a JavaModule kód módosításai megvárják, amíg a gép hőmérséklete eléri a 25 fokot, mielőtt üzeneteket küldene. Emellett hozzáadja a **Riasztás üzenettípust** minden olyan üzenethez, amely eléri az adott hőmérsékleti küszöbértéket.

## <a name="edit-the-module-twin"></a>A modul szerkesztése iker

A Telepítési jegyzékben a JavaModule modul ikermodulját használtuk a hőmérsékleti küszöbérték 25 fokos beállításához. Az ikermodul segítségével a modulkód frissítése nélkül módosíthatja a funkciót.

1. A Visual Studio-kódban bontsa ki a részleteket az IoT Edge-eszköz alatt a futó modulok megtekintéséhez.

2. Kattintson a jobb gombbal a **JavaModule elemre,** és válassza **az Ikermodul szerkesztése parancsot.**

3. Keresse **meg a TemperatureThreshold** tulajdonságot a kívánt tulajdonságokban. Változtassa meg az értékét egy új hőmérséklet 5 fokkal 10 fokkal magasabb, mint a legutóbbi jelentett hőmérséklet.

4. Mentse a modul ikerfájlját.

5. Kattintson a jobb gombbal a modul ikerszerkesztő ablaktáblájának tetszőleges pontjára, és válassza **az Ikermodul frissítése parancsot**.

6. Figyelje a bejövő eszközről a felhőbe irányuló üzeneteket. Az új hőmérsékleti küszöbérték eléréséig meg kell jelennie az üzeneteknek.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként.

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy IoT Edge-modult, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Ha készen áll saját modulok készítésére, többet is megtudhat az [IoT Edge-modulok fejlesztéséről,](module-development.md) illetve [arról, hogyan fejleszthet modulokat a Visual Studio-kóddal.](how-to-vs-code-develop-module.md) Tekintse meg az [IoT Edge modul minták](https://github.com/Azure/iotedge/tree/master/edge-modules) kódpéldák, beleértve a szimulált hőmérséklet modul.

Folytassa a következő oktatóanyaggal, és ismerje meg, hogy az Azure IoT Edge hogyan segíti az Azure felhőszolgáltatásainak üzembe helyezését a peremhálózaton lévő adatok feldolgozásához és elemzéséhez.

> [!div class="nextstepaction"]
> [Functions](tutorial-deploy-function.md)
> [Stream Analytics](tutorial-deploy-stream-analytics.md)
> [Machine Learning](tutorial-deploy-machine-learning.md)
> [Egyéni vision szolgáltatás](tutorial-deploy-custom-vision.md)

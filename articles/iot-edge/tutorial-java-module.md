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
ms.openlocfilehash: f654f33fe03b29a3aa93386d49e8f5a43cffc9c8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59493042"
---
# <a name="tutorial-develop-a-java-iot-edge-module-and-deploy-to-your-simulated-device"></a>Oktatóanyag: A Java IoT Edge-modul fejlesztése és üzembe helyezése a szimulált eszköz

Az Azure IoT Edge-modulokkal olyan kódot helyezhet üzembe, amely közvetlenül az IoT Edge-eszközökön implementálja az üzleti logikát. Ez az oktatóanyag végigvezeti az érzékelőktől kapott adatokat szűrő IoT Edge-modul létrehozásának és üzembe helyezésének lépésein. A szimulált IoT Edge-eszköz, amelyet az Azure IoT Edge üzembe helyezése a szimulált eszközön fogja használni [Linux](quickstart-linux.md) rövid. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:    

> [!div class="checklist"]
> * IoT Edge Java-modul létrehozása a Visual Studio Code-dal az Azure IoT Edge Maven-sabloncsomag és az Azure IoT Java eszközoldali SDK alapján.
> * Docker-rendszerkép létrehozása és közzététele a regisztrációs adatbázisba a Visual Studio Code és a Docker használatával.
> * A modul üzembe helyezése az IoT Edge-eszközön.
> * A létrejött adatok megtekintése.


Az ebben az oktatóanyagban létrehozott IoT Edge-modul szűri az eszköze által létrehozott hőmérsékletadatokat. Csak akkor küld felfelé irányuló üzeneteket, amikor a hőmérséklet egy megadott küszöbérték felett van. Ez a fajta peremhálózati elemzés a felhőbe küldött és ott tárolt adatok mennyiségének csökkentésére használható. 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek

Egy Azure IoT Edge-eszköz:

* Használhatja az Azure virtuális gép IoT Edge-eszköz esetében ez a rövid útmutató lépéseit követve [Linux](quickstart-linux.md). 
* Az IoT Edge-hez a Java-modulok nem támogatja a Windows-tárolók. 

Felhőerőforrások:

* Egy ingyenes vagy standard szintű [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-ban. 

Fejlesztési erőforrások:

* [Visual Studio Code](https://code.visualstudio.com/). 
* A Visual Studio Code-hoz készült [Java-bővítménycsomag](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack).
* [Az Azure IoT-eszközök](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) a Visual Studio Code. 
* A [Java SE Development Kit 10](https://aka.ms/azure-jdks), valamint úgy [állítsa be a `JAVA_HOME` környezeti változót](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/), hogy a JDK-telepítésre mutasson.
* [Maven 3](https://maven.apache.org/)
* [Docker CE](https://docs.docker.com/install/).
   * Ha Windows-eszközön végzi a fejlesztést, ellenőrizze, hogy a Docker [konfigurálva van-e Linux-tárolók használatára](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers). 


## <a name="create-a-container-registry"></a>Tároló-beállításjegyzék létrehozása

Ebben az oktatóanyagban használhatja az Azure IoT-eszközök a Visual Studio Code fel egy modult, és hozzon létre egy **tárolórendszerkép** a fájlokból. Ezután ezt a rendszerképet leküldi a rendszerképeit tároló és felügyelő **beállításjegyzékbe**. Végül üzembe helyezi a rendszerképet a beállításjegyzékből az IoT Edge-eszközön való futtatáshoz.  

Minden olyan Docker-kompatibilis beállításjegyzéket a tárolólemezképek tárolására használható. Két népszerű Docker beállításjegyzék szolgáltatások [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) és [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags). Ez az oktatóanyag az Azure Container Registryt használja. 

Ha még nem rendelkezik egy tároló-beállításjegyzéket, az alábbi lépésekkel hozzon létre egy új Azure-ban:

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Erőforrás létrehozása** > **Tárolók** > **Container Registry** elemet.

2. Adja meg a következő értékeket a tárolóregisztrációs adatbázis létrehozásához:

   | Mező | Érték | 
   | ----- | ----- |
   | Beállításjegyzék neve | Egyedi nevet adjon meg. |
   | Előfizetés | A legördülő listából válasszon egy előfizetést. |
   | Erőforráscsoport | Egyszerűbb felügyeletre használja ugyanazt az erőforráscsoportot az összes teszt erőforrást, amely során az IoT Edge útmutatóink és oktatóanyagaink segítségével hoz létre. Például: **IoTEdgeResources**. |
   | Hely | Válassza ki az Önhöz legközelebb eső helyet. |
   | Rendszergazdai felhasználó | Állítsa **Engedélyezés** értékre. |
   | SKU | Válassza az **Alapszintű** lehetőséget. | 

5. Kattintson a **Létrehozás** gombra.

6. Miután létrejött a tárolóregisztrációs adatbázis, keresse meg, majd válassza a **Hozzáférési kulcsok** elemet. 

7. Másolja a **Bejelentkezési kiszolgáló**, a **Felhasználónév** és a **Jelszó** értékeit. A tároló-beállításjegyzékbe való hozzáférés biztosításához az oktatóanyag későbbi részében használja ezeket az értékeket. 

## <a name="create-an-iot-edge-module-project"></a>IoT Edge-modulprojekt létrehozása
A következő lépésekkel hozzon létre egy IoT Edge-modul projekt, amely azon alapul, az Azure IoT Edge-maven sabloncsomag és az Azure IoT-Java eszközoldali SDK. A projekt a Visual Studio Code és az Azure IoT-eszközök használatával hoz létre.

### <a name="create-a-new-solution"></a>Új megoldás létrehozása

Létrehozhat egy Java-megoldást, amelyet a saját kódjával testreszabhat. 

1. A Visual Studio Code-ban a VS Code parancskatalógusának megnyitásához válassza a **View (Nézet)** > **Command Palette (Parancskatalógus)** elemet. 

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

### <a name="update-the-module-with-custom-code"></a>A modul módosítása egyéni kóddal

1. A VS Code Explorerben nyissa meg a **modules** > **JavaModule** > **src** > **main** > **java** > **com** > **edgemodule** > **App.java** modult.

5. Adja hozzá az alábbi kódot a fájl elejéhez új hivatkozott osztályok importálásához.

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

5. Vegye fel a következő definíciót az **App** osztályba. Ez a változó hőmérséklet küszöbértéket állít be. A mért gép hőmérséklet nem jelentik az IoT Hub csak akkor veszíti keresztül ezt az értéket. 

    ```java
    private static final String TEMP_THRESHOLD = "TemperatureThreshold";
    private static AtomicLong tempThreshold = new AtomicLong(25);
    ```

7. A **MessageCallbackMqtt** végrehajtási metódusát cserélje a következő kódra. Ezt a metódust akkor hívja meg a rendszer, amikor a modul MQTT-üzenetet kap az IoT Edge-központtól. Kiszűri a modul ikerdokumentumán keresztül beállított hőmérsékleti határérték alatti hőmérsékleteket jelentő üzeneteket.

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

8. Adja hozzá az alábbi két statikus belső osztályt az **App** osztályhoz. Ezeket az osztályokat tempThreshold változó frissíteni, amikor az ikermodul kívánt tulajdonságmódosítás. Minden modul rendelkezik saját ikerdokumentummal, amelyekkel közvetlenül a felhőből konfigurálhatja a modulban futó kódot.

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

9. Adja hozzá a következő sorokat a **main** metódushoz a **client.open()** után a modul ikerdokumentum-frissítéseire való feliratkozáshoz.

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

11. Mentse az App.java fájlt.

12. A VS Code Explorerben az IoT Edge-megoldás munkaterületén nyissa meg a **deployment.template.json** fájlt. A fájl arra utasítja az IoT Edge-ügynök, mely modulok üzembe helyezéséhez és az IoT Edge hubot jelzi, hogyan irányítsa az üzenetek közöttük. Ebben az esetben a két modulokra **tempSensor** és **JavaModule**. A Visual Studio Code-bővítmény automatikusan kitölti a legtöbb, hogy a központi telepítési sablont a szükséges, de győződjön meg arról, hogy minden működik-e a megoldás pontos információ: 

   1. Az IoT Edge az alapértelmezett platform értékre van állítva **amd64** a VS Code állapotsorban, ami azt jelenti, a **JavaModule** a lemezkép verziószámát Linux AMD64-es értékre van állítva. Módosítsa az alapértelmezett platform az állapotsorban **amd64** való **arm32v7** , amely az IoT Edge-eszköz architektúra esetén. 

      ![A modul lemezképplatformmal frissítése](./media/tutorial-java-module/image-platform.png)

   2. Ellenőrizze, hogy a sablonban a megfelelő modulnév van-e megadva az alapértelmezett **SampleModule** név helyett, amelyet az IoT Edge-megoldás létrehozásakor módosított.

   3. A **registryCredentials** szakasz tárolja a Docker registry hitelesítő adataival, úgy, hogy az IoT Edge-ügynök a modul rendszerképének kérheti le. A tényleges felhasználónév–jelszó párokat az .env fájl tárolja, amelyet a Git figyelmen kívül hagy. Ha még nem tette, vegye fel a hitelesítő adatait a .env fájlba.  

   4. Ha szeretne további tudnivalók a központi telepítési jegyzékek, [megtudhatja, hogyan helyezhet üzembe modulokat, és ezekkel létesíthetnek útvonalat az IoT Edge](module-composition.md).

13. Adja hozzá a **JavaModule** modul ikerdokumentumát az üzembehelyezési jegyzékhez. Szúrja be a következő JSON-tartalmat a **moduleContent** szakasz alján, az **$edgeHub** modul ikerdokumentuma után: 

   ```json
     "JavaModule": {
         "properties.desired":{
             "TemperatureThreshold":25
         }
     }
   ```

   ![Ikermodul központi telepítési sablon hozzáadása](./media/tutorial-java-module/module-twin.png)

14. Mentse a deployment.template.json fájlt.

## <a name="build-your-iot-edge-solution"></a>Az IoT Edge-megoldás összeállítása

Az előző szakaszban létrehozott egy IoT Edge-megoldás, és hozzáadja a kódot a **JavaModule** üzenetek kiszűréséhez, ahol az a gép jelentett hőmérséklet fogadható alatt van. Most állítsa össze a megoldást, mint egy tárolórendszerképet, és küldheti el azt a tárolóregisztrációs adatbázis. 

1. Jelentkezzen be a következő parancs beírásával a terminál a Visual Studio Code. Ezután küldje le a modul rendszerképét az Azure Container Registry-adatbázisba.
     
   ```csh/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```
   Használja az első szakaszban az Azure Container Registry-adatbázisból kimásolt felhasználónevet, jelszót és bejelentkezési kiszolgálót. Vagy le is kérheti ezeket az értékeket az Azure Portalon a tárolóregisztrációs adatbázis **Hozzáférési kulcsok** szakaszában.

2. A VS Code Explorerben kattintson a jobb gombbal a deployment.template.json fájlra, és válassza a **Build and Push IoT Edge solution** (IoT Edge-megoldás összeállítása és leküldése) lehetőséget. 

Amikor a megoldás összeállítására utasítja a Visual Studio Code-ot, az elsőként lekéri az adatokat az üzembehelyezési sablonból, és létrehoz egy deployment.json nevű fájlt egy új **config** nevű mappában. Ezután futtatja a következő két parancsot az integrált terminálon: `docker build` és `docker push`. A két parancs létrehozza a kódot, tárolóba helyezi a Java-alkalmazást, majd leküldi a kódot a megoldás inicializálásakor megadott tárolóregisztrációs adatbázisba. 

A VS Code integrált termináljában láthatja a teljes tárolórendszerképet címkével együtt. A rendszerkép címe a module.json fájlban lévő információk alapján áll össze az \<adattár\>:\<verzió\>-\<platform\> formátumban. Ebben az oktatóanyagban a következőképpen kell kinéznie: registryname.azurecr.io/javamodule:0.0.1-amd64.

>[!TIP]
>Ha hibaüzenet jelenik meg, és a modul leküldéses próbál, győződjön meg arról, a következő ellenőrzések:
>* Jelentkezett be a Docker, a Visual Studio Code hitelesítő adatok használatával a tárolóregisztrációs adatbázisból? Ezeket a hitelesítő adatokat eltérnek az Azure Portalra való bejelentkezéshez használja azokat.
>* A tároló-beállításjegyzékbe a helyes? Nyissa meg **modulok** > **JavaModule** > **module.json** , és keresse meg a **tárház** mező. A lemezképtár hasonlóan kell kinéznie  **\<registryname\>.azurecr.io/javamodule**. 
>* Tárolók rendszert futtató fejlesztői gépen azonos típusú készít? Visual Studio Code-ot alapértelmezett Linux-tárolók amd64. A fejlesztői gépén arm32v7 Linux-tárolók fut, a platform a tárolóplatform megfelelően a VS Code-ablak alján kék állapotsávját frissíteni.
>* Az IoT Edge-hez a Java-modulok nem támogatja a Windows-tárolók.

## <a name="deploy-and-run-the-solution"></a>A megoldás üzembe helyezése és futtatása

Az IoT Edge-eszköz beállításához használt rövid útmutatóban egy modult helyezett üzembe az Azure Portal segítségével. Az Azure IoT Hub-eszközkészlet bővítmény (korábbi nevén Azure IoT-eszközkészlet bővítmény) használata a Visual Studio Code-modulok is telepítheti. Már elő van készítve egy üzembehelyezési jegyzék a forgatókönyvhöz, a **deployment.json** fájl. Most csak ki kell választania az üzemelő példányt fogadó eszközt.

1. Futtassa a parancsot a VS Code parancskatalógus **Azure: Jelentkezzen be a** , és kövesse az utasításokat követve jelentkezzen be az Azure-fiókjával. Ha már be van jelentkezve, ezt a lépést kihagyhatja.

2. Futtassa a VS Code parancskatalógus **Azure IoT hubbal: Válassza ki az IoT Hub**. 

3. Válassza ki a konfigurálni kívánt IoT Edge-eszközt tartalmazó előfizetést és IoT Hubot. 

4. A VS Code Explorerben bontsa ki az **Azure IoT Hub Devices** (Azure IoT Hub-eszközök) szakaszt. 

5. Kattintson a jobb gombbal az IoT Edge-eszköz nevére, majd válassza a **Create Deployment for Single Device** (Üzembe helyezés létrehozása egyetlen eszközhöz) parancsot. 

   ![Üzemelő példány létrehozása egyetlen eszközhöz](./media/tutorial-java-module/create-deployment.png)

6. Válassza ki a **deployment.json** fájlt a **config** mappában, majd kattintson a **Select Edge Deployment Manifest** (Edge üzembehelyezési jegyzék kiválasztása) elemre. Ne használja a deployment.template.json fájlt. 

7. Kattintson a frissítés gombra. Látható, hogy az új **JavaModule** fut a **TempSensor** modul, valamint az **$edgeAgent** és az **$edgeHub** mellett.  

## <a name="view-generated-data"></a>A létrejött adatok megtekintése

Miután alkalmazta az üzembehelyezési jegyzéket az IoT Edge-eszközén, az eszköz IoT Edge-futtatókörnyezet összegyűjti az új környezettel kapcsolatos információkat, és megkezdi a végrehajtást. Az eszközön minden olyan futó modul leáll, amely nem szerepel az üzembehelyezési jegyzékben. Az eszközről hiányzó modulok elindulnak. 

Az IoT Edge-eszköz állapotát a Visual Studio Code Explorer **Azure IoT Hub-eszközök** szakaszában tekintheti meg. Bontsa ki az eszköz részleteit, és megjelenik a telepített és a futó modulok listája. 

Az IoT Edge-eszközön láthatja a telepítési modulok állapotát a parancs segítségével `iotedge list`. Négy modult kell látnia: a két IoT Edge-futtatókörnyezeti modult, a tempSensort és az ebben az oktatóanyagban létrehozott egyéni modult. Az összes modul elindítása eltarthat néhány percig, ezért ha nem látja mindet azonnal, futtassa újra a parancsot. 

Az egyes modulok által létrehozott üzenetek megtekintéséhez használja az `iotedge logs <module name>` parancsot. 

Az IoT Hubra beérkező üzenetek a Visual Studio Code segítségével tekinthetők meg. 

1. Az IoT Hub-központra érkező adatok monitorozásához válassza a három pont (**...**) elemet, majd a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
2. Egy adott eszköz D2C üzeneteinek monitorozásához kattintson a listában a jobb gombbal az eszközre, és válassza ki a **Start Monitoring D2C Messages** (D2C üzenetek monitorozásának megkezdése) lehetőséget.
3. Adatok monitorozásának leállításához futtassa a parancsot **Azure IoT hubbal: D2C üzenet monitorozásának leállítása** a parancskatalógus. 
4. A modul ikerdokumentumának megtekintéséhez vagy frissítéséhez kattintson a listában a jobb gombbal a modulra, és válassza az **Edit module twin** (Modul ikerdokumentumának szerkesztése) lehetőséget. A modul ikerdokumentumának frissítéséhez mentse az ikerdokumentum JSON-fájlját, kattintson a jobb gombbal a szerkesztési területre, majd válassza az **Update Module Twin** (Modul ikerdokumentumának frissítése) lehetőséget.
5. A Docker-naplók megtekintéséhez telepítse a [Docker](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) for VS Code (Docker a VS Code-hoz) eszközt. A helyi rendszerben a futó modulokat a Docker Explorerben találja meg. A helyi menüben válassza a **Show Logs** (Naplók megjelenítése) lehetőséget az integrált terminálban való megtekintéshez.
 
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 

Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a létrehozott erőforrásokat és konfigurációkat. Azt is megteheti, hogy ugyanezt az IoT Edge-eszközt használja teszteszközként. 

Ellenkező esetben a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott helyi konfigurációkat és Azure-erőforrásokat. 

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

[!INCLUDE [iot-edge-clean-up-local-resources](../../includes/iot-edge-clean-up-local-resources.md)]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban IoT Edge-modult hozott létre olyan kóddal, amely szűri az IoT Edge-eszköz által létrehozott nyers adatokat. Továbbléphet a következő oktatóanyagokra, és megtudhatja, milyen más módokon alakíthatja üzleti megállapításokká ezeket az adatokat a peremhálózaton az Azure IoT Edge segítségével.

> [!div class="nextstepaction"]
> [Adatok tárolása a peremhálózaton SQL Server-adatbázisokkal](tutorial-store-data-sql-server.md)


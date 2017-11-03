---
title: "Azure IoT Hub eszköz iker tulajdonságait (Java) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub eszköz twins eszközök konfigurálásához. Az Azure IoT-eszközök SDK Java segítségével valósítja meg a szimulált eszköz alkalmazások és az Azure IoT szolgáltatás Java SDK service-alkalmazást, amely módosítja a használatával egy eszközt a két eszköz konfigurációs végrehajtásához."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/12/2017
ms.author: dobett
ms.openlocfilehash: 3881e3791c97baf2922722f01f9e6e6ea55ed2e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Használni kívánt tulajdonságokat eszközök konfigurálása

[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Ez az oktatóanyag végén két Java konzol alkalmazások közül választhat:

* **Szimulált eszköz**, a szimulált eszköz alkalmazást, amely megvárja-e a szükséges konfiguráció-frissítés, és egy szimulált konfigurációs frissítési folyamat állapotának jelentéseket.
* **set konfiguráló**, egy háttér-alkalmazást, amely beállítja a kívánt konfiguráció egy eszközön, és lekérdezi a konfigurációs frissítési folyamat.

> [!NOTE]
> A cikk [Azure IoT SDK-k] [ lnk-hub-sdks] használható eszközt és a háttér-alkalmazások az Azure IoT SDK-k információt nyújt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A legújabb [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

Ha követte a [Ismerkedés az eszköz twins] [ lnk-twin-tutorial] oktatóanyagban már rendelkezik egy IoT-központot, és egy eszközidentitás nevű **myDeviceId**. Ebben az esetben ugorjon a [a szimulált eszköz-alkalmazás létrehozása] [ lnk-how-to-configure-createapp] szakasz.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>A szimulált eszköz-alkalmazás létrehozása
Ebben a szakaszban hoz létre egy Java-Konzolalkalmazás, amely kapcsolódik, a központ **myDeviceId**, és megvárja-e a szükséges konfiguráció frissítése a, majd jelentést készít a frissítések szimulált konfigurációs frissítési folyamat.

1. Hozzon létre egy üres nevű dt-get-started.

1. A dt-get-started mappában hozzon létre egy Maven project nevű **szimulált eszköz** parancsot a parancssorba az alábbi parancs segítségével. Látható, hogy ez egyetlen hosszú parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban lépjen a simulated-device mappára.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a szimulált eszköz mappában, és adja hozzá a következő függőség a **függőségek** csomópont. A függőség lehetővé teszi, hogy az iot-szolgáltatás-ügyfélcsomag az alkalmazás kommunikáljon az IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.30</version>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-eszközügyfél** [Maven-keresési] [lnk-maven-eszköz-keresési].

1. Adja hozzá a következő **build** csomópont után a **függőségek** csomópont. Ez a konfiguráció arra utasítja a Java 1.8 az lehetővé teszi az alkalmazás Maven:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Mentse és zárja be a pom.xml fájlt.

1. Egy szövegszerkesztőben nyissa meg a simulated-device\src\main\java\com\mycompany\app\App.java forrásfájl.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le **{yourdeviceconnectionstring}** feljegyzett eszköz kapcsolati karakterlánccal rendelkező a *hozzon létre egy eszközidentitás* szakasz:

    ```java
    private static String deviceId = "myDeviceId";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring}";
    private static DeviceClient client;
    private static TelemetryConfig telemetryConfig;
    ```

1. Egy visszahívás-kezelő eszköz iker állapoteseményeit (a hibakereséshez) a alkalmazásához adja hozzá a következő beágyazott osztályt a **App** osztály:

    ```java
    protected static class DeviceTwinStatusCallBack implements IotHubEventCallback 
    {
        public void execute(IotHubStatusCode status, Object context) 
        {
            //System.out.println("IoT Hub responded to device twin operation with status " + status.name());
        }
    }
    ```

1. Adja hozzá a következő beágyazott osztályt a **App** osztály:

    ```java
    private static class TelemetryConfig extends Device 
    {
        private String configId = "0000";
        private String sendFrequency = "45m";

        private Boolean initialRun = true;
        
        private Property telemetryConfig = new Property("telemetryConfig", "{configId=" + configId + ", sendFrequency=" + sendFrequency + "}");

        public void InitTelemetry() throws IOException 
        {
            System.out.println("Report initial telemetry config:");
            System.out.println(this.telemetryConfig);

            this.setReportedProp(this.telemetryConfig);

            client.sendReportedProperties(this.getReportedProp());
        }

        private void UpdateReportedConfiguration() 
        {
            try {
                System.out.println("Initiating config change");
                    
                this.setReportedProp(this.telemetryConfig);
                client.sendReportedProperties(this.getReportedProp());
                    
                System.out.println("Simulating device reset");
                
                Thread.sleep(10000);
                
                System.out.println("Completing config change");
                System.out.println("Config change complete");
            } catch (Exception e) {
                System.out.println("Exception \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
            }
        }

        @Override
        public void PropertyCall(String propertyKey, Object propertyValue, Object context) 
        {
            if (propertyKey.equals("telemetryConfig")) {
                if (!(initialRun)) {
                    System.out.println("Desired property change:");
                    System.out.println(propertyKey + ":" + propertyValue);

                    telemetryConfig.setValue(propertyValue);

                    UpdateReportedConfiguration();
                } else {
                    initialRun = false;
                }
            } 
        }
    }
    ```

    > [!NOTE]
    > A TelemetryConfig osztályt bővíti a [eszközosztályt] [ lnk-java-device-class] hozzáférhetnek a kívánt tulajdonságokkal visszahívások.

1. Aláírását, valamint módosíthatja a **fő** metódust kell küldeni a következő kivételekkel:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. Adja hozzá a következő kódot a **fő** metódus példányának létrehozása egy **DeviceClient** és **TelemetryConfig**:

    ```java
    client = new DeviceClient(connString, protocol);

    telemetryConfig = new TelemetryConfig();
    ```

1. Adja hozzá a következő kódot a **fő** történő eszköz iker szolgáltatások indítása:

    ```java
    try 
    {
        System.out.println("Connecting to hub");
        client.open();
        client.startDeviceTwin(new DeviceTwinStatusCallBack(), null, telemetryConfig, null);

        telemetryConfig.InitTelemetry();

        System.out.println("Wait for desired telemetry...");
        client.subscribeToDesiredProperties(telemetryConfig.getDesiredProp());
    }
    catch (Exception e) 
    {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" + e.getMessage());
        telemetryConfig.clean();
        client.close();
        System.out.println("Shutting down...");
    }
    ```

1. Adja hozzá a következő kódot a **fő** leállítása szükség esetén az eszköz szimulátor módszert:

    ```java
    System.out.println("Press enter to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();

    telemetryConfig.clean();
    client.close();
    ```

1. Mentse és zárja be a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

1. Build a **szimulált eszköz** háttér-alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban keresse meg a szimulált eszköz mappát, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

   > [!NOTE]
   > Ez az oktatóanyag nem szimulálása egyidejű keresni minden olyan esetben. Néhány konfigurációs frissítési folyamat közben fut-e a frissítés, néhány lehet a várólistába helyezni őket, és néhány sikerült hibát elutasítása a célként megadott konfigurációs módosítások befogadásához lehet. Ügyeljen arra, hogy fontolja meg a kívánt viselkedés, a konfigurációs folyamat, és adja hozzá a megfelelő logikai kezdeményezése a konfiguráció módosítása előtt.
   > 
   > 

## <a name="create-the-service-app"></a>A service-alkalmazás létrehozása
Ebben a szakaszban hoz létre egy Java-Konzolalkalmazás, amely frissíti a *szükséges tulajdonságok* meg az eszköz a két társított **myDeviceId** új telemetriai configuration objektummal. Ezután lekérdezi az eszköz twins az IoT hub tárolja, és a kívánt és jelentett konfigurációkat, az eszköz közötti különbséget szemlélteti.

1. A dt-get-started mappában hozzon létre egy Maven project nevű **set konfiguráló** parancsot a parancssorba az alábbi parancs segítségével. Látható, hogy ez egyetlen hosszú parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=set-configuration -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban navigáljon a set-konfigurációt tartalmazó mappa.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt az eseményindító-újraindítás mappában, és adja hozzá a következő függőség a **függőségek** csomópont. A függőség lehetővé teszi, hogy az iot-szolgáltatás-ügyfélcsomag az alkalmazás kommunikáljon az IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-szolgáltatásügyfél** [Maven-keresési] [lnk-maven-szolgáltatás-keresési].

1. Adja hozzá a következő **build** csomópont után a **függőségek** csomópont. Ez a konfiguráció arra utasítja a Java 1.8 az lehetővé teszi az alkalmazás Maven:

    ```xml
    <build>
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
          <artifactId>maven-compiler-plugin</artifactId>
          <version>3.3</version>
          <configuration>
            <source>1.8</source>
            <target>1.8</target>
          </configuration>
        </plugin>
      </plugins>
    </build>
    ```

1. Mentse és zárja be a pom.xml fájlt.

1. Egy szövegszerkesztőben nyissa meg a set-configuration\src\main\java\com\mycompany\app\App.java forrásfájl.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.*;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.HashSet;
    import java.util.Set;
    ```

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le **{youriothubconnectionstring}** az IoT hub kapcsolati karakterlánccal feljegyzett a *létrehoz egy IoT-központot* szakasz:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";
    ```

1. Lekérdezése, és frissítse a szimulált eszköz eszköz twins, adja hozzá az alábbi kódot a **fő** módszert:

    ```java
    DeviceTwin twinClient = DeviceTwin.createFromConnectionString(iotHubConnectionString);
    DeviceTwinDevice device = new DeviceTwinDevice(deviceId);

    String sendFrequency= "12h";

    try {
        twinClient.getTwin(device);

        // make sure a differing value exists before calling the updateTwin() method, else a null device exception will be thrown
        // this allows the program to be run multiple times for demonstration purposes
        String desiredProperties = device.desiredPropertiesToString();
        if (desiredProperties.contains("sendFrequency=" + sendFrequency))
        {
            sendFrequency = "8h";
        }
            
        Set<Pair> tags = new HashSet<Pair>();
        tags.add(new Pair("telemetryConfig", "{configId=0001, sendFrequency=" + sendFrequency + "}"));

        twinClient.getTwin(device);
        device.setDesiredProperties(tags);

        System.out.println("Config report for: " + deviceId);   
        System.out.println(device);

        twinClient.updateTwin(device);

        String reportedProperties = device.reportedPropertiesToString();
        Boolean waitFlag = true;

        while (waitFlag) {
            if (!reportedProperties.contains("sendFrequency=" + sendFrequency)) {
                Thread.sleep(10000);
            }
            else 
            {
                waitFlag = false;
            }

            twinClient.getTwin(device);
            reportedProperties = device.reportedPropertiesToString();
        }
            
        SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "properties.reported.telemetryConfig='{configId=0001, sendFrequency=" + sendFrequency + "}'", null);

        Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
        while (twinClient.hasNextDeviceTwin(twinQuery)) {
            DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
            System.out.println(d.getDeviceId() + " found with changed telemetryConfig");
        }

        System.out.println("Config report for: " + deviceId);
        twinClient.getTwin(device);
        System.out.println(device);

        } catch (IotHubException e) {
            System.out.println(e.getMessage());
        } catch (IOException e) {
            System.out.println(e.getMessage());
        } catch (InterruptedException e) {
            System.out.println(e.getMessage());
        }
    ```

1. Mentse és zárja be a set-desired-configuration\src\main\java\com\mycompany\app\App.java fájlt.

1. Build a **set konfiguráló** háttér-alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorba keresse meg a set-konfigurációs mappát, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`
   
    Ez a kód lekéri az eszköz iker a **myDeviceId**, majd frissíti a kívánt tulajdonságát egy új telemetriai configuration objektummal.
    Ezt követően azt lekérdezi az eszköz twins tárolja az IoT hub 10 másodpercenként, és kiírja a kívánt és jelentett telemetriai konfigurációkat. Tekintse meg a [IoT-központ lekérdezési nyelv] [ lnk-query] további információt az eszközök közötti hatékony jelentések létrehozásához.
   
   > [!IMPORTANT]
   > Ez az alkalmazás lekérdezi az IoT-központ szemléltető célokra 10 másodpercenként csak az eszköz frissítése után. Lekérdezésekkel számos eszközön keresztül a felhasználók számára is elérhető jelentések létrehozásához, és nem észleli a módosításokat. Ha a megoldás a valós idejű értesítések eszköz események van szüksége, [iker értesítések][lnk-twin-notifications].
   > 

   > [!IMPORTANT]
   > Nincs késleltetést legfeljebb egy percet, az eszköz jelentés művelet és a lekérdezési eredmények között. Ez a nagyon nagy méretekben működéséhez a lekérdezés infrastruktúra engedélyezéséhez.  Egy egyetlen eszközt iker használati konzisztens nézetek beolvasása a **getDeviceTwin** metódust a **DeviceTwin** osztály.
   > 
   > 

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. Parancsot egy parancssorba a szimulált eszköz mappában futtassa a következő parancsot az IoT hub eszköz iker hívásait figyelését:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. Parancsot egy parancssorba a set-konfigurációt tartalmazó mappa futtassa a következő parancsot lekérdezéséhez, és frissítse az eszköz twins az IoT hub a szimulált eszközén:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. A szimulált eszköz válaszol-e a rendszer újraindítása közvetlen metódus hívása:

    ![Java IoT Hub szimulált eszköz alkalmazásának válaszol-e az eszköz iker hívás][img-deviceconfigured]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy szabványoskonfiguráció mint beállítása *szükséges tulajdonságok* a megoldásban való háttér, és egy eszköz alkalmazás észleli a változást, és egy többlépéses frissítési folyamat állapotának jelentett tulajdonságai reporting szimulálása megírt.

A következő források segítségével megtudhatja, hogyan:

* telemetriai adatokat küldhet az eszközökről a [Ismerkedés az IoT-központ] [ lnk-iothub-getstarted] oktatóanyagban
* ütemezett vagy műveleteket hajtson végre a nagy mennyiségű eszközök lásd: a [ütemezés és a szórásos feladatok] [ lnk-schedule-jobs] oktatóanyag.
* az interaktív (például bekapcsolásával a felhasználó által felügyelt alkalmazásból ventilátor), eszközök szabályozásának a [közvetlen módszerekkel] [ lnk-methods-tutorial] oktatóanyag.

<!-- images -->
[img-deviceconfigured]: media/iot-hub-java-java-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-java-device-class]: https://docs.microsoft.com/en-us/java/api/com.microsoft.azure.sdk.iot.device._device_twin._device

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app

---
title: "Eszköz belső vezérlőprogram frissítése az Azure IoT Hub (Java/Java) |} Microsoft Docs"
description: "Hogyan használható az eszközkezelés Azure IoT hub eszköz vezérlőprogram-frissítés kezdeményezése. Az Azure IoT-eszközök SDK Java valósítja meg a szimulált eszköz alkalmazásának és a service-alkalmazást, amely elindítja a belső vezérlőprogram-frissítés végrehajtásához a."
services: iot-hub
documentationcenter: java
author: msebolt
manager: timlt
editor: 
ms.assetid: 70b84258-bc9f-43b1-b7cf-de1bb715f2cf
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: v-masebo
ms.openlocfilehash: 024c2e9bf580f97b412a85913ca29d757872556a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>Egy eszköz belső vezérlőprogram-frissítés (Java/Java) kezdeményezésére történő Eszközkezelés használata
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Az a [Ismerkedés az eszközkezelés] [ lnk-dm-getstarted] oktatóanyag, megtudhatta, hogyan használható a [eszköz iker] [ lnk-devtwin] és [közvetlen módszerek ] [ lnk-c2dmethod] primitívek távolról az eszköz újraindítását. Ez az oktatóanyag az ugyanazon az IoT-központ primitívek használ, és bemutatja, hogyan hajtsa végre egy végpontok közötti szimulált belső vezérlőprogram-frissítés.  A belső vezérlőprogram frissítési implementációja szerepel ebben a mintában a [málna Pi eszköz megvalósítási minta][lnk-rpi-implementation].

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy Java-konzolalkalmazást, amely behívja a **firmwareUpdate** közvetlen módszer a szimulált eszköz alkalmazásában az IoT hub keresztül.
* Hozzon létre egy Java-Konzolalkalmazás, amely az eszköz és megvalósítja az **firmwareUpdate** közvetlen módszer. Ez a módszer indít el egy többlépcsős folyamat, amely megvárja-e a belső vezérlőprogram lemezképet letölti, letölti a belső vezérlőprogram lemezképet és a belső vezérlőprogram kép végül vonatkozik. A frissítés egyes szakasza alatt az eszköz használatával a jelentésben szereplő tulajdonságok előrehaladásról.

Ez az oktatóanyag végén két Java konzol alkalmazások közül választhat:

**belső vezérlőprogram-frissítés**, meghívja a közvetlen módszer a szimulált eszköz, a válasz, valamint rendszeres időközönként jeleníti meg jelentett tulajdonság frissítései

**Szimulált eszköz**, csatlakozik az IoT hub a korábban létrehozott eszköz identitású, megkapja a firmwareUpdate közvetlen metódus hívása, és a belső vezérlőprogram frissítési szimuláció keresztül futtatja

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A legújabb [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Indítás, az eszközön, a közvetlen módszer használatával távoli vezérlőprogram-frissítés
Ebben a szakaszban hozzon létre egy Java-Konzolalkalmazás, amely indít el egy távoli belső vezérlőprogram frissítése egy eszközön. Az alkalmazás közvetlen módszer előtt használja a frissítés, és az eszköz iker lekérdezések rendszeres időközönként a aktív belső vezérlőprogram frissítése a állapot lekérdezése céljából.

1. Hozzon létre egy üres nevű fw-get-started.

1. A keretrendszer-get-started mappában hozzon létre egy Maven project nevű **belső vezérlőprogram-frissítés** parancsot a parancssorba az alábbi parancs segítségével. Látható, hogy ez egyetlen hosszú parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban keresse meg a belső vezérlőprogram-frissítés mappát.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a belső vezérlőprogram-frissítés mappában, és adja hozzá a következő függőség a **függőségek** csomópont. A függőség lehetővé teszi, hogy az iot-szolgáltatás-ügyfélcsomag az alkalmazás kommunikáljon az IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-szolgáltatásügyfél** használatával [Maven keresési](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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

1. Egy szövegszerkesztőben nyissa meg a firmware-update\src\main\java\com\mycompany\app\App.java forrásfájl.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwin;
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceTwinDevice;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le **{youriothubconnectionstring}** az IoT hub kapcsolati karakterlánccal feljegyzett a *létrehoz egy IoT-központot* szakasz:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Egy módszert, amelyet az eszköz két olvassa be a jelentésben szereplő tulajdonságok alkalmazásához adja hozzá a következő a **App** osztály:

    ```java
    public static void ShowReportedProperties() 
    {
        try 
        {
          DeviceTwin deviceTwins = DeviceTwin.createFromConnectionString(iotHubConnectionString);
          DeviceTwinDevice twinDevice = new DeviceTwinDevice(deviceId);
          
          Boolean firmwareUpdated = false;
          Integer timeoutCycle = 0;
    
          while (!firmwareUpdated)
          {
            if (timeoutCycle > 5)
            {
              System.out.println("Operation timed out");
              break;
            }
    
            Thread.sleep(1000);
              
            deviceTwins.getTwin(twinDevice);
    
            String reportedProperties = twinDevice.reportedPropertiesToString();
              
            if(reportedProperties.contains("status=waiting"))
            {
              System.out.println("Waiting on device...");
            }
            else if(reportedProperties.contains("status=downloadComplete"))
            {
              System.out.println("Download complete, applying firmware...");
            }
            else if (reportedProperties.contains("status=applyComplete"))
            {
              System.out.println("Firmware applied");
              System.out.println("Get reported properties from device twin");
              System.out.println(twinDevice.reportedPropertiesToString());
              firmwareUpdated = true;
            }
            else
            {
              timeoutCycle++;
            }
          }
        } catch (Exception ex) {
            System.out.println("Exception reading reported properties: " + ex.getMessage());
        }
    }
    ```

1. Aláírását, valamint módosíthatja a **fő** metódust kell küldeni a következő kivételekkel:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. Meghívni a firmwareUpdate közvetlen metódust a szimulált eszköz, adja hozzá az alábbi kódot a **fő** módszert:

    ```java
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
      String payload = "https://someurl";
      
      System.out.println("Invoked firmware update on device.");
      System.out.println("Sent URL: " + payload);
      
      MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

      if(result == null)
      {
        throw new IOException("Invoke direct method reboot returns null");
      }

      System.out.println("Status for device:   " + result.getStatus());
      System.out.println("Message from device: " + result.getPayload());
    }
    catch (IotHubException e)
    {
      System.out.println(e.getMessage());
    }
    ```

1. Kérdezze le a szimulált eszköz jelentett tulajdonságait, adja hozzá az alábbi kódot a **fő** módszert:

    ```java
    ShowReportedProperties();
    ```

1. Ahhoz, hogy állítsa le az alkalmazást, adja hozzá a következő kódot a **fő** módszert:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. Mentse és zárja be a firmware-update\src\main\java\com\mycompany\app\App.java fájlt.

1. Build a **belső vezérlőprogram-frissítés** háttér-alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban keresse meg a belső vezérlőprogram-frissítés mappát, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>Közvetlen metódushívások kezeli az eszköz szimulálása
Ebben a szakaszban hozzon létre egy Java szimulált eszköz-Konzolalkalmazás, amely képes fogadni a firmwareUpdate közvetlen módszer. Több államot folyamatot, a belső vezérlőprogram frissítési állapot kommunikáció reportedProperties segítségével szimulálhatja majd futtatja az alkalmazást.

1. A keretrendszer-get-started mappában hozzon létre egy Maven project nevű **szimulált eszköz** parancsot a parancssorba az alábbi parancs segítségével. Látható, hogy ez egyetlen hosszú parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban lépjen a simulated-device mappára.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a belső vezérlőprogram-frissítés mappában, és adja hozzá a következő függőség a **függőségek** csomópont. A függőség lehetővé teszi, hogy az iot-szolgáltatás-ügyfélcsomag az alkalmazás kommunikáljon az IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-eszközügyfél** használatával [Maven keresési](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

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
    import java.time.LocalDateTime;
    import java.util.Scanner;
    import java.util.Set;
    import java.util.HashSet;
    import java.util.HashMap;
    ```

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le **{yourdeviceconnectionstring}** az eszköz kapcsolati karakterlánccal feljegyzett a *hozzon létre egy Eszközidentitás* szakasz:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. Közvetlen módszer funkció végrehajtásához adja meg az itt a következő beágyazott osztályok hozzáadásával a **App** osztály:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "firmwareUpdate" :
          {
            System.out.println("Response to method '" + methodName + "' sent successfully");
    
            downloadURL = new String((byte[])methodData);
    
            int status = METHOD_SUCCESS;
            System.out.println("Starting firmware update");
            deviceMethodData = new DeviceMethodData(status, "Started firmware update");
            FirmwareUpdateThread firmwareUpdateThread = new FirmwareUpdateThread();
            Thread t = new Thread(firmwareUpdateThread);
            t.start();
            break;
          }
          default:
          {
            int status = METHOD_NOT_DEFINED;
            deviceMethodData = new DeviceMethodData(status, "Not defined direct method " + methodName);
          }
        }
        return deviceMethodData;
      }
    }
    ```

1. Valósítja meg az eszköz a két funkció, adja meg itt a következő beágyazott osztályok hozzáadásával a **App** osztály:

    ```java
    protected static class DeviceTwinStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device twin operation with status " + status.name());
      }
    }
    protected static class PropertyCallback implements PropertyCallBack<String, String>
    {
      public void PropertyCall(String propertyKey, String propertyValue, Object context)
      {
        System.out.println("PropertyKey:     " + propertyKey);
        System.out.println("PropertyKvalue:  " + propertyKey);
      }
    }
    ```

1. A belső vezérlőprogram-frissítés alkalmazásához adja hozzá a következő beágyazott osztályt a **App** osztály:

    ```java
    protected static class FirmwareUpdateThread implements Runnable {
      public void run() {
        try {      
          HashMap initialUpdate = new HashMap();
          Property sentProperty = new Property("firmwareUpdate", initialUpdate);
          Set<Property> sentPackage = new HashSet<Property>();
          
          System.out.println("Downloading from " + downloadURL);
    
          initialUpdate.put("status", "waiting");
          initialUpdate.put("fwPackageUri", downloadURL);
          initialUpdate.put("startedWaitingTime", LocalDateTime.now().toString());   
          sentPackage.add(sentProperty);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Download complete");
    
          HashMap downloadUpdate = new HashMap();
          downloadUpdate.put("status","downloadComplete");
          downloadUpdate.put("downloadCompleteTime", LocalDateTime.now().toString());
          downloadUpdate.put("startedApplyingImage", LocalDateTime.now().toString());
          sentProperty.setValue(downloadUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          System.out.println("Apply complete");
    
          HashMap applyUpdate = new HashMap();
          applyUpdate.put("status","applyComplete");
          applyUpdate.put("lastFirmwareUpdate", LocalDateTime.now().toString());
          sentProperty.setValue(applyUpdate);
    
          client.sendReportedProperties(sentPackage);
    
          Thread.sleep(5000);
    
          HashMap resetUpdate = new HashMap();
          applyUpdate.put("status","reset");
          sentProperty.setValue(resetUpdate);
    
          client.sendReportedProperties(sentPackage);
        }
        catch (Exception ex) {
          System.out.println("Exception in reboot thread: " + ex.getMessage());
        }
      }
    }
    ```

1. Aláírását, valamint módosíthatja a **fő** metódust kell küldeni a következő kivételekkel:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. A közvetlen módszerek és az eszköz twins rutin kezdeményez, adja hozzá az alábbi kódot a **fő** módszert:

    ```java
    client = new DeviceClient(connString, protocol);

    try
    {
      client.open();
      client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
      client.startDeviceTwin(new DeviceTwinStatusCallback(), null, new PropertyCallback(), null);
      System.out.println("Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.");
    }
    catch (Exception e)
    {
      System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Ahhoz, hogy állítsa le az alkalmazást, adja hozzá a következő kódot az végén a **fő** módszert:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Mentse és zárja be a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

1. Build a **szimulált eszköz** alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban keresse meg a szimulált eszköz mappát, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancsot a parancssorba a **szimulált eszköz** mappa, a következő parancsot a belső vezérlőprogram frissítési közvetlen módszer figyelését.
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. A parancsot a parancssorba a **belső vezérlőprogram-frissítés** mappa meghívása a frissítést, és az eszköz twins a szimulált eszköz az IoT hub a lekérdezés a következő parancsot:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. A szimulált eszköz ad választ az a közvetlen módszer a konzolon tekintheti meg.

    ![Belső vezérlőprogram frissítése sikeres volt][img-fwupdate]

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag elindítása egy távoli belső vezérlőprogram frissítése egy eszközön a közvetlen módszer használatával, és a jelentésben szereplő tulajdonságok segítségével nyomon követheti a belső vezérlőprogram frissítése.

Megtudhatja, hogyan terjeszthető ki az IoT-megoldás és az ütemezések metódushívások több eszközön, tekintse meg a [ütemezés és a szórásos feladatok] [ lnk-tutorial-jobs] oktatóanyag.

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
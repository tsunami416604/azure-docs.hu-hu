---
title: Eszköz belső vezérlőprogramjának frissítése az Azure IoT Hub (Java vagy Java) szolgáltatással |} A Microsoft Docs
description: Hogyan lehet Azure IoT Hub eszközfelügyeleti használatával kezdeményezheti az eszköz belső vezérlőprogram frissítése. Egy szimulált eszközalkalmazás megvalósításához és egy service-alkalmazás, amely elindítja a belső vezérlőprogram-frissítés megvalósítása az Azure IoT eszközoldali SDK a Javához készült használhatja.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: dobett
ms.openlocfilehash: 5991615bca26749e1f138b561260108f8bcf2646
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611327"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-javajava"></a>Eszközfelügyelet használatával kezdeményezheti az eszköz belső vezérlőprogram frissítése (Java vagy Java)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

Az a [Eszközfelügyelet – első lépések] [ lnk-dm-getstarted] oktatóanyag bemutatta, hogyan használható a [ikereszköz] [ lnk-devtwin] és [közvetlen metódusok ] [ lnk-c2dmethod] primitívek távolról újraindítja az eszközt. Ebben az oktatóanyagban használja az ugyanazon az IoT Hub primitívek, és bemutatja, hogyan teheti egy teljes körű szimulált belsővezérlőprogram-frissítést.  Ez a minta a belső vezérlőprogram frissítési megvalósítása szerepel a [Raspberry Pi eszköz megvalósítási minta][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy Java-konzolalkalmazást, amely meghívja a **firmwareUpdate** közvetlen metódust a szimulált eszközalkalmazásnak, az IoT hub segítségével.
* Hozzon létre egy Java-konzolalkalmazást, amely szimulálja az eszköz és valósítja meg a **firmwareUpdate** közvetlen metódust. Ez a módszer folyamatot kezdeményez, többlépcsős, a belső vezérlőprogram rendszerképének letöltésére vár, a belső vezérlőprogram rendszerképének letöltése és végül alkalmazza a belsővezérlőprogram-lemezképet. A frissítés minden egyes fázisa során az eszköz jelentett tulajdonságait felhasználva jelenti az állapotot.

Ez az oktatóanyag végén két Java-konzolalkalmazással fog rendelkezni:

**belső vezérlőprogram-frissítés**, közvetlen metódus meghívja a szimulált eszközön, a válasz, valamint rendszeres időközönként jeleníti meg a jelentett tulajdonságok frissítések

**a szimulált eszköz**, csatlakozik az IoT hubhoz a korábban létrehozott eszközidentitással, megkapja a firmwareUpdate közvetlen metódus meghívása és a belső vezérlőprogram frissítési szimuláció kipróbálása

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A legújabb [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html) 
* [Maven 3](https://maven.apache.org/install.html) 
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Aktiválja az eszközön, a közvetlen metódus használatával távoli belső vezérlőprogram frissítése
Ebben a szakaszban egy Java-konzolalkalmazást, amely kezdeményezi egy távoli belső vezérlőprogram frissítése egy eszközön hoz létre. Az alkalmazás közvetlen metódus kezdeményezni a frissítést, és használja az ikereszköz-lekérdezések rendszeres időközönként beolvasni az aktív belső vezérlőprogram frissítésének állapotát.

1. Hozzon létre egy keretrendszer-get-started nevű üres mappát.

1. A keretrendszer-get-started mappában hozzon létre egy nevű Maven-projektet **belső vezérlőprogram-frissítés** használja az alábbi parancsot a parancssorba. Látható, hogy ez egyetlen hosszú parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=firmware-update -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban keresse meg a belső vezérlőprogram-frissítés mappát.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a belső vezérlőprogram-frissítés mappában, és adja hozzá a következő függőséget a **függőségek** csomópont. Ezzel a függőséggel használhatja az iot-service-client csomagot az alkalmazásban, az IoT hubbal való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.5.22</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-service-client** használatával [Maven keresési](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Adja hozzá a következő **összeállítása** csomópont után a **függőségek** csomópont. Ez a konfiguráció arra utasítja a használható a Java 1.8-as hozhat létre az alkalmazást a maven használatával:

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

1. Egy szövegszerkesztővel nyissa meg a firmware-update\src\main\java\com\mycompany\app\App.java forrásfájl.

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

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le **{youriothubconnectionstring}** feljegyzett IoT hub kapcsolati karakterláncra az *hozzon létre egy IoT hubot* szakaszban:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    private static final String methodName = "firmwareUpdate";
    private static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    private static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    ```

1. Megvalósításának egyik módszere, amely beolvassa a jelentett tulajdonságok az ikereszközről, adja hozzá a következőt a **alkalmazás** osztály:

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

1. Módosítsa az aláírást, a **fő** metódus állíthatunk munkába a következő kivételek érvényesek:

    ```java
    public static void main( String[] args ) throws IOException
    ```

1. A szimulált eszközön firmwareUpdate közvetlen metódus meghívása, adja hozzá a következő kódot a **fő** módszer:

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

1. A szimulált eszköz a jelentett tulajdonságok lekérdezésére, adja hozzá a következő kódot a **fő** módszer:

    ```java
    ShowReportedProperties();
    ```

1. Ahhoz, hogy állítsa le az alkalmazást, adja hozzá a következő kódot a **fő** módszer:

    ```java
    System.out.println("Press ENTER to exit.");
    System.in.read();
    System.out.println("Shutting down sample...");
    ```

1. Mentse és zárja be a firmware-update\src\main\java\com\mycompany\app\App.java fájlt.

1. Hozhat létre a **belső vezérlőprogram-frissítés** háttéralkalmazáshoz, és kijavíthatja az esetleges hibákat. A parancssorban keresse meg a belső vezérlőprogram-frissítés mappát, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="simulate-a-device-to-handle-direct-method-calls"></a>Közvetlen metódust hívja kezelése eszköz szimulálása
Ebben a szakaszban egy Java szimulált eszköz-konzolalkalmazást, amely képes fogadni a firmwareUpdate közvetlen metódus hoz létre. Az alkalmazás ezután szimulálja a belső vezérlőprogram frissítését reportedProperties használatával való kommunikációhoz állapota több állapot folyamaton keresztül futtatja.

1. A keretrendszer-get-started mappában hozzon létre egy nevű Maven-projektet **simulated-device** használja az alábbi parancsot a parancssorba. Látható, hogy ez egyetlen hosszú parancs:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban lépjen a simulated-device mappára.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a belső vezérlőprogram-frissítés mappában, és adja hozzá a következő függőséget a **függőségek** csomópont. Ezzel a függőséggel használhatja az iot-service-client csomagot az alkalmazásban, az IoT hubbal való kommunikációhoz:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Ellenőrizze, hogy a legújabb **iot-device-client** használatával [Maven keresési](http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22).

1. Adja hozzá a következő **összeállítása** csomópont után a **függőségek** csomópont. Ez a konfiguráció arra utasítja a használható a Java 1.8-as hozhat létre az alkalmazást a maven használatával:

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

1. Egy szövegszerkesztővel nyissa meg a simulated-device\src\main\java\com\mycompany\app\App.java forrásfájl.

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

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le **{yourdeviceconnectionstring}** eszköz feljegyzett kapcsolati karakterláncra az *Eszközidentitás létrehozása* szakaszban:

    ```java
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;

    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String connString = "{yourdeviceconnectionstring";
    private static DeviceClient client;

    private static String downloadURL = "unknown";
    ```

1. Közvetlen metódus funkciók végrehajtásához adja meg visszahívások, a következő beágyazott osztályok hozzáadásával a **alkalmazás** osztály:

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

1. Device twin funkciók végrehajtásához adja meg visszahívások, a következő beágyazott osztályok hozzáadásával a **alkalmazás** osztály:

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

1. A belső vezérlőprogram-frissítés megvalósítása, adja hozzá a következő beágyazott osztály a **alkalmazás** osztály:

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

1. Módosítsa az aláírást, a **fő** metódus állíthatunk munkába a következő kivételek érvényesek:

    ```java
    public static void main(String[] args) throws IOException, URISyntaxException
    ```

1. A közvetlen metódusok és a device twins rutin kezdeményez, adja hozzá a következő kódot a **fő** módszer:

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

1. Ahhoz, hogy állítsa le az alkalmazást, adja hozzá a következő kódot a végéhez a **fő** módszer:

    ```java
    System.out.println("Press any key to exit...");
    Scanner scanner = new Scanner(System.in);
    scanner.nextLine();
    scanner.close();
    client.close();
    System.out.println("Shutting down...");
    ```

1. Mentse és zárja be a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

1. Hozhat létre a **simulated-device** alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban lépjen a simulated-device mappában, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.

1. A parancsot a parancssorba a **simulated-device** mappában futtassa a következő parancsot, amellyel megkezdheti a figyeli a belső vezérlőprogram frissítési közvetlen metódus.
   
    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

1. A parancsot a parancssorba a **belső vezérlőprogram-frissítés** mappában futtassa a következő parancsot indítja el a belső vezérlőprogram frissítését, és lekérdezheti az ikereszközöket az IoT hub a szimulált eszközön:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

3. A szimulált eszköz válaszol a közvetlen metódus a konzolon látható.

    ![Belső vezérlőprogram frissítése sikeresen megtörtént][img-fwupdate]

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban használt közvetlen metódus aktiválhat egy távoli belső vezérlőprogram frissítése egy eszközön, és hajtsa végre a belső vezérlőprogram frissítésének állapotát a jelentett tulajdonságok segítségével.

Ismerje meg, hogyan bővítheti az IoT-megoldás és az ütemezés metódus meghívja a több eszközre, tekintse meg a [feladatok ütemezése és szórása] [ lnk-tutorial-jobs] oktatóanyag.

<!-- images -->
[img-fwupdate]: media/iot-hub-java-java-firmware-update/firmwareUpdated.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-java-java-device-management-getstarted.md
[lnk-tutorial-jobs]: iot-hub-java-java-schedule-jobs.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
---
title: "Közvetlen Azure IoT Hub-módszerekkel (Java) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub közvetlen módszerek. Az Azure IoT-eszközök SDK Java segítségével valósítja meg a szimulált eszköz alkalmazást, amely közvetlen módszer és az Azure IoT szolgáltatást megvalósítása, amely a közvetlen módszer hívja service-alkalmazást Java SDK tartalmazza."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: ab035b8e-bff8-4e12-9536-f31d6b6fe425
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 4fb759ecd7767c126bc22165494652039ba1caa4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-direct-methods-java"></a>Közvetlen módszerekkel (Java)

[!INCLUDE [iot-hub-selector-c2d-methods](../../includes/iot-hub-selector-c2d-methods.md)]

Ebben az oktatóanyagban létrehoz két Java-konzol alkalmazásokhoz:

* **Invoke-közvetlen-method**, a Java háttér-alkalmazást, amely egy metódus meghívja a szimulált eszköz alkalmazás és a válasz megjeleníti.
* **Szimulált eszköz**, a Java-alkalmazást, amely egy eszköz csatlakoztatása az IoT hub létrehozása eszköz identitású szimulálja. Ez az alkalmazás a háttérből meghívott közvetlenül a válaszol.

> [!NOTE]
> Az SDK-k használó alkalmazások futtatásához eszközökön és a megoldás háttérrendszeréhez kapcsolatos információkért lásd: [Azure IoT SDK-k][lnk-hub-sdks].

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Java SE 8. <br/> [A fejlesztési környezet előkészítését][lnk-dev-setup] ismertető cikk leírja, hogyan telepítheti a Javát ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.
* Maven 3  <br/> [A fejlesztési környezet előkészítését][lnk-dev-setup] ismertető cikk leírja, hogyan telepítheti a [Mavent][lnk-maven] ehhez az oktatóanyaghoz Windows vagy Linux rendszeren.
* [NODE.js-verzió 0.10.0-s vagy újabb](http://nodejs.org).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy Java-Konzolalkalmazás, amely válaszol a végfelhasználók által a megoldás háttérrendszere nevű metódust.

1. Hozzon létre egy üres nevű iot-java-közvetlen-metódus.

1. Az iot-java-közvetlen-metódus mappában hozza létre a nevű Maven-projektté **szimulált eszköz** parancsot a parancssorba az alábbi parancs segítségével. A következő parancsot az egyetlen, hosszú parancsot:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban lépjen a simulated-device mappára.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt a simulated-device mappában, és adja hozzá a következő függőségeket a **függőségek** csomóponthoz. A függőség lehetővé teszi, hogy az iot-eszközök-ügyfélcsomag az alkalmazás kommunikáljon az IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.3.32</version>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-device-client** legújabb verzióját a [Maven keresési funkciójával][lnk-maven-device-search] tekintheti meg.

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

1. Egy szövegszerkesztővel nyissa meg a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.device.*;
    import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;

    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Scanner;
    ```

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. A mag cseréje `{youriothubname}` az IoT hub nevű és `{yourdevicekey}` a eszközkulcs értékre hozott létre a *hozzon létre egy eszközidentitás* szakasz:

    ```java
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myDeviceID;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
    private static String deviceId = "myDeviceId";
    private static final int METHOD_SUCCESS = 200;
    private static final int METHOD_NOT_DEFINED = 404;
    ```

    Ez a mintaalkalmazás a **protocol** változót használja egy **DeviceClient** objektum példányának létrehozásakor. 

1. Térjen vissza az IoT hub állapotkódot, vegye fel a következő beágyazott osztályt a **App** osztály:

    ```java
    protected static class DirectMethodStatusCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context)
      {
        System.out.println("IoT Hub responded to device method operation with status " + status.name());
      }
    }
    ```

1. A közvetlen módszer meghívásához a megoldás háttérből kezeli, vegye fel a következő beágyazott osztály a **App** osztály:

    ```java
    protected static class DirectMethodCallback implements com.microsoft.azure.sdk.iot.device.DeviceTwin.DeviceMethodCallback
    {
      @Override
      public DeviceMethodData call(String methodName, Object methodData, Object context)
      {
        DeviceMethodData deviceMethodData;
        switch (methodName)
        {
          case "writeLine" :
          {
            int status = METHOD_SUCCESS;
            System.out.println(new String((byte[])methodData));
            deviceMethodData = new DeviceMethodData(status, "Executed direct method " + methodName);
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

1. Létrehozásához egy **DeviceClient** és a közvetlen módszer meghívásához figyelésére, vegye fel a **fő** metódust a **App** osztály:

    ```java
    public static void main(String[] args)
      throws IOException, URISyntaxException
    {
      System.out.println("Starting device sample...");

      DeviceClient client = new DeviceClient(connString, protocol);

      try
      {
        client.open();
        client.subscribeToDeviceMethod(new DirectMethodCallback(), null, new DirectMethodStatusCallback(), null);
        System.out.println("Subscribed to direct methods. Waiting...");
      }
      catch (Exception e)
      {
        System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \n" +  e.getMessage());
        client.close();
        System.out.println("Shutting down...");
      }

      System.out.println("Press any key to exit...");
      Scanner scanner = new Scanner(System.in);
      scanner.nextLine();
      scanner.close();
      client.close();
      System.out.println("Shutting down...");
    }
    ```

1. Mentse és zárja be a simulated-device\src\main\java\com\mycompany\app\App.java fájl

1. Build a **szimulált eszköz** alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban keresse meg a szimulált eszköz mappát, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="call-a-direct-method-on-a-device"></a>A közvetlen metódus hívása az eszközön

Ebben a szakaszban hozzon létre egy Java-Konzolalkalmazás, amely meghívja a közvetlen módszer és a válasz megjeleníti. A Konzolalkalmazás csatlakozik az IoT Hub közvetlen metódus meghívására.

1. Az iot-java-közvetlen-metódus mappában hozza létre a nevű Maven-projektté **meghívása közvetlen-metódus** parancsot a parancssorba az alábbi parancs segítségével. A következő parancsot az egyetlen, hosszú parancsot:

    `mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=invoke-direct-method -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

1. A parancssorban keresse meg az invoke-közvetlen-metódus mappát.

1. Egy szövegszerkesztővel nyissa meg a pom.xml fájlt az invoke-közvetlen-metódus mappában, és adja hozzá a következő függőség a **függőségek** csomópont. A függőség lehetővé teszi, hogy az iot-szolgáltatás-ügyfélcsomag az alkalmazás kommunikáljon az IoT hub:

    ```xml
    <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-service-client</artifactId>
      <version>1.7.23</version>
      <type>jar</type>
    </dependency>
    ```

    > [!NOTE]
    > Az **iot-service-client** legújabb verzióját a [Maven keresési funkciójával][lnk-maven-service-search] tekintheti meg.

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

1. Egy szövegszerkesztőben nyissa meg a invoke-direct-method\src\main\java\com\mycompany\app\App.java fájlt.

1. Adja hozzá a következő **importálási** utasításokat a fájlhoz:

    ```java
    import com.microsoft.azure.sdk.iot.service.devicetwin.DeviceMethod;
    import com.microsoft.azure.sdk.iot.service.devicetwin.MethodResult;
    import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

    import java.io.IOException;
    import java.util.concurrent.TimeUnit;
    ```

1. Adja hozzá a következő osztályszintű változókat az **App** osztályhoz. Cserélje le `{youriothubconnectionstring}` az IoT hub kapcsolati karakterlánccal feljegyzett a *létrehoz egy IoT-központot* szakasz:

    ```java
    public static final String iotHubConnectionString = "{youriothubconnectionstring}";
    public static final String deviceId = "myDeviceId";

    public static final String methodName = "writeLine";
    public static final Long responseTimeout = TimeUnit.SECONDS.toSeconds(30);
    public static final Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);
    public static final String payload = "a line to be written";
    ```

1. A szimulált eszköz metódus meghívása, adja hozzá az alábbi kódot a **fő** módszert:

    ```java
    System.out.println("Starting sample...");
    DeviceMethod methodClient = DeviceMethod.createFromConnectionString(iotHubConnectionString);

    try
    {
        System.out.println("Invoke direct method");
        MethodResult result = methodClient.invoke(deviceId, methodName, responseTimeout, connectTimeout, payload);

        if(result == null)
        {
            throw new IOException("Direct method invoke returns null");
        }
        System.out.println("Status=" + result.getStatus());
        System.out.println("Payload=" + result.getPayload());
    }
    catch (IotHubException e)
    {
        System.out.println(e.getMessage());
    }

    System.out.println("Shutting down sample...");
    ```

1. Mentse és zárja be a invoke-direct-method\src\main\java\com\mycompany\app\App.java fájl

1. Build a **meghívása közvetlen-metódus** alkalmazást, és kijavíthatja az esetleges hibákat. A parancssorban keresse meg az invoke-közvetlen-metódus mappát, és futtassa a következő parancsot:

    `mvn clean package -DskipTests`

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll a konzol alkalmazások futtatásához.

1. Parancsot egy parancssorba a szimulált eszköz mappában futtassa a következő parancsot az IoT hub a metódushívások figyelését:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT Hub szimulált eszköz alkalmazást a közvetlen metódushívások figyelésére][8]

1. A parancssorba az invoke-közvetlen-metódus mappában futtassa a következő parancsot a szimulált eszköz egyik metódusát hívja az IoT hub:

    `mvn exec:java -Dexec.mainClass="com.mycompany.app.App"`

    ![Java IoT-központ szolgáltatás alkalmazás közvetlen metódusának hívása][7]

1. A szimulált eszköz válaszol-e a közvetlen metódus hívása:

    ![Java IoT Hub szimulált eszköz alkalmazásának válaszol-e a közvetlen metódus hívása][9]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Ez az eszközazonosító segítségével engedélyezheti a felhő által meghívott módszerek reagálni a szimulált eszköz alkalmazás. Létrehozott egy alkalmazást, amely meghívja a módszerek az eszközön, és megjeleníti az eszköz válaszára is.

Más IoT-forgatókönyvek megismeréséhez tekintse meg a [több eszközön feladatok ütemezése][lnk-devguide-jobs].

Megtudhatja, hogyan terjeszthető ki az IoT-megoldás és az ütemezések metódushívások több eszközön, tekintse meg a [ütemezés és a szórásos feladatok] [ lnk-tutorial-jobs] oktatóanyag.

<!-- Images. -->
[7]: ./media/iot-hub-java-java-direct-methods/invoke-method.png
[8]: ./media/iot-hub-java-java-direct-methods/device-listen.png
[9]: ./media/iot-hub-java-java-direct-methods/device-respond.png

<!-- Links -->
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md
[lnk-maven]: https://maven.apache.org/what-is-maven.html
[lnk-hub-sdks]: iot-hub-devguide-sdks.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-maven-service-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22
[lnk-maven-device-search]: http://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-device-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22

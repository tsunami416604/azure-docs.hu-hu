---
title: "Az Azure IoT Hub (Java) útválasztási üzenetek |} Microsoft Docs"
description: "How Azure IoT Hub eszközről a felhőbe üzenetek feldolgozásához átirányítani más háttérszolgáltatások üzenetek útválasztási szabályokat és az egyéni végpontokat használatával."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: dobett
ms.openlocfilehash: c75624d8551adabbd993d22ac4901ca26ef40d51
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="routing-messages-with-iot-hub-java"></a>Üzenetek (Java) az IoT hubbal

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

Az Azure IoT Hub egy teljes körűen felügyelt szolgáltatás, amely lehetővé teszi a megbízható és biztonságos kétirányú kommunikációs eszközök millióira és a megoldás közötti háttér. Egyéb oktatóanyagok ([Ismerkedés az IoT-központ] és [IoT Hub-felhő eszközre üzenetek][lnk-c2d]) bemutatják a basic, eszköz-felhő és a felhő-eszköz használata az IoT-központ üzenetküldési működését.

Ez az oktatóanyag épít, a kód a [Ismerkedés az IoT-központ] oktatóanyag, és bemutatja, hogyan használható az üzenet-útválasztás eszközről a felhőbe üzenetek feldolgozásához skálázható módon. Az oktatóanyag bemutatja, hogyan a megoldás háttérből azonnali beavatkozást igénylő üzenetek feldolgozásához. Például egy eszköz előfordulhat, hogy küldése riasztás, amely elindítja a jegy beszúrása egy CRM-rendszerbe. Adatpont üzenetek ezzel szemben, egyszerűen hírcsatorna be egy elemzés. Például hőmérséklet telemetriai adatokat egy eszközről, amely későbbi elemzési kell tárolni az egy adatpont üzenet.

Ez az oktatóanyag végén három Java konzol alkalmazások futtatása:

* **Szimulált eszköz**, az alkalmazás létrehozása a módosított változatát a [Ismerkedés az IoT-központ] oktatóanyagban adatpont eszközről a felhőbe üzeneteket küld másodpercenként, és az interaktív eszközre felhő üzenetek 10 másodpercenként . Ezt az alkalmazást az AMQP protokoll használatával kommunikáljon az IoT hubbal.
* **olvasási-d2c-üzenetek** jeleníti meg az eszköz alkalmazás által küldött telemetriai adatok.
* **olvasási kritikus várólista-** távolít el a Service Bus-üzenetsorba, az IoT hub csatolva a kritikus üzeneteihez.

> [!NOTE]
> Az IoT-központ rendelkezik sok eszköz platformok és nyelvek, beleértve a C, Java és JavaScript SDK támogatása. Az eszköz a fizikai eszköz ebben az oktatóanyagban helyettesítése és eszközök csatlakoztatása az IoT-központ, lásd: a [Azure IoT fejlesztői központ].

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* A teljes működő verziójához a [Ismerkedés az IoT-központ] oktatóanyag.
* A legújabb [Java SE Development Kit 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
* [Maven 3](https://maven.apache.org/install.html)
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

Javasoljuk továbbá kapcsolatos tájékoztatás [Azure Storage] és [Azure Service Bus].

## <a name="send-interactive-messages-from-a-device-app"></a>Interaktív üzenetek küldése egy eszköz alkalmazásból
Ebben a szakaszban létrehozott eszközalkalmazás módosítja a [Ismerkedés az IoT-központ] alkalmanként küldéséhez azonnali feldolgozást igénylő oktatóanyag.

1. Egy szövegszerkesztővel nyissa meg a simulated-device\src\main\java\com\mycompany\app\App.java fájlt. Ez a fájl tartalmazza a kódját a **szimulált eszköz** létrehozta az alkalmazást a [Ismerkedés az IoT-központ] oktatóanyag.

2. Cserélje le a **MessageSender** osztály az alábbi kódra:

    ```java
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        if (new Random().nextDouble() > 0.5) {
                            msgStr = "This is a critical message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "critical");
                        } else {
                            msgStr = "This is a storage message.";
                            msg = new Message(msgStr);
                            msg.setProperty("level", "storage");
                        }
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    Ez a módszer véletlenszerűen hozzáadja a tulajdonság `"level": "critical"` és `"level": "storage"` az eszköz által küldött üzenetek, amely szimulálja egy üzenetet, amely az alkalmazás háttér-vagy, hogy véglegesen kell tárolni, hogy azonnali beavatkozást igényel. Az alkalmazás az üzenet törzse alapuló útválasztási üzeneteket is támogatja.
   
   > [!NOTE]
   > Több, különböző esetekre, beleértve a cold-path feldolgozási mellett a gyakran használt adatok elérési útja az itt bemutatott példában üzenettulajdonságok üzenetek is használhatja.

2. Mentse és zárja be a simulated-device\src\main\java\com\mycompany\app\App.java fájlt.

    > [!NOTE]
    > Határozottan javasoljuk, hogy például az exponenciális leállítási, az MSDN-cikkben leírtak újrapróbálkozási házirendje megvalósítása [átmeneti hiba kezelése].

3. Ha a **simulated-device** alkalmazást a Maven használatával szeretné felépíteni, futtassa a következő parancsot a parancssorban a simulated-device mappában:

    ```cmd/sh
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>Az IoT hub és útvonal üzenetek rá egy várólista hozzáadása

Ebben a szakaszban hozzon létre egy Service Bus-üzenetsorba, csatlakoztassa az IoT hub, és konfigurálja az IoT hub, az üzenetek küldése az üzenetsorba, annak alapján, hogy az üzenet-tulajdonság. A Service Bus-üzenetsorok folyamat üzenetek módjáról további információkért lásd: [Ismerkedés a várólisták][lnk-sb-queues-java].

1. Hozzon létre egy Service Bus-üzenetsorba, a [Ismerkedés a várólisták][lnk-sb-queues-java]. Jegyezze fel az a névtér és a várólista nevét.

2. Az Azure portálon, nyissa meg az IoT hub, és kattintson **végpontok**.

    ![Az IoT-központ végpontok][30]

3. Az a **végpontok** panelen kattintson **Hozzáadás** a sor hozzáadása az IoT hub tetején. A végpont neve **CriticalQueue** majd válassza ki a legördülő listákat **Service Bus-üzenetsorba**, ahol a várólista van a Service Bus-névtér és a várólista nevét. Amikor elkészült, kattintson a **mentése** alján.

    ![A végpont hozzáadása][31]

4. Most kattintson **útvonalak** az IoT Hub a. Kattintson a **Hozzáadás** útválasztási szabály, amely továbbítja az üzeneteket a várólista létrehozása a panel tetején az előzőekben adott hozzá. Válassza ki **DeviceTelemetry** a adatforrásként. Adja meg `level="critical"` feltételt, és válassza ki a várólista-útválasztási szabály végpontjának egyéni végpontként hozzáadott. Amikor elkészült, kattintson a **mentése** alján.

    ![Egy útvonal hozzáadása][32]

    Győződjön meg arról, hogy a tartalék útvonal értéke **ON**. Ez a beállítás az alapértelmezett beállítása az IoT-központ.

    ![Tartalék útvonal][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(Választható) A várólista végpontról olvasása

Opcionálisan elolvashatja az üzenetek a várólista végpontról utasításait követve [Ismerkedés a várólisták][lnk-sb-queues-java]. Az alkalmazás neve **olvasási kritikus várólista-**.

## <a name="run-the-applications"></a>Az alkalmazások futtatása

Most már készen áll a három alkalmazások futtatásához.

1. Futtatásához a **d2c-üzenetek olvasása** alkalmazás, a parancssor vagy a rendszerhéj keresse meg azt az olvasási-d2c mappát, és hajtsa végre a következő parancsot:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```

   ![Futtassa a üzenetek d2c olvasása][readd2c]

2. Futtatásához a **olvasási kritikus várólista-** alkalmazás, a parancssor vagy a rendszerhéj keresse meg azt az olvasási kritikus várólista-mappát, és hajtsa végre a következő parancsot:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Olvasási kritikus üzeneteihez futtatása][readqueue]

3. Futtatásához a **szimulált eszköz** alkalmazást, a parancssor vagy a rendszerhéj keresse meg a szimulált eszköz mappát, és hajtsa végre a következő parancsot:

   ```cmd/sh
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![Szimulált eszköz futtatása][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(Választható) Az IoT hub és útvonal üzenetek hozzá a tároló hozzáadása

Ebben a szakaszban hozzon létre egy tárfiókot, csatlakoztassa az IoT hub, és konfigurálja az IoT hub üzeneteket küldhet a fiók, az üzenet tulajdonság alapján. Felügyelni a tárolást kapcsolatos további információkért lásd: [Ismerkedés az Azure Storage szolgáltatással][Azure Storage].

 > [!NOTE]
   > Ha nem csak egy **végpont**, előfordulhat, hogy a telepítő a **StorageContainer** kívül a **CriticalQueue** , és mindkét simulatneously futtassa.

1. Hozzon létre egy tárfiókot, [Azure Storage-dokumentációt] [lnk-tároló] leírtak szerint. Jegyezze fel a fiók nevét.

2. Az Azure portálon, nyissa meg az IoT hub, és kattintson **végpontok**.

3. Az a **végpontok** panelen válassza a **CriticalQueue** végpontot, és kattintson **törlése**. Kattintson a **Igen**, és kattintson a **Hozzáadás**. A végpont neve **StorageContainer** majd válassza ki a legördülő listákat **Azure-tárolót**, és hozzon létre egy **tárfiók** és egy **tárolási tároló**.  Jegyezze fel a neveket.  Amikor elkészült, kattintson a **OK** alján. 

 > [!NOTE]
   > Ha nem csak egy **végpont**, nem kell törölnie a **CriticalQueue**.

4. Kattintson a **útvonalak** az IoT Hub a. Kattintson a **Hozzáadás** útválasztási szabály, amely továbbítja az üzeneteket a várólista létrehozása a panel tetején az előzőekben adott hozzá. Válassza ki **eszközre küldött üzenetek** a adatforrásként. Adja meg `level="storage"` feltételt, és válassza a **StorageContainer** útválasztási szabály végpontjának egyéni végpontként. Kattintson a **mentése** alján.  

    Győződjön meg arról, hogy a tartalék útvonal értéke **ON**. Ez a beállítás az alapértelmezett beállítása az IoT-központ.

1. Győződjön meg arról, hogy az előző alkalmazások továbbra is futnak. 

1. Az Azure portálon lépjen a tárfiókhoz a **Blob szolgáltatás**, kattintson a **blobok Tallózás...** .  Jelölje ki a tárolót, keresse meg és kattintson a JSON-fájlt, és kattintson **letöltése** adatok megtekintéséhez.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudta, hogyan megbízhatóan átirányítani az eszköz a felhőbe küldött üzeneteket az üzenetet az IoT-központ útválasztási funkcióra használatával.

A [IoT hubbal felhő eszközre üzenetek küldése] [ lnk-c2d] bemutatja, hogyan üzenetek küldése az eszközöket a megoldás háttérből.

Példák teljes végpontok közötti megoldások, amelyek használják az IoT-központot, lásd: [Azure IoT Suite][lnk-suite].

Az IoT hubbal megoldások fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [IoT Hub fejlesztői útmutató].

Az üzenetet az IoT hubon útválasztási kapcsolatos további információkért lásd: [üzeneteket küldjön és fogadjon IoT hubbal][lnk-devguide-messaging].

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub fejlesztői útmutató]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Ismerkedés az IoT-központ]: iot-hub-java-java-getstarted.md
[Azure IoT fejlesztői központ]: https://azure.microsoft.com/develop/iot
[átmeneti hiba kezelése]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Átmeneti hiba kezelése]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-java-java-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/

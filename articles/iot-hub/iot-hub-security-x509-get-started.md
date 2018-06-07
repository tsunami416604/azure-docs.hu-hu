---
title: Útmutató az Azure IoT Hub X.509 biztonsági |} Microsoft Docs
description: Ismerkedés az Azure IoT hub szimulált környezetben X.509-alapú biztonsági.
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: dd5b9f196f911011d9dd606d46f515b88d338531
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635576"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Az Azure IoT hub X.509 biztonsági beállítása

Ez az oktatóanyag az Azure IoT hub használatával biztonságos lépéseket szimulálja a *X.509-tanúsítvány hitelesítése*. Szemlélteti bemutatjuk a nyílt forráskódú eszköz OpenSSL segítségével olyan tanúsítványokat hoznak létre helyileg a Windows-számítógépre. Azt javasoljuk, hogy használja-e ez az oktatóanyag csak tesztelési célokra. Éles környezet érdekében vásároljon kell a tanúsítványokat egy *legfelső szintű hitelesítésszolgáltatót (CA)*. 

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag megköveteli, hogy Ön készen áll a következőket:

- Az IoT-központ létrehozott Azure-előfizetéséhez. Lásd: [létrehoz egy IoT-központot portálon keresztül](iot-hub-create-through-portal.md) a részletes lépéseket. 
- Rendelkezik [Visual Studio 2015-öt vagy a Visual Studio 2017](https://www.visualstudio.com/vs/) van telepítve a számítógépre. 

<a id="getcerts"></a>

## <a name="get-x509-ca-certificates"></a>X.509 CA-tanúsítványok beszerzése
Az IoT Hub X.509-alapú biztonsági ahhoz szükséges egy [X.509-tanúsítvány láncában](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), mely tartalmazza a legfelső szintű tanúsítvány, valamint a bármely beállítást a levéltanúsítvány köztes tanúsítványok. 

A következő módszereket a tanúsítványok beolvasása választhatja:
- Az X.509 tanúsítvány vásárlása egy *legfelső szintű hitelesítésszolgáltatót (CA)*. Ez az üzemi környezetben javasolt.
VAGY
- Hozzon létre egy saját X.509-tanúsítványokat, mint például egy harmadik féltől származó eszközzel [OpenSSL](https://www.openssl.org/). Ez lesz finom teszt és fejlesztési célokra. Az elvégzendő *létrehozása az x.509 szabványú tanúsítványokban* és *létrehozása X.509 tanúsítványlánc* a cikkben [X.509 tanúsítvány létrehozása a PowerShell segítségével](iot-hub-security-x509-create-certificates.md) ismerteti a PowerShell-parancsfájlpélda a tanúsítványok létrehozásához keresztül OpenSSL és a PowerShell használatával. Ha használni szeretné **Bash** helyett PowerShell rendszerhéj, olvassa el a kapcsolódó szakaszait [CA-tanúsítványok minta kezelése](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md). Ez az oktatóanyag a többi fogja használni a OpenSSL-környezet, állítsa be ezt a *hogyan* lépésre Azure IoT Hub-végpont X.509 biztonsági útmutatója.


<a id="registercerts"></a>

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Az IoT hub X.509 Hitelesítésszolgáltatói tanúsítványok regisztrálása

Ezeket a lépéseket mutatja be egy új hitelesítésszolgáltató hozzáadása az IoT hub a portálon keresztül.

1. Az IoT hub navigáljon az Azure-portálon, és nyissa meg a **beállítások** > **tanúsítványok** menü. 
2. Kattintson a **Hozzáadás** hozzáadása egy új tanúsítványt.
3. Adjon meg egy jól hangzó megjelenítési nevet a tanúsítványhoz. Válassza ki a legfelső szintű tanúsítvány fájlt nevű *RootCA.cer* a számítógépről az előző szakaszban létrehozott. Kattintson a **Feltöltés** gombra.
4. Ha a tanúsítvány feltöltése sikerült értesítést kap, kattintson **mentése**.

    ![Tanúsítvány feltöltése](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Ez megjeleníti a tanúsítványt a **tanúsítvány Explorer** listája. Megjegyzés: a **állapot** ezt a tanúsítványt az *tartalomszűrő*.

5. Kattintson az előző lépésben felvett tanúsítványban.

6. Az a **Tanúsítványadatok** panelen kattintson a **ellenőrző kód generálása**.

7. Létrehoz egy **ellenőrzőkódot** ellenőrzése a tanúsítvány tulajdonosa. A kódot másolja a vágólapra. 

   ![Tanúsítvány ellenőrzése](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Most, be kell jelentkeznie a *ellenőrzőkódot* és a titkos kulcs társult a hitelesítésszolgáltató X.509 tanúsítvánnyal, amely állít elő, aláírás. Nincsenek elérhető hajtsa végre az aláírási folyamat, például OpenSSL eszközöket. Ez más néven a [adatok](https://tools.ietf.org/html/rfc5280#section-3.1). Ha a PowerShell-mintaparancsfájlok használta az előző szakaszban, majd futtassa a parancsfájlt, című részében [az X.509 Hitelesítésszolgáltatói tanúsítvány adatok](iot-hub-security-x509-create-certificates.md#signverificationcode).
 
9. Töltse fel az eredményül kapott aláírás a portálon az IoT hub 8. lépésben fent. Az a **Tanúsítványadatok** panel az Azure portálon lépjen a **ellenőrző tanúsítvány .pem vagy .cer fájl**, és válassza ki például az aláírás *VerifyCert4.cer*hozta létre a minta PowerShell parancs segítségével a _Fájlkezelőben_ mellett az ikonra.

10. Ha a tanúsítvány sikeresen feltöltődött, kattintson **ellenőrizze**. A **állapot** a tanúsítvány módosításait **_ellenőrizve_** a a **tanúsítványok** panelen. Kattintson a **frissítése** Ha nem frissíti automatikusan.

   ![Ellenőrző tanúsítvány feltöltése](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  


<a id="createdevice"></a>

## <a name="create-an-x509-device-for-your-iot-hub"></a>Egy X.509 eszközt az IoT hub létrehozása

1. Az Azure-portálon lépjen az IoT hub **eszköz Explorer**.

2. Kattintson a **Hozzáadás** új eszköz hozzáadásához. 

3. Egy jól hangzó megjelenítési nevet adjon a **Eszközazonosító**, és válassza ki **_X.509 hitelesítésszolgáltató aláírt_** , a **hitelesítési típus**. Kattintson a **Save** (Mentés) gombra.

   ![X.509-eszköz létrehozása a portálon](./media/iot-hub-security-x509-get-started/create-x509-device.png)



<a id="authenticatedevice"></a>

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>X.509-tanúsítványokat az X.509 eszköz hitelesítéséhez

Az X.509 eszköz hitelesítéséhez, kell az eszközt a hitelesítésszolgáltató tanúsítványát az szolgáltatásba. A levél eszközök aláírása általában végezhető el a gyártási üzemben, ahol gyártási eszközök ennek megfelelően engedélyezték. Az eszközt másik kell vinnie egy gyártójától, mindegyik gyártó aláírási művelet belül a tanúsítványlánc köztes tanúsítvány lesz rögzítve. A végeredménynek az eszköz levéltanúsítvány a hitelesítésszolgáltató tanúsítványa egy tanúsítványlánc. Ha a korábbi szakaszokban a PowerShell-parancsfájlokat használja, akkor a parancsfájl című részében is futtathatja *létrehozás levél X.509-tanúsítvány az eszköz* a cikkben [PowerShell parancsfájlok kezelheti a hitelesítésszolgáltató által aláírt X.509-tanúsítványokat](iot-hub-security-x509-create-certificates.md) szimulálása ezt a folyamatot.

Ezután láthatja, hogyan hozhat létre egy C# alkalmazást, az X.509 eszköz regisztrálva az IoT hub szimulálásához. Kapni fog hőmérséklet és a páratartalom értékek a szimulált eszköz a központnak. Vegye figyelembe, hogy ebben az oktatóanyagban létre fogunk hozni csak az alkalmazást. Az olvasók az IoT-központ szolgáltatás alkalmazás, amely az eseményeket, a szimulált eszköz által küldött választ küld létrehozására való nélkül van, egy gyakorlatot. A C#-alkalmazás feltételezi, hogy elvégezte-e a PowerShell-parancsfájlok, a cikkben említett [PowerShell-parancsfájlokkal kezelheti a hitelesítésszolgáltató által aláírt X.509-tanúsítványokat](iot-hub-security-x509-create-certificates.md)

1. A Visual Studio új Visual C# klasszikus Windows asztal-projekt létrehozása a konzol alkalmazás projektsablon használatával. Nevet a projektnek **SimulateX509Device**.
   ![A Visual Studio X.509 eszköz projekt létrehozása](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateX509Device** projektre, és kattintson a **NuGet-csomagok kezelése...** . Válassza a NuGet-Csomagkezelő ablak **Tallózás** keresse meg a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, majd fogadja el a használati feltételeket. Ez az eljárás tölti le, telepíti, és hozzáad egy hivatkozást az Azure IoT eszköz SDK NuGet-csomagot és annak függőségeit.
   ![A Visual Studio eszközök SDK NuGet-csomag hozzáadása](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Adja hozzá az alábbi kódsorokat tetején a *Program.cs* fájlt:
    
    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. Adja hozzá az alábbi kódsorokat belül a **Program** osztály:
    
    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```
   Használt eszközök rövid név használata helyett az előző szakaszban leírt _< your_device_id >_ helyőrző.

5. A következő függvény a hőmérséklet és a páratartalom véletlenszerű számok létrehozásához, és ezeket az értékeket küldeni a hub hozzáadása:
    ```CSharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

6. Végül adja hozzá a következő sorokat a kód a **fő** funkció, a helyőrzők helyett _-eszközazonosító_, _-iot-központ-név_ és  _Absolute-path-to-Your-Device-PFX-File_ kérelmének a telepítőt.
    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "123");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

        if (deviceClient == null)
        {
            Console.WriteLine("Failed to create DeviceClient!");
        }
        else
        {
            Console.WriteLine("Successfully created DeviceClient!");
            SendEvent(deviceClient).Wait();
        }

        Console.WriteLine("Exiting...\n");
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
   Ez a kód csatlakozik az IoT hub a kapcsolati karakterlánc az X.509 eszközhöz létrehozásával. Sikeres csatlakozás után, majd elküldi a hőmérséklet és a páratartalom eseményeket szeretne az elosztóhoz, és megvárja-e a válaszában. 
7. Mivel ez az alkalmazás fér hozzá egy *.pfx* fájlt kell végrehajtani ezt a *Admin* mód. A Visual Studio megoldás felépítéséhez. Nyisson meg egy új parancsablakot, mint egy **rendszergazda**, és lépjen abba a mappába, amely tartalmazza az ebben a megoldásban. Keresse meg a *bin/Debug* belül a megoldás mappa elérési útja. Az alkalmazás futtatásához **SimulateX509Device.exe** a a _Admin_ parancsablakot. Meg kell jelennie az eszközt sikeresen csatlakozik a központ és az események küldése. 
   ![Eszköz-alkalmazás futtatása](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="see-also"></a>Lásd még
Az IoT-megoldásból biztosításával kapcsolatos további tudnivalókért lásd:

* [Az IoT ajánlott biztonsági eljárások][lnk-security-best-practices]
* [Az IoT-biztonsági architektúrája][lnk-security-architecture]
* [Az IoT üzembe][lnk-security-deployment]

Az IoT-központ képességeit további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

[lnk-security-best-practices]: iot-hub-security-best-practices.md
[lnk-security-architecture]: iot-hub-security-architecture.md
[lnk-security-deployment]: iot-hub-security-deployment.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md

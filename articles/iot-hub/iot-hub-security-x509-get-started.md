---
title: Oktatóanyag az Azure IoT Hub X.509-biztonság |} A Microsoft Docs
description: Ismerkedés az X.509-alapú biztonság az Azure IoT hub szimulált környezetben.
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: dkshir
ms.openlocfilehash: 61aefe70b7091f666dbb40bec6a4f25437f489d3
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39186934"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Az Azure IoT hub X.509 biztonsági beállítása

Ez az oktatóanyag az Azure IoT hub használatával biztonságossá kell lépéseket szimulálja a *X.509 tanúsítvány alapú hitelesítést*. Szemlélteti hogyan használhatja a nyílt forráskódú eszköz OpenSSL tanúsítványok helyileg létrehozása a Windows-gépen bemutatjuk. Azt javasoljuk, hogy használja-e ebben az oktatóanyagban csak tesztelési célokra. Éles környezet származó tanúsítványokat kell vásárolnia egy *legfelső szintű hitelesítésszolgáltató (CA)*. 

## <a name="prerequisites"></a>Előfeltételek
Ehhez az oktatóanyaghoz, hogy készen áll az alábbi forrásanyagokat:

- Létrehozott egy IoT hubra az Azure-előfizetésében. Lásd: [portálon keresztül IoT hub létrehozása](iot-hub-create-through-portal.md) a részletes lépéseket. 
- Rendelkezik [Visual Studio 2015 vagy Visual Studio 2017](https://www.visualstudio.com/vs/) telepítve van a gépén. 

<a id="getcerts"></a>

## <a name="get-x509-ca-certificates"></a>X.509-es Hitelesítésszolgáltatói tanúsítványok beolvasása
Az IoT hub az X.509-alapú biztonsági a kezdéshez szükséges egy [X.509-tanúsítványláncot](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), amely tartalmazza a legfelső szintű tanúsítvány, valamint a másnapi levéltanúsítványt köztes tanúsítványokra. 

A következő módszereket a tanúsítványok választhatja:
- Az X.509 tanúsítványok vásárlása egy *legfelső szintű hitelesítésszolgáltató (CA)*. Ez éles környezetben ajánlott.
VAGY
- Hozzon létre saját X.509-tanúsítványokat, mint például egy harmadik féltől származó eszközzel [OpenSSL](https://www.openssl.org/). Ez lesz finom fejlesztés és tesztelés céljából. Lásd: [kezelése teszt Hitelesítésszolgáltatói tanúsítványok, minták és oktatóanyagok](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) generálása információ tesztelése a CA-tanúsítványok a PowerShell vagy a Bash használatával. Ez az oktatóanyag további részeinek útmutatásai alapján létrehozott teszt CA-tanúsítványokat használ a [kezelése teszt Hitelesítésszolgáltatói tanúsítványok, minták és oktatóanyagok](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).


<a id="registercerts"></a>

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>Az IoT hubhoz x.509-es Hitelesítésszolgáltatói tanúsítványok regisztrálása

Ezek a lépések bemutatják, hogyan adhat hozzá egy új hitelesítésszolgáltató az IoT hubhoz a portálon keresztül.

1. Az Azure Portalon keresse meg az IoT hubot, és nyissa meg a **beállítások** > **tanúsítványok** menü. 
2. Kattintson a **Hozzáadás** egy új tanúsítványt.
3. Adjon meg egy rövid nevet a tanúsítványhoz. Válassza ki a legfelső szintű tanúsítvány fájlt nevű *RootCA.cer* az előző szakaszban, a gép számára. Kattintson a **Feltöltés** gombra.
4. Miután a tanúsítvány feltöltése sikerült értesítést kap, kattintson a **mentése**.

    ![Tanúsítvány feltöltése](./media/iot-hub-security-x509-get-started/add-new-cert.png)  

   Ez azt mutatja majd a tanúsítvány a **Tanúsítványkezelő** listája. Megjegyzés: a **állapot** , de ez a tanúsítvány *ellenőrzött*.

5. Kattintson az előző lépésben hozzáadott a tanúsítványt.

6. Az a **Tanúsítványadatok** panelen kattintson a **ellenőrző kód előállítása**.

7. Létrehoz egy **ellenőrzőkódot** ellenőrzése a tanúsítvány tulajdonosa. Másolja a vágólapra a kódot. 

   ![Tanúsítvány ellenőrzése](./media/iot-hub-security-x509-get-started/verify-cert.png)  

8. Most meg kell bejelentkeznie, ez *ellenőrzőkódot* és a titkos kulcs társítása az x.509-es Hitelesítésszolgáltatói tanúsítvány, amely állít elő, aláírás. Nincsenek elérhető ezen aláírási folyamat, például OpenSSL eszközöket. Ez más néven a [birtokában megvalósíthatósági](https://tools.ietf.org/html/rfc5280#section-3.1). 3. lépés [kezelése teszt Hitelesítésszolgáltatói tanúsítványok, minták és oktatóanyagok](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) egy ellenőrző kódot állít elő.
 
9. Töltse fel az eredményül kapott aláírás fent 8. lépés-az IoT hubhoz a portálon. Az a **Tanúsítványadatok** panel az Azure Portalon lépjen a **ellenőrző tanúsítvány .pem or .cer fájlja**, és válassza ki például az aláírás *VerifyCert4.cer*a minta PowerShell parancs segítségével hozta létre a _fájlkezelő_ ikon mellett azt.

10. Miután sikeresen feltöltötte a tanúsítványt, kattintson a **ellenőrizze**. A **állapot** a tanúsítvány módosításainak **_ellenőrzött_** a a **tanúsítványok** panelen. Kattintson a **frissítése** Ha nem frissíti automatikusan.

   ![Töltse fel a tanúsítvány-ellenőrzés](./media/iot-hub-security-x509-get-started/upload-cert-verification.png)  


<a id="createdevice"></a>

## <a name="create-an-x509-device-for-your-iot-hub"></a>X.509-eszközt az IoT hub létrehozása

1. Az Azure Portalon keresse meg az IoT hub **Device Explorer**.

2. Kattintson a **Hozzáadás** egy új eszköz hozzáadásához. 

3. Adjon egy rövid nevet a **Eszközazonosító**, és válassza ki **_X.509 Hitelesítésszolgáltatói aláírással_** , a **hitelesítési típus**. Kattintson a **Save** (Mentés) gombra.

   ![X.509-eszköz létrehozása a portálon](./media/iot-hub-security-x509-get-started/create-x509-device.png)



<a id="authenticatedevice"></a>

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Az X.509-tanúsítványokat az X.509-eszköz hitelesítése

Az X.509-eszköz hitelesítéséhez, kell először jelentkeznie a hitelesítésszolgáltató tanúsítványát az eszközre. Levéleszközök aláírása a lapkagyártó műhelyben, ahol gyártási eszközök engedélyezve van ennek megfelelően a szokásos módon történik. Mivel az eszköz megfelelően egy gyártót a a másikra, egyes gyártók aláírási művelet rögzített mint belül a tanúsítványlánc egy közbenső tanúsítvány. A végeredmény a tanúsítványlánc a Hitelesítésszolgáltatói tanúsítvány, az eszköz levéltanúsítványt. 4. lépés [kezelése teszt Hitelesítésszolgáltatói tanúsítványok, minták és oktatóanyagok](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) létrehoz egy eszköz tanúsítványt.

Ezután bemutatjuk hogyan hozhat létre egy C#-alkalmazás az X.509-eszköz regisztrálása az IoT hub szimulálásához. Küldeni fogunk értékek hőmérséklettel és páratartalommal kapcsolatos szimulált eszközről érkező a hub. Vegye figyelembe, hogy ebben az oktatóanyagban létre fogunk hozni csak az alkalmazást. Gyakorlatként hagyják közvetíthet az olvasóknak a, amely az események a szimulált eszköz által küldött választ küld az IoT Hub-szolgáltatásalkalmazás létrehozása. A C#-alkalmazás azt feltételezi, hogy követte a lépéseket a [kezelése teszt Hitelesítésszolgáltatói tanúsítványok, minták és oktatóanyagok](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).

1. A Visual Studióban hozzon létre egy új Visual C# Windows klasszikus Asztalialkalmazás-projektet a Konzolalkalmazás projekt sablon használatával. Adja a projektnek **SimulateX509Device**.
   ![X.509-eszköz projekt létrehozása a Visual Studióban](./media/iot-hub-security-x509-get-started/create-device-project.png)

2. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateX509Device** projektre, és kattintson a **NuGet-csomagok kezelése...** . Válassza a NuGet-Csomagkezelő ablak **Tallózás** és keressen rá a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti, és hozzáad egy rá mutató hivatkozást az Azure IoT eszköz SDK NuGet-csomagot és annak függőségeit.
   ![Eszköz SDK NuGet-csomag hozzáadása a Visual Studióban](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

3. Adja hozzá a következő kódsorokat felső részén a *Program.cs* fájlt:
    
    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

4. Adja hozzá a következő kódsorokat belül a **Program** osztály:
    
    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```
   Használt eszközök rövid nevét használja a helyére az előző szakaszban _< your_device_id >_ helyőrző.

5. Adja hozzá a következő függvény létrehozásához hőmérséklettel és páratartalommal kapcsolatos véletlenszerű számot, és ezeket az értékeket küldjön a hubnak:
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

6. Végül adja hozzá a következő kódsort a **fő** függvényt, és cserélje le a helyőrzőket _eszközazonosító_, _az iot hub-neve-_ és  _Absolute-path-to-Your-Device-PFX-File_ szükség szerint a telepítő által.
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
   Ez a kód csatlakozik az IoT hub kapcsolati karakterláncát az X.509-eszköz létrehozásával. Miután sikeresen csatlakozott, ezután hőmérséklettel és páratartalommal kapcsolatos eseményeket küld az eseményközpont, és megvárja, amíg a választ. 
7. Mivel ez az alkalmazás hozzáfér egy *.pfx* fájl, szükség lehet végrehajtani ezt a *rendszergazdai* mód. A Visual Studio-megoldás létrehozásához. Nyisson meg egy új parancsablakot, mint egy **rendszergazda**, és lépjen abba a mappába, amely tartalmazza ezt a megoldást. Keresse meg a *bin/Debug* belül a megoldás mappa elérési útja. Az alkalmazás futtatásához **SimulateX509Device.exe** származó a _rendszergazdai_ parancsablakot. Az eszköz sikeresen csatlakozik a hubhoz, és az események kell megjelennie. 
   ![Eszköz-alkalmazás futtatása](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="see-also"></a>Lásd még
Az IoT-megoldás biztosításával kapcsolatos további tudnivalókért lásd:

* [IoT ajánlott biztonsági eljárások][lnk-security-best-practices]
* [IoT-biztonsági architektúrája][lnk-security-architecture]
* [Az IoT-környezet biztonságossá tétele][lnk-security-deployment]

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

[lnk-security-best-practices]: ../iot-fundamentals/iot-security-best-practices.md
[lnk-security-architecture]: ../iot-fundamentals/iot-security-architecture.md
[lnk-security-deployment]: ../iot-fundamentals/iot-security-deployment.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md

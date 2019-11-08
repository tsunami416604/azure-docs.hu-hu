---
title: Oktatóanyag X. 509 biztonsághoz az Azure IoT Hubban | Microsoft Docs
description: Az Azure IoT hub X. 509 alapú biztonságának első lépései szimulált környezetben.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 32219eeaee7980b685ac3453c6af3beff716abe2
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824089"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Az X. 509 Biztonság beállítása az Azure IoT hub-ban

Ez az oktatóanyag azokat a lépéseket mutatja be, amelyekkel biztonságossá teheti az Azure IoT hub-t az *X. 509 Tanúsítványos hitelesítés*használatával. Az illusztrációk alapján a nyílt forráskódú eszköz OpenSSL segítségével helyileg hozhat létre tanúsítványokat a Windows rendszerű gépen. Javasoljuk, hogy ezt az oktatóanyagot csak tesztelési célokra használja. Éles környezetben a tanúsítványokat a *legfelső szintű hitelesítésszolgáltatóktól (CA)* kell megvásárolnia.

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következő erőforrások szükségesek:

* Létrehozott egy IoT hubot az Azure-előfizetésével. A részletes lépésekért lásd: [IoT hub létrehozása a portálon](iot-hub-create-through-portal.md) .

* A [Visual studio 2017 vagy a Visual studio 2019](https://www.visualstudio.com/vs/) telepítve van.

## <a name="get-x509-ca-certificates"></a>X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok beolvasása

A IoT Hub X. 509 tanúsítványalapú biztonsága megköveteli, hogy egy [x. 509 tanúsítványláncot](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)kell kezdenie, amely magában foglalja a főtanúsítványt, valamint a leküldéses tanúsítványig a közbenső tanúsítványokat.

A tanúsítványok beszerzéséhez a következő lehetőségek közül választhat:

* X. 509 tanúsítványok vásárlása a *legfelső szintű hitelesítésszolgáltatótól (CA)* . Ez a módszer éles környezetekben ajánlott.

* Hozzon létre saját X. 509 tanúsítványokat egy külső gyártótól származó eszközzel, például az [OpenSSL](https://www.openssl.org/)használatával. Ez a technika tesztelési és fejlesztési célokra is kiváló. A HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok a PowerShell vagy a bash használatával történő létrehozásával kapcsolatos információkért tekintse meg a [teszt hitelesítésszolgáltatói tanúsítványok kezelése mintákhoz és oktatóanyagokhoz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) című témakört. Ennek az oktatóanyagnak a többi része a teszt hitelesítésszolgáltatói tanúsítványok [tesztelése a mintákhoz és az oktatóanyagokhoz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)című témakör útmutatását követve létrehozott hitelesítésszolgáltatói tanúsítványokat használ.

* Létrehoz egy meglévő legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány által aláírt [X. 509 köztes hitelesítésszolgáltatói tanúsítványt](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) , és feltölti azt az elosztóba. A köztes tanúsítvány feltöltése és ellenőrzése után az alább leírtaknak megfelelően a rendszer az alább említett legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány helyén is használható. A köztes HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány létrehozásához és aláírásához az OpenSSL ([OpenSSL REQ](https://www.openssl.org/docs/man1.1.0/man1/req.html) és [OpenSSL CA](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) eszközök is használhatók.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok regisztrálása az IoT hub-ban

Ezek a lépések bemutatják, hogyan adhat hozzá új hitelesítésszolgáltatót az IoT hub-hoz a portálon keresztül.

1. A Azure Portal navigáljon az IoT hubhoz, és válassza a **beállítások** > a központ **tanúsítványai** elemet.

1. Új tanúsítvány hozzáadásához válassza a **Hozzáadás** lehetőséget.

1. A **tanúsítvány neve**mezőben adjon meg egy felhasználóbarát megjelenítendő nevet, majd válassza ki az előző szakaszban létrehozott tanúsítványfájl a számítógépről.

1. Ha értesítést kap arról, hogy a tanúsítvány feltöltése sikeresen megtörtént, válassza a **Mentés**lehetőséget.

    ![Tanúsítvány feltöltése](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   A tanúsítvány nem **ellenőrzött**állapotú tanúsítványok listájában jelenik meg.

1. Válassza ki az imént hozzáadott tanúsítványt a **tanúsítvány részleteinek**megjelenítéséhez, majd válassza az **ellenőrző kód előállítása**lehetőséget.

   ![Tanúsítvány ellenőrzése](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Másolja az **ellenőrző kódot** a vágólapra. Ezzel ellenőrzi a tanúsítvány tulajdonjogát.

1. A [minták és oktatóanyagok tesztelésére szolgáló hitelesítésszolgáltatói tanúsítványok kezeléséhez](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)kövesse a 3. lépést.  Ez a folyamat aláírja az ellenőrző kódot az X. 509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványhoz társított titkos kulccsal, amely aláírást generál. Az aláírási folyamat elvégzéséhez rendelkezésre állnak eszközök, például az OpenSSL. Ez a folyamat a [birtoklás igazolása](https://tools.ietf.org/html/rfc5280#section-3.1).

1. A **tanúsítvány részletei**területen az **ellenőrzés tanúsítvány. PEM vagy. cer fájlban**keresse meg és nyissa meg az aláírási fájlt. Ezután válassza az **ellenőrzés**lehetőséget.

   A tanúsítvány állapota **ellenőrizve**állapotra módosult. Válassza a **frissítés** lehetőséget, ha a tanúsítvány nem frissül automatikusan.

## <a name="create-an-x509-device-for-your-iot-hub"></a>X. 509 eszköz létrehozása az IoT hub számára

1. A Azure Portal navigáljon az IoT hubhoz, majd válassza a **felfedezők** > **IoT-eszközök**elemet.

1. Új eszköz hozzáadásához válassza az **új** lehetőséget.

1. Az **eszköz azonosítója**mezőben adjon meg egy felhasználóbarát megjelenítendő nevet. A **Hitelesítés típusa**mezőben válassza az **X. 509 hitelesítésszolgáltató aláírva**lehetőséget, majd kattintson a **Mentés**gombra.

   ![X. 509 eszköz létrehozása a portálon](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Hitelesítse X. 509 eszközét az X. 509 tanúsítvánnyal

Az X. 509 eszköz hitelesítéséhez először alá kell írnia az eszközt a HITELESÍTÉSSZOLGÁLTATÓI tanúsítvánnyal. A levelek eszközeinek aláírása általában a gyártási üzemben történik, ahol ennek megfelelően engedélyezték a gyártási eszközöket. Ahogy az eszköz az egyik gyártóról a másikra mutat, a rendszer minden gyártó aláírási műveletét közbenső tanúsítványként rögzíti a láncon belül. Ennek eredményeképpen a HITELESÍTÉSSZOLGÁLTATÓI tanúsítványból az eszközön lévő levél tanúsítványára vonatkozó tanúsítványlánc szerepel. A [tesztelési hitelesítésszolgáltatói tanúsítványok a mintákhoz és oktatóanyagokhoz való kezelésének](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) 4. lépése egy eszköz tanúsítványát hozza létre.

Ezután megmutatjuk, hogyan hozhat létre egy C# alkalmazást az IoT hub-hoz regisztrált X. 509 eszköz szimulálásához. A szimulált eszköz hőmérséklet-és páratartalom-értékét a hubhoz küldi a rendszer. Ebben az oktatóanyagban csak az eszköz alkalmazást fogjuk létrehozni. Az olvasók feladata, hogy létrehozza a IoT Hub szolgáltatásalkalmazás létrehozását, amely választ küld a szimulált eszköz által küldött eseményekre. Az C# alkalmazás feltételezi, hogy követte a [teszt hitelesítésszolgáltatói tanúsítványok kezelése a mintákhoz és az oktatóanyagokhoz](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)című témakör lépéseit.

1. Nyissa meg a Visual studiót, válassza az **új projekt létrehozása**lehetőséget, majd válassza a **Console app (.NET-keretrendszer)** projekt sablonját. Kattintson a **Tovább** gombra.

1. Az **új projekt konfigurálása**lapon nevezze el a projekt *SimulateX509Device*, majd válassza a **Létrehozás**lehetőséget.

   ![X. 509 eszköz projekt létrehozása a Visual Studióban](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Megoldáskezelő kattintson a jobb gombbal a **SimulateX509Device** projektre, majd válassza a **NuGet-csomagok kezelése**lehetőséget.

1. A **NuGet csomagkezelő eszközben**válassza a **Tallózás** lehetőséget, és keresse meg a **Microsoft. Azure. Devices. Client**elemet. Válassza az **Install** (Telepítés) lehetőséget.

   ![Device SDK NuGet-csomag hozzáadása a Visual Studióban](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Ez a lépés letölti, telepíti és hozzáadja az Azure IoT Device SDK NuGet csomagra és annak függőségeire mutató hivatkozást.

1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

    ```CSharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Adja hozzá a következő mezőket a **program** osztályhoz:

    ```CSharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    A _< your_device_id >_ helyett használja az előző szakaszban használt felhasználóbarát eszköznév nevét.

1. Adja hozzá a következő függvényt a hőmérséklet és a páratartalom véletlenszerű számának létrehozásához, majd küldje el ezeket az értékeket a hubhoz:

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

1. Végül adja hozzá a következő sornyi kódot a **fő** függvényhez, és cserélje le a helyőrzőket az _eszköz-azonosító_, _a-IOT-hub-Name_és az _abszolút elérésű útvonal-az-Ön-eszköz-pfx-fájlra_ a telepítéshez szükséges módon.

    ```CSharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
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

   Ez a kód az X. 509 eszköz kapcsolati karakterláncának létrehozásával csatlakozik az IoT hubhoz. A sikeres csatlakozás után a rendszer hőmérséklet-és páratartalom-eseményeket küld a hubhoz, és megvárja a válaszát.

1. Futtassa az alkalmazást. Mivel ez az alkalmazás egy *. pfx* -fájlhoz fér hozzá, lehetséges, hogy rendszergazdaként kell futtatnia ezt az alkalmazást.

   1. Hozza létre a Visual Studio-megoldást.

   1. Nyisson meg egy új parancssori ablakot a **Futtatás rendszergazdaként**parancs használatával.  

   1. Keresse meg a megoldást tartalmazó mappát, majd navigáljon a megoldás mappájában található *bin/debug* elérési útra.

   1. Futtassa az alkalmazást a **SimulateX509Device. exe** fájlból a parancssorból.

   Az eszköznek sikeresen csatlakoznia kell a hubhoz, és el kell küldenie az eseményeket.

   ![Eszköz alkalmazásának futtatása](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a IoT-megoldás biztonságossá tételéről, olvassa el a következő témakört:

* [Ajánlott IoT biztonsági eljárások](../iot-fundamentals/iot-security-best-practices.md)

* [IoT biztonsági architektúra](../iot-fundamentals/iot-security-architecture.md)

* [Az IoT-környezet biztonságossá tétele](../iot-fundamentals/iot-security-deployment.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [AI üzembe helyezése az Edge-eszközökön Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

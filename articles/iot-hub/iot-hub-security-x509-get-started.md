---
title: Oktatóanyag az X.509 biztonságához az Azure IoT Hubban | Microsoft dokumentumok
description: Az X.509 alapú biztonság az Azure IoT hub egy szimulált környezetben.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: 968241eff1bcab449f9a4def7a394a508461ec95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271173"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>Az Azure IoT Hub X.509-es védelmének beállítása

Ez az oktatóanyag az Azure IoT-központ *x.509-es tanúsítványhitelesítés*használatával történő védelméhez szükséges lépéseket mutatja be. Szemléltetéscéljából az OpenSSL nyílt forráskódú eszközét használjuk tanúsítványok helyi létrehozására a Windows-számítógépen. Azt javasoljuk, hogy ezt az oktatóanyagot csak tesztelési célokra használja. Éles környezetben a tanúsítványokat egy *legfelső szintű hitelesítésszolgáltatótól kell megvásárolnia.*

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag megköveteli, hogy a következő erőforrások készen álljanak:

* Létrehozott egy IoT-központot az Azure-előfizetésével. Részletes lépésekért [tekintse meg az IoT-központ létrehozása a portálon keresztül](iot-hub-create-through-portal.md) című témakört.

* A [Visual Studio 2017 vagy a Visual Studio 2019](https://www.visualstudio.com/vs/) telepítve van.

## <a name="get-x509-ca-certificates"></a>X.509 hitelesítésszolgáltatói tanúsítványok beszerezése

Az IoT Hub X.509 tanúsítványalapú biztonsága megköveteli, hogy egy [X.509 tanúsítványlánccal](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification)kezdje, amely tartalmazza a főtanúsítványt, valamint a köztes tanúsítványokat a levéltanúsítványig.

A tanúsítványok bekéselésének az alábbi módokközül választhat:

* X.509-es tanúsítványok vásárlása *legfelső szintű hitelesítésszolgáltatótól*. Ez a módszer éles környezetekben ajánlott.

* Hozzon létre saját X.509-es tanúsítványait egy külső gyártótól származó eszközzel, például [az OpenSSL-rel.](https://www.openssl.org/) Ez a technika rendben van a vizsgálati és fejlesztési célokra. A teszthitelesítési tanúsítványok PowerShell vagy Bash használatával történő létrehozásáról a [hitelesítésszolgáltatói tanúsítványok](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) teszteléséről a minták és oktatóanyagok kezelése című témakörben található. Az oktatóanyag többi része a teszthitelesítési tanúsítványokat használja, amelyeket a [teszthitelesítési tanúsítványok kezelése a mintákhoz és az oktatóanyagokhoz tartozó teszthitelesítési tanúsítványok kezelése](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)című útmutató utasításai nak megfelelően hoz létre.

* Hozzon létre egy [X.509 köztes hitelesítésszolgáltatói tanúsítványt,](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) amelyet egy meglévő legfelső szintű hitelesítésszolgáltatói tanúsítvány írt alá, és töltse fel a hubra. A köztes tanúsítvány feltöltése és ellenőrzése után az alábbiakban megadottak szerint használható az alább említett legfelső szintű hitelesítésszolgáltatói tanúsítvány helyén. Az olyan eszközök, mint az OpenSSL ([openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) és [openssl ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) köztes hitelesítésszolgáltatói tanúsítvány létrehozására és aláírására használhatók.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>X.509 hitelesítésszolgáltatói tanúsítványok regisztrálása az IoT-központba

Ezek a lépések bemutatják, hogyan adhat hozzá egy új hitelesítésszolgáltatót az IoT hubhoz a portálon keresztül.

1. Az Azure Portalon keresse meg az IoT-központot, és válassza a **beállítások** > **tanúsítványok** a hub.

1. Új tanúsítvány hozzáadásához válassza a **Hozzáadás** lehetőséget.

1. A **Tanúsítvány neve**mezőbe írjon be egy rövid megjelenítendő nevet, és jelölje ki a számítógépről az előző szakaszban létrehozott tanúsítványfájlt.

1. Miután értesítést kap a tanúsítvány sikeres feltöltésről, válassza a **Mentés**lehetőséget.

    ![Tanúsítvány feltöltése](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   A tanúsítvány nem ellenőrzött állapotú tanúsítványok listájában jelenik **meg.**

1. Jelölje ki az imént hozzáadott tanúsítványt a **Tanúsítvány részleteinek**megjelenítéséhez, majd válassza **az Ellenőrző kód létrehozása lehetőséget.**

   ![Tanúsítvány ellenőrzése](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Másolja az **ellenőrző kódot** a vágólapra. A tanúsítvány tulajdonjogának ellenőrzésére használhatja.

1. Kövesse a [3.](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)  Ez a folyamat aláírja az ellenőrző kódot az X.509 hitelesítésszolgáltatói tanúsítványhoz tartozó személyes kulccsal, amely aláírást hoz létre. Az aláírási folyamat végrehajtásához rendelkezésre állnak eszközök, például openSSL. Ez a folyamat az úgynevezett [bizonyíték a birtoklás](https://tools.ietf.org/html/rfc5280#section-3.1).

1. A **Tanúsítvány részletei**csoportban a **.pem vagy .cer tanúsítvány fájlban**keresse meg és nyissa meg az aláírásfájlt. Ezután válassza **az Ellenőrzés lehetőséget.**

   A tanúsítvány állapota **Ellenőrzött**állapotúra változik. Válassza a **Frissítés** lehetőséget, ha a tanúsítvány nem frissül automatikusan.

## <a name="create-an-x509-device-for-your-iot-hub"></a>X.509-es eszköz létrehozása az IoT hubhoz

1. Az Azure Portalon keresse meg az IoT-központot, és válassza az **Explorers** > **IoT-eszközök**lehetőséget.

1. Új eszköz hozzáadásához válassza az **Új** lehetőséget.

1. Az **Eszközazonosító**mezőbe írjon be egy rövid megjelenítendő nevet. A **Hitelesítés típusa mezőben**válassza az **X.509 hitelesítésszolgáltató aláírása**lehetőséget, majd kattintson a Mentés **gombra.**

   ![X.509-es eszköz létrehozása a portálon](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Az X.509-es eszköz hitelesítése az X.509-es tanúsítványokkal

Az X.509-es eszköz hitelesítéséhez először alá kell írnia az eszközt a hitelesítésszolgáltatói tanúsítvánnyal. A levéleszközök aláírása általában a gyártóüzemben történik, ahol a gyártási eszközöket ennek megfelelően engedélyezték. Ahogy az eszköz egyik gyártóról a másikra kerül, minden gyártó aláírási művelete köztes tanúsítványként rögzítésre kerül a láncon belül. Az eredmény egy tanúsítványlánc a hitelesítésszolgáltatói tanúsítványtól az eszköz levéltanúsítványához. A [minták és oktatóanyagok hitelesítésszolgáltatói tanúsítványainak kezelése](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) 4.

Ezután bemutatjuk, hogyan hozhat létre egy C# alkalmazást az IoT hubhoz regisztrált X.509-es eszköz szimulálására. A szimulált eszközről hőmérséklet- és páratartalom-értékeket küldünk az ön központjába. Ebben az oktatóanyagban csak az eszközalkalmazást hozzuk létre. Az olvasók nak edzésként marad az IoT Hub szolgáltatásalkalmazás létrehozása, amely választ küld a szimulált eszköz által küldött eseményekre. A C# alkalmazás feltételezi, hogy a [minták és oktatóanyagok teszthitelesítési tanúsítványainak kezelése](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)című lépést követte.

1. Nyissa meg a Visual Studio alkalmazást, válassza **az Új projekt létrehozása**lehetőséget, majd válassza a Console App **(.NET Framework)** projektsablont. Válassza a **Tovább lehetőséget.**

1. Az **Új projekt konfigurálása**területen nevezze el a *SimulateX509Device nevű projektet,* majd válassza a Create **(Létrehozás) lehetőséget.**

   ![X.509-es eszközprojekt létrehozása a Visual Studióban](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateX509Device** projektre, majd válassza **a NuGet csomagok kezelése parancsot.**

1. A **NuGet csomagkezelőben**válassza a **Tallózás** lehetőséget, és keresse meg a **Microsoft.Azure.Devices.Client**elemet. Válassza az **Install** (Telepítés) lehetőséget.

   ![Eszköz SDK NuGet csomag hozzáadása a Visual Studióban](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Ez a lépés letölti, telepíti, és hozzáadja a hivatkozást az Azure IoT-eszköz SDK NuGet csomag és a függőségek.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Adja hozzá a következő mezőket a **Program** osztályhoz:

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Használja az előző szakaszban használt rövid eszköznevet _<your_device_id>_ helyett.

1. Adja hozzá a következő függvényt a hőmérséklet és a páratartalom véletlenszerű számának létrehozásához, és küldje el ezeket az értékeket a hubnak:

    ```csharp
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

1. Végül adja hozzá a következő kódsorokat a **Fő** függvényhez, a helyőrzők _eszközazonosítója_, _a hub-neve_és az _abszolút-path-to-your-device-pfx-file_ helyett, ahogy azt a beállítás megköveteli.

    ```csharp
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

   Ez a kód az IoT hubhoz csatlakozik az X.509-es eszköz kapcsolati karakterláncának létrehozásával. Miután sikeresen csatlakozott, ezután elküldi a hőmérséklet és a páratartalom eseményeket a hub, és várja a választ.

1. Futtassa az alkalmazást. Mivel ez az alkalmazás hozzáfér egy *.pfx* fájlhoz, előfordulhat, hogy rendszergazdaként kell futtatnia ezt az alkalmazást.

   1. Készítse el a Visual Studio-megoldást.

   1. Nyisson meg egy új parancssorablakot a **Futtatás rendszergazdaként paranccsal.**  

   1. Nyissa meg a megoldást tartalmazó mappát, majd keresse meg a megoldásmappában található *bin/Debug* elérési utat.

   1. Futtassa az alkalmazást **SimulateX509Device.exe** a parancssorból.

   Látnia kell, hogy az eszköz sikeresen csatlakozik a hubhoz, és elküldi az eseményeket.

   ![Eszközalkalmazás futtatása](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>További lépések

Az IoT-megoldás biztonságossá tétele további információ:

* [Az IoT biztonságának gyakorlati tanácsai](../iot-fundamentals/iot-security-best-practices.md)

* [IoT biztonsági architektúra](../iot-fundamentals/iot-security-architecture.md)

* [Az IoT üzemelő példányának védelme](../iot-fundamentals/iot-security-deployment.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)

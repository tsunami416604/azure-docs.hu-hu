## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása

Ebben a szakaszban használhatja a [Azure-portálon] [ lnk-azure-portal] egy eszközidentitás az IoT hub a identitás rendszerleíró adatbázis létrehozásához. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. További információkért lásd az [IoT Hub fejlesztői útmutatójának][lnk-devguide-identity] „Identitásjegyzék” című szakaszát. A **eszköz Explorer** a portál segítségével a, hozzon létre egy eszköz egyedi azonosítója és kulcsa, az eszköz segítségével azonosítja magát, amikor az IoT-központ csatlakozik. Az eszközazonosítókban különbözőnek számítanak a kis- és nagybetűk.

1. Győződjön meg arról, hogy be vannak jelentkezve a [Azure-portálon][lnk-azure-portal].

1. Az ugrósávon kattintson **összes erőforrás** és az IoT hub erőforrás található.

    ![Keresse meg az Iot hub][img-find-iothub]

1. Az IoT hub erőforrás már meg van nyitva, kattintson a **eszköz Explorer** eszközzel, és kattintson a **Hozzáadás** tetején. Adjon meg a nevet az új eszköz, például: **myDeviceId**, és kattintson a **mentése**.

    ![Az eszközazonosító létrehozása a portálon][img-create-device]

   Ezzel létrehoz egy új eszközidentitás az IoT hub.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Az a **eszköz Explorer**tartozó eszközök listája, kattintson az újonnan létrehozott eszközre, és jegyezze fel az a **kapcsolati karakterlánc---elsődleges kulcs**. 

    ![Eszköz kapcsolati karakterlánc][img-connection-string]

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszközidentitásokat. Az eszközazonosítókat és kulcsokat biztonsági hitelesítő adatokként tárolja, valamint tartalmaz egy engedélyezve/letiltva jelzőt, amellyel letilthatja egy adott eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információkért lásd az [Azure IoT Hub fejlesztői útmutatóját][lnk-devguide-identity].

<!-- Images. -->
[img-find-iothub]: ./media/iot-hub-get-started-create-device-identity-portal/find-iothub.png
[img-create-device]: ./media/iot-hub-get-started-create-device-identity-portal/create-identity-portal.png
[img-connection-string]: ./media/iot-hub-get-started-create-device-identity-portal/device-connection-string.png


<!-- Links -->
[lnk-azure-portal]: https://portal.azure.com
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md


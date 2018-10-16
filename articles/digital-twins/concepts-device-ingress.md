---
title: Az Azure digitális Twins kapcsolat- és telemetriabevitelt eszköz |} A Microsoft Docs
description: Bevezetés az Azure digitális Twins eszköz áttekintése
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 61d05a7e9936a0edd17c5528ce4f55233b6e7e0e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324114"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Eszköz kapcsolat- és telemetriabevitelt

Az eszközök és érzékelők által küldött telemetriai adatokat bármely IoT-megoldás gerincét alkotnak. Mint ilyen ezek az erőforrások különálló jelölő és kezelésének őket egy helyen kontextusában az IoT-alkalmazások fejlesztése a fő szempont. Azure digitális Twins eszközök és a egy térbeli intelligencia graph érzékelők összesítésével egyszerűbbé teszi az IoT-megoldások fejlesztését.

Első lépésként egy `IoTHub` erőforrást kell létrehozni a térbeli grafikon, így minden eszköz alatt üzenetek küldéséhez a legfelső szintű hely gyökérmappájában. Után az IoT Hub létrehozása, és olyan eszközök vannak regisztrálva, a digitális Twins példány belül, az eszközök a digitális Twins szolgáltatás keresztül történő adatküldés megkezdheti a [Azure IoT eszközoldali SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks).

Egy lépésenkénti útmutató előkészítési eszközökön található a [Oktatóanyaggal, telepítheti és konfigurálhatja a digitális Twins](tutorial-facilities-setup.md). Egy pillanat alatt a lépések a következők:

- Az Azure digitális Twins üzembe a [Azure Portalon](https://portal.azure.com)
- Tárolóhelyek létrehozása a Graph
- Hozzon létre egy `IoTHub` erőforrás, és rendelje hozzá a gráfban található szóközt
- Eszközök és érzékelők létrehozása a graph, és hozzárendelheti azokat a fenti lépések során létrehozott tárolóhelyek
- Hozzon létre egy megfeleltetőben megadott feltételek alapján telemetriai üzeneteket szűrése
- Hozzon létre egy [ **felhasználó által definiált függvény** ](concepts-user-defined-functions.md) és rendelje hozzá a grafikon a telemetriai üzeneteket egyéni feldolgozásához szóközt
- Rendelhet egy szerepkört, hogy a felhasználó által definiált függvény a grafikon adatainak eléréséhez
- A digitális Twins felügyeleti API-k az IoT Hub eszköz kapcsolati karakterláncának lekérése
- Az eszköz kapcsolati karakterláncának konfigurálása az Azure IoT eszközoldali SDK-eszközön

Az alábbiakban bemutatjuk, a digitális Twins felügyeleti API-tól az eszköz IoT Hub kapcsolati karakterláncának beszerzése és az érzékelő-alapú telemetriát küldjön az IoT Hub telemetriai üzenetformátum használata. Digitális Twins telemetriai társítandó belül az adatok biztosítása érdekében a térbeli graph érzékelő kap minden részének feldolgozása és irányítja a megfelelő térbeli környezetében van szükség.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Az IoT Hub eszköz kapcsolati karakterláncának lekérése a felügyeleti API-ból

Tegye meg az eszköz API-t egy GET hívást egy `includes=ConnectionString` paramétert az IoT Hub eszköz kapcsolati karakterláncának beolvasása. Eszköz egyedi azonosítója vagy hardver Azonosítóját az adott eszköz szerint szűrheti:

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| Egyéni attribútum neve | Cserélje le |
| --- | --- |
| `yourManagementApiUrl` | A felügyeleti API teljes URL-címe |
| `yourDeviceGuid` | Az eszköz azonosítója |
| `yourDeviceHardwareId` | Az eszköz hardveres azonosítója |

A válasz hasznos adatban, másolja ki az eszköz `connectionString` tulajdonság, amely adatokat küldeni az Azure digitális Twins fogja használni az Azure IoT eszközoldali SDK hívása során.

## <a name="device-to-cloud-message"></a>Felhőüzenet eszköz

Az eszköz üzenet formátuma és a hasznos adat a megoldás igényei szerint személyre is szabhatja. Használhat bármilyen adategyezményben, egy bájttömböt vagy által támogatott stream lehet szerializálni a [Azure IoT Device Client Message osztály, üzenet (byte [] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Az üzenet lehet egyéni bináris formátum a választott mindaddig, amíg a megfelelő felhasználó által megadott függvények adategyezményben vissza. Egy eszköz a felhőbe irányuló üzenetet egyetlen követelménye, hogy karbantartása vonatkozó tulajdonságokat (lásd alább), hogy az üzenet megfelelően legyen irányítva a feldolgozó motor.

### <a name="telemetry-properties"></a>Telemetriai adatok tulajdonságai

Miközben hasznos tartalmát egy `Message` tetszőleges adatokat lehet van néhány követelmény 256 KB-os méretig, a másolatot a várt [Message.Properties](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet). Az alábbi lépéseket követve tükrözi a rendszer támogatja a szükséges és választható tulajdonságok:

| Tulajdonság neve | Érték | Szükséges | Leírás |
|---|---|---|---|
| DigitalTwins-Telemetria | 1.0 | igen | Egy állandó érték, amely azonosítja a rendszer üzenetet |
| DigitalTwins-SensorHardwareId | `string(72)` | igen | Egy egyedi azonosítója az érzékelő küld a `Message`. Ezt az értéket meg kell egyeznie egy objektum `HardwareId` tulajdonság esetében a rendszer feldolgozza azt. Például: `00FF0643BE88-CO2` |
| CreationTimeUtc | `string` | nem | Egy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) azonosításával a tartalom a mintavételi idő dátum karakterlánc formátumú. Például: `2018-09-20T07:35:00.8587882-07:00` |
| CorrelationId | `string` | nem | A `uuid` , amelyek segítségével nyomkövetési eseményeket a rendszer között. Például: `cec16751-ab27-405d-8fe6-c68e1412ce1f`

### <a name="sending-your-message-to-digital-twins"></a>Az üzenetet küld a digitális Twins

Használja a DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) vagy [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) hívás elküldeni az üzenetet a digitális Twins szolgáltatásnak.

## <a name="next-steps"></a>További lépések

Azure digitális Twins' adatfeldolgozás és a felhasználó által megadott függvények képességekkel kapcsolatos további információkért olvassa el a [Azure digitális Twins adatfeldolgozás és a felhasználó által definiált függvények](concepts-user-defined-functions.md).


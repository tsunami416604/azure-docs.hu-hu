---
title: Az Azure digitális Twins kapcsolat- és telemetriabevitelt eszköz |} A Microsoft Docs
description: Ahhoz, hogy egy eszköz előkészítése az Azure digitális Twins áttekintése
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 374a27b10b3928838b10e4fba24e1f36721c162a
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53433679"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Eszközkapcsolatok és bemenő telemetriaadatok

Az eszközök és érzékelők által küldött telemetriai adatokat bármely IoT-megoldás gerincét alkotnak. Ezeket a különböző erőforrásokat jelölik, és kezelheti őket egy helyen kontextusában olyan vezető problémáit a IoT-alkalmazások fejlesztéséhez. Az Azure digitális Twins leegyszerűsíti az eszközök és a egy térbeli intelligencia graph érzékelők összesítésével IoT-megoldások fejlesztése.

Első lépésként hozzon létre egy Azure IoT Hub-erőforrást a térbeli graph gyökérmappájában. Az IoT Hub-erőforrás lehetővé teszi, hogy minden eszköz alatt üzenetek küldéséhez a legfelső szintű helyet. Az IoT Hub létrehozása után regisztrálhatják az eszközeiket az érzékelők, a digitális Twins példány belül. Az eszközök révén adatokat küldhet egy digitális Twins szolgáltatásokhoz a [Azure IoT eszközoldali SDK-t](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks).

Egy lépésenkénti útmutató az ahhoz, hogy az eszközök regisztrálására, tekintse meg a [Oktatóanyaggal, telepítheti és konfigurálhatja a digitális Twins](tutorial-facilities-setup.md). Egy pillanat alatt a lépések a következők:

- Egy digitális Twins példány üzembe helyezése a [az Azure portal](https://portal.azure.com).
- Tárolóhelyek létrehozása a graph.
- Hozzon létre egy IoT Hub-erőforrást, és rendelje hozzá a gráfban található szóközzel.
- Eszközök és érzékelők létrehozása a graph, és hozzárendelheti azokat a tárolóhelyek az előző lépésekben létrehozott.
- Hozzon létre egy megfeleltetőben megadott feltételek alapján telemetriai üzeneteket szűréséhez.
- Hozzon létre egy [felhasználó által definiált függvény](concepts-user-defined-functions.md), és rendelje hozzá a grafikon a telemetriai üzeneteket egyéni feldolgozásához szóközzel.
- Rendelje hozzá a szerepkört, hogy a felhasználó által definiált függvény a grafikon adatainak eléréséhez.
- Az IoT Hub eszköz kapcsolati karakterláncának lekérése a digitális Twins felügyeleti API-kon keresztül.
- Az eszköz kapcsolati karakterláncának konfigurálása az Azure IoT eszközoldali SDK-t az eszközön.

A következő szakaszokban megismerheti az IoT Hub eszköz kapcsolati karakterláncának beszerzése a digitális Twins felügyeleti API-ból. Azt is megtudhatja, hogyan érzékelő-alapú telemetriát küldjön az IoT Hub telemetriai üzenetformátum használatával. Digitális Twins telemetriát, amely a térbeli diagramon belül érzékelő társítani kell minden egyes adatra van szükség. Ez a követelmény biztosítja, hogy az adatok feldolgozását és irányítja a megfelelő térbeli környezetében.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Az IoT Hub eszköz kapcsolati karakterláncának lekérése a felügyeleti API-ból

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Tegye meg az eszköz API-t egy GET hívást egy `includes=ConnectionString` paramétert az IoT Hub eszköz kapcsolati karakterláncának beolvasása. Az eszköz szűrheti a GUID Azonosítóját vagy a Hardverazonosító, az adott eszköz.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_DEVICE_GUID* | Az eszköz azonosítója |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?hardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Paraméter értéke | Csere erre |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | Az eszköz hardveres azonosítója |

A válasz hasznos adatban, másolja ki az eszköz **connectionString** tulajdonság. Akkor használja az Azure IoT eszközoldali SDK-val adatokat küldeni a digitális Twins hívásakor.

## <a name="device-to-cloud-message"></a>Eszköz – felhő üzenetek

Az eszköz üzenet formátuma és a hasznos adat a megoldás igényei szerint személyre is szabhatja. Használja bármely adategyezményben, egy bájttömböt vagy által támogatott stream lehet szerializálni a [Azure IoT Device Client Message osztály, üzenet (byte [] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Az üzenet lehet egyéni bináris formátum a választott mindaddig, amíg a megfelelő felhasználó által definiált függvények adategyezményben vissza. Nincs eszköz a felhőbe irányuló üzenetet csak egy követelmény. Annak biztosításához, hogy a rendszer megfelelően irányítja az üzenetet a motor tulajdonságait kell fenntartani.

### <a name="telemetry-properties"></a>Telemetriai adatok tulajdonságai

 A hasznos adat tartalmát egy **üzenet** tetszőleges adatokat lehet akár 256 KB-nál. Néhány követelmények tulajdonságait a várt a [ `Message.Properties` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) típusa. A táblázat tartalmazza a szükséges és választható tulajdonságait a rendszer által támogatott.

| Tulajdonság neve | Érték | Szükséges | Leírás |
|---|---|---|---|
| **DigitalTwins-Telemetria** | 1.0 | Igen | Állandó érték, amely azonosítja a rendszer üzenetet. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Igen | Az érzékelők által küldött egyedi azonosítója a **üzenet**. Ezt az értéket meg kell egyeznie egy objektum **HardwareId** tulajdonság esetében a rendszer feldolgozza azt. Például: `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Nem | Egy [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formátumú dátum karakterlánc, amely azonosítja a mintavétel ideje a tartalom. Például: `2018-09-20T07:35:00.8587882-07:00`. |
| **korrelációs azonosító** | `string` | Nem | Nyomkövetési eseményeket a rendszer között használta UUID azonosítója. Például: `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Az üzenet küldéséhez digitális Twins

Használja a DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) vagy [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) hívása digitális Twins elküldeni az üzenetet.

## <a name="next-steps"></a>További lépések

Az Azure digitális Twins adatfeldolgozás és a felhasználó által definiált függvények képességekkel kapcsolatos további információkért olvassa el a [Azure digitális Twins adatfeldolgozás és a felhasználó által definiált függvények](concepts-user-defined-functions.md).

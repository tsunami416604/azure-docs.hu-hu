---
title: Az eszközök csatlakoztatása és a telemetria bejövő állapota – Azure digitális Twins | Microsoft Docs
description: Az eszközök Azure digitális Twins-beli üzembe helyezésének áttekintése
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 6c61bc6075b3f0713dd790f1b3aa1a47af9d8e6c
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71950019"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Eszközkapcsolatok és bemenő telemetriaadatok

Az eszközök és érzékelők által továbbított telemetria-adatmennyiség bármely IoT-megoldás gerincét képezi. Ezeknek a különböző erőforrásoknak a ábrázolása és a hely kontextusában való kezelése a IoT alkalmazás-fejlesztés fő problémái. Az Azure Digital Twins a IoT-megoldások fejlesztésének folyamatát az eszközök és érzékelők egy térbeli intelligencia gráftal való egyesítésével egyszerűsíti.

Első lépésként hozzon létre egy Azure IoT Hub-erőforrást a térbeli gráf gyökerében. A IoT Hub erőforrás lehetővé teszi az összes eszköz számára a legfelső szintű terület elérését az üzenetek küldéséhez. A IoT Hub létrehozása után regisztrálja az eszközöket a digitális Twins-példányon belüli érzékelőkkel. Az eszközök az [Azure IoT ESZKÖZOLDALI SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)-n keresztül küldhetnek az adatfájlokat egy digitális Twins szolgáltatásba.

Az eszközök előkészítésének lépésenkénti útmutatója a [digitális ikrek üzembe helyezését és konfigurálását ismertető oktatóanyagban](tutorial-facilities-setup.md)található. Egy pillantással a lépések a következők:

- Helyezzen üzembe egy digitális Twins-példányt a [Azure Portal](https://portal.azure.com).
- Hozzon létre szóközt a gráfban.
- Hozzon létre egy IoT Hub erőforrást, és rendelje hozzá a gráf egyik területéhez.
- Hozzon létre eszközöket és érzékelőket a gráfban, és rendelje hozzá az előző lépésekben létrehozott szóközökhez.
- Hozzon létre egy Matcher a telemetria üzenetek szűréséhez feltételek alapján.
- Hozzon létre egy [felhasználó által definiált függvényt](concepts-user-defined-functions.md), és rendelje hozzá a gráf egy szóközzel a telemetria-üzenetek egyéni feldolgozásához.
- Rendeljen hozzá egy szerepkört, amely lehetővé teszi a felhasználó által definiált függvény számára a Graph-adat elérését.
- Szerezze be a IoT Hub eszköz csatlakoztatási karakterláncát a digitális Twins felügyeleti API-kkal.
- Konfigurálja az eszközön a csatlakoztatási karakterláncot az Azure IoT Device SDK-val.

A következő részekben megtudhatja, hogyan kérheti le a IoT Hub eszköz kapcsolódási karakterláncát a digitális Twins felügyeleti API-ból. Azt is megtudhatja, hogyan használhatja a IoT Hub telemetria üzenet formátumát érzékelő alapú telemetria küldéséhez. A digitális Twins minden olyan telemetria igényel, amelyet a rendszer a térbeli gráfon belüli érzékelőkhöz társít. Ez a követelmény biztosítja, hogy az adatfeldolgozás és a megfelelő térbeli környezeten belül legyen átirányítva.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>A IoT Hub-eszköz csatlakoztatási karakterláncának beolvasása a felügyeleti API-ból

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Az eszköz API-ját egy `includes=ConnectionString` paraméterrel megadhatja az IoT Hub eszköz kapcsolódási karakterláncának beolvasásához. Az eszköz GUID azonosítóját vagy a hardver AZONOSÍTÓját szűrheti az adott eszköz kereséséhez.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Paraméter | Csere erre |
| --- | --- |
| *YOUR_DEVICE_GUID* | Az eszköz azonosítója |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Paraméter értéke | Csere erre |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | Az eszköz hardver-azonosítója |

A válasz adattartalomban másolja az eszköz **ConnectionString** tulajdonságát. Akkor használja, ha az Azure IoT Device SDK-t hívja meg az adatküldés digitális ikreknek.

## <a name="device-to-cloud-message"></a>Eszközről a felhőbe irányuló üzenet

Testre szabhatja az eszköz üzenetének formátumát és a hasznos adatokat, hogy illeszkedjen a megoldás igényeihez. Minden olyan adategyezményt használhat, amely az Azure IoT-eszköz ügyféloldali üzenet osztálya által támogatott byte-tömbbe vagy streambe szerializálható [, üzenet (bájt [] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Az üzenet tetszőleges egyéni bináris formátum lehet, ha az adategyezményt egy megfelelő, felhasználó által definiált függvényben dekódolja. Egy eszközről a felhőbe irányuló üzenethez csak egy követelmény szükséges. Megtarthatja a tulajdonságok készletét annak biztosításához, hogy az üzenet megfelelően legyen irányítva a feldolgozó motorhoz.

### <a name="telemetry-properties"></a>Telemetria tulajdonságai

 Az **üzenetek** hasznos adatai akár 256 KB-os méretig is lehetnek tetszőleges adattartalom. A [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) típusú tulajdonságok esetében néhány követelménynek kell szerepelnie. A táblázat a rendszer által támogatott kötelező és választható tulajdonságokat jeleníti meg.

| Tulajdonság neve | Value | Szükséges | Leírás |
|---|---|---|---|
| **DigitalTwins-Telemetry** | 1.0 | Igen | Egy állandó érték, amely az üzenetet azonosítja a rendszernek. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Igen | Az **üzenetet**küldő érzékelő egyedi azonosítója. Ennek az értéknek meg kell egyeznie egy objektum **HardwareId** tulajdonságával, hogy a rendszer feldolgozza azt. Például: `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Nem | [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) formázott dátum sztring, amely azonosítja a hasznos adatok mintavételi idejét. Például: `2018-09-20T07:35:00.8587882-07:00`. |
| **CorrelationId** | `string` | Nem | Egy UUID, amely a rendszer eseményeinek nyomon követésére szolgál. Például: `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Üzenet küldése a digitális ikreknek

A DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) vagy a [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) hívásával küldje el az üzenetet a digitális ikreknek.

## <a name="next-steps"></a>További lépések

- Az Azure Digital Twins adatfeldolgozási és a felhasználó által definiált függvények funkcióinak megismeréséhez olvassa el az [Azure Digital Twins adatfeldolgozási és felhasználó által definiált függvények](concepts-user-defined-functions.md)című témakört.

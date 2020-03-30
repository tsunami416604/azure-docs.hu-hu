---
title: Eszközkapcsolat és telemetriai adatok – Azure Digital Twins | Microsoft dokumentumok
description: Megtudhatja, hogyan csatlakozhat, alaplapra, és küldhet telemetriát egy IoT-eszközről az Azure Digital Twins alkalmazásban.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5c2c519ece9806b92c3e455d5f550bc2abfc9f3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862475"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Eszközkapcsolatok és bemenő telemetriaadatok

Az eszközök és érzékelők által küldött telemetriai adatok képezik bármely IoT-megoldás gerincét. Hogyan lehet ezeket a különböző erőforrásokat képviselni és kezelni egy adott hely környezetében az IoT-alkalmazások fejlesztésének fő szempontja. Az Azure Digital Twins leegyszerűsíti az IoT-megoldások fejlesztésének folyamatát az eszközök és érzékelők térbeli intelligenciagrafikonnal való egyesítésével.

Első lépésekhez hozzon létre egy Azure IoT Hub-erőforrást a térbeli gráf gyökerében. Az IoT Hub-erőforrás lehetővé teszi, hogy a gyökérterület alatt lévő összes eszköz üzeneteket küldjön. Az IoT Hub létrehozása után regisztrálja az eszközöket a digitális twins-példányon belüli érzékelőkkel. Az eszközök adatokat küldhetnek egy Digital Twins szolgáltatásnak az [Azure IoT-eszköz SDK-n](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks)keresztül.

Az eszközök fedélzetre vidalmával kapcsolatos részletes útmutatóért olvassa el a [Digitális twins telepítéséhez és konfigurálásához futó oktatóanyagcímű dokumentumát.](tutorial-facilities-setup.md) Egy pillantással, a lépések a következők:

- Digitális Twins-példány üzembe helyezése az [Azure Portalon.](https://portal.azure.com)
- Hozzon létre szóközöket a grafikonon.
- Hozzon létre egy IoT Hub-erőforrást, és rendelje hozzá a diagram egy területéhez.
- Hozzon létre eszközöket és érzékelőket a grafikonon, és rendelje hozzá őket az előző lépésekben létrehozott terekhez.
- Hozzon létre egy matcher a telemetriai üzenetek szűréséhez feltételek alapján.
- Hozzon létre egy [felhasználó által definiált függvényt,](concepts-user-defined-functions.md)és rendelje hozzá a diagram egy területhez a telemetriai üzenetek egyéni feldolgozásához.
- Rendeljen hozzá egy szerepkört, amely lehetővé teszi a felhasználó által definiált függvény számára a diagramadatok elérését.
- Az IoT Hub-eszköz kapcsolati karakterlánca a digitális twins felügyeleti API-k.
- Konfigurálja az eszköz kapcsolati karakterláncot az eszközön az Azure IoT-eszköz SDK-val.

A következő szakaszokban megtudhatja, hogyan szerezheti be az IoT Hub-eszköz kapcsolati karakterláncát a Digital Twins Management API-ból. Azt is megtudhatja, hogyan használhatja az IoT Hub telemetriai üzenetformátumot érzékelőalapú telemetriai adatok küldéséhez. A digitális twins minden egyes telemetriai elem, amely et kap, hogy a térbeli diagramon belül egy érzékelő társítva kell lennie. Ez a követelmény biztosítja, hogy az adatok feldolgozása és irányított a megfelelő térbeli környezetben.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Az IoT Hub-eszköz kapcsolati karakterláncának beszereznie a Felügyeleti API-ból

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Get-hívás az eszköz API-n egy `includes=ConnectionString` paramétert az IoT Hub-eszköz kapcsolati karakterláncának lekérni. Szűrje az eszköz GUID azonosítója vagy a hardverazonosító szerint az adott eszköz megkereséséhez.

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
| *YOUR_DEVICE_HARDWARE_ID* | Az eszköz hardverazonosítója |

A válasz hasznos adatában másolja az eszköz **connectionString** tulajdonságát. Használja, amikor felhívja az Azure IoT-eszköz SDK adatok küldése a digitális twins.

## <a name="device-to-cloud-message"></a>Eszközről felhőbe irányuló üzenet

Testre szabhatja az eszköz üzenetformátumát és hasznos adatát, hogy megfeleljen a megoldás igényeinek. Használjon olyan adategyezményt, amely szerializálható egy bájttömbbe vagy streambe, amelyet az [Azure IoT Device Client Message osztály, a Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___)támogat. Az üzenet lehet egy egyéni bináris formátum, amelyet ön választ, feltéve, hogy az adategyezményt egy megfelelő felhasználó által definiált függvényben dekódolja. Csak egy követelmény van egy eszközről a felhőbe irányuló üzenethez. Tulajdonságkészlet karbantartása annak érdekében, hogy az üzenet megfelelően legyen a feldolgozómotorhoz irányítva.

### <a name="telemetry-properties"></a>Telemetriai tulajdonságok

 **Az üzenet** hasznos tartalma tetszőleges, legfeljebb 256 KB méretű adat lehet. A [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) típus tulajdonságaihoz néhány követelmény várható. A táblázat a rendszer által támogatott szükséges és választható tulajdonságokat mutatja.

| Tulajdonság neve | Érték | Kötelező | Leírás |
|---|---|---|---|
| **DigitalTwins-telemetria** | 1.0 | Igen | Állandó érték, amely a rendszernek küldött üzenetet azonosítja. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Igen | Az üzenetet küldő érzékelő egyedi **azonosítója.** Ennek az értéknek meg kell egyeznie egy objektum **HardwareId** tulajdonságával ahhoz, hogy a rendszer feldolgozható legyen. Például: `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Nem | Egy [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) formátumú dátumkarakterlánc, amely azonosítja a hasznos adat mintavételi idejét. Például: `2018-09-20T07:35:00.8587882-07:00`. |
| **Korrelációs azonosító** | `string` | Nem | Egy UUID, amely az események nyomon követésére szolgál a rendszeren keresztül. Például: `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Küldje el üzenetét a digitális ikreknek

A DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) vagy [sendeventbatchasync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) hívás használatával küldje el az üzenetet a Digital Twins.

## <a name="next-steps"></a>További lépések

- Ha többet szeretne megtudni az Azure Digital Twins adatfeldolgozásról és a felhasználó által definiált függvények képességeiről, olvassa el az [Azure Digital Twins adatfeldolgozást és a felhasználó által definiált függvényeket.](concepts-user-defined-functions.md)

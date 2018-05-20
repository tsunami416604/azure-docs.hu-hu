---
title: Az Azure IoT-központ eszközkapcsolatok |} Microsoft Docs
description: Ez a cikk az Azure IoT-központ eszközkapcsolatok kapcsolatos főbb fogalmakat be
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 00b621a4635ef1ceda26772ac5876fa2599b56f8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="device-connectivity-in-azure-iot-central"></a>Az Azure IoT-központ eszközkapcsolatok

Ez a cikk a Microsoft Azure IoT központi eszközkapcsolatok kapcsolatos főbb fogalmakat be.

Minden eszköz, amely összeköti az Azure IoT központi alkalmazáshoz kapcsolódik a [végpontok](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints) , amelyek ki vannak téve az IoT hubból Azure IoT központi használja. Az IoT-központ lehetővé teszi, hogy a csatlakoztatott eszközökből méretezhető, biztonságos és megbízható kapcsolatok.

## <a name="sdk-support"></a>SDK-támogatás

Az Ön Azure eszközoldali SDK-k ajánlat legegyszerűbb módja az eszközök, amely kapcsolódik az Azure IoT központi alkalmazás valósítja meg a kódot. A következő eszköz SDK-k érhetők el:

- [C-hez készült Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-c)
- [Python-hez készült Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-python)
- [A Node.js az Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-node)
- [Javához készült Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-java)
- [.NET-keretrendszerhez készült Azure IoT-SDK](https://github.com/azure/azure-iot-sdk-csharp)

Minden eszköz csatlakozik, egy egyedi kapcsolati karakterlánc, amely azonosítja az eszköz használatával. Egy eszköz csak csatlakozhat az IoT hub, ahol regisztrálták. Az Azure IoT központi alkalmazás létrehozásakor egy valódi eszköz az alkalmazás használata a kapcsolati karakterlánc állít elő.

## <a name="sdk-features-and-iot-hub-connectivity"></a>SDK-funkciók és az IoT Hub csatlakozási

Az IoT-központ minden eszköz kommunikációs használja a következő IoT-központ kapcsolati lehetőségek:

- [Üzenetküldési eszközről a felhőbe](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Eszköz twins](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

A következő táblázat összefoglalja, hogyan Azure IoT központi eszközök szolgáltatásainak leképezése továbbítása az IoT-központ szolgáltatások:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Mérési: telemetriai adat | Üzenetküldési eszközről a felhőbe |
| Eszköztulajdonságok | A két eszköz jelentett tulajdonságai |
| Beállítások | Eszköz iker szükséges, és jelentett tulajdonságai |

Az eszköz SDK-k használatával kapcsolatos további tudnivalókért lásd: egyet az alábbi cikkekben például kód:

- [Általános Node.js ügyfél csatlakoztatása az Azure IoT központi alkalmazás](howto-connect-nodejs.md)
- [A Pi málna eszköz csatlakoztatása az Azure IoT központi alkalmazás](howto-connect-raspberry-pi-python.md)
- [Egy DevDiv kit eszköz csatlakoztatása az Azure IoT központi alkalmazás](howto-connect-devkit.md).

A következő videó áttekintést nyújt az Azure IoT központi csatlakozás valós eszközt:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>Protokollok

Az eszköz SDK-k a következő hálózati protokollok támogatása az IoT-központ csatlakozik:

- MQTT
- AMQP
- HTTPS

A különbség a protokollok és bővebben egy kiválasztásáról, lásd: [válasszon olyan kommunikációs protokollt](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Ha az eszköz nem tudja használni a támogatott protokollok, használhatja a Azure IoT peremhálózati protokollnak az átalakításhoz. IoT peremhálózati szituációkat más üzletiintelligencia-az-a-edge feldolgozási kiszervezéséhez peremén az Azure IoT központi alkalmazásból.

## <a name="security"></a>Biztonság

Eszközök és az Azure IoT központi között továbbított összes adat titkosítva van. Az IoT-központ olyan eszköz, amely az eszköz számára is elérhető IoT-központok végpontjai egyik származó kérelmek hitelesíti. Egy eszköz elkerülése érdekében a hitelesítő adatok cseréjét a hálózaton keresztül, az aláírt jogkivonatokat segítségével hitelesíti. További információkért tekintse meg, [IoT-központ való hozzáférés szabályozása](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> Jelenleg eszközöket, amelyek kapcsolódnak az Azure IoT központi SAS-tokenje kell használnia. X.509-tanúsítványokat az Azure IoT központi csatlakozás eszközök esetében nem támogatottak.

## <a name="next-steps"></a>További lépések

Most, hogy az eszköz kapcsolatot az Azure IoT-központ megismerte az alábbiakban a javasolt lépéseket:

- [Készítse elő, és egy DevKit eszköz csatlakoztatása](howto-connect-devkit.md)
- [Készítse elő, és csatlakozzon a málna Pi](howto-connect-raspberry-pi-python.md)
- [Általános Node.js ügyfél csatlakoztatása az Azure IoT központi alkalmazás](howto-connect-nodejs.md)

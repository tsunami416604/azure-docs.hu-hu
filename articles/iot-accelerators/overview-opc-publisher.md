---
title: Mi az az OPC-közzétevő – Azure |} A Microsoft Docs
description: Az OPC-közzétevő áttekintése
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 99b3f6b4495ae5981322c90fce251b9b0a18bb5d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450911"
---
# <a name="what-is-opc-publisher"></a>Mi az az OPC-közzétevő?

Az OPC-közzétevő egy referenciaimplementációt, amely bemutatja, hogyan lehet:

- Csatlakozás meglévő OPC UA-kiszolgálók.
- Tegye közzé az OPC UA-kiszolgálók küldött telemetriai adatok JSON-kódolású OPC UA Pub/Sub formátumban, egy JSON-adattartalom, az Azure IoT Hub használatával.

Az átviteli protokollokat, amely támogatja az Azure IoT Hub ügyfél SDK-t használhatja: HTTPS, az AMQP és mqtt-ről.

A referenciaimplementáció tartalmazza:

- Az OPC UA *ügyfél* meglévő OPC UA-kiszolgálók a hálózaton való kapcsolódáshoz.
- Az OPC UA *kiszolgáló* porton, amelyek segítségével kezelheti a mi közzé van téve, és az IoT Hub kínál 62222 közvetlen módszerek használatával Ugyanezt megteheti.

Letöltheti a [OPC-közzétevő referenciamegvalósítása](https://github.com/Azure/iot-edge-opc-publisher) a Githubról.

Az alkalmazás a .NET Core-technológia segítségével van megvalósítva, és a .NET Core által támogatott bármilyen platformon futtatható.

Az OPC-közzétevő újrapróbálkozási logika, amely nem bizonyos számú életben tartási kérelem megválaszolásához-végpontokra irányuló kapcsolatokat implementál. Ha például az OPC UA-kiszolgáló nem válaszol, áramkimaradás miatt.

Az egyes különböző közzétételi intervallum OPC UA-kiszolgáló, az alkalmazás létrehoz egy különálló előfizetést, amelyen mind a csomóponton a közzétételi intervallumát frissülnek.

Az OPC-közzétevő támogatja a hálózati terhelés csökkentése érdekében az IoT hubnak küldött adatok kötegelés. A kötegelés csomagot küld az IoT Hub, csak akkor, ha a beállított csomag méretet.

Ez az alkalmazás NuGet-csomagok az OPC Foundation OPC UA hálózatireferencia-veremben használja. Lásd: [ https://opcfoundation.org/license/redistributables/1.3/ ](https://opcfoundation.org/license/redistributables/1.3/) a licencfeltételeket.

### <a name="next-steps"></a>További lépések

Most már megtanulta, mi az az OPC-közzétevő,-e a javasolt következő lépésre megtudhatja, hogyan [konfigurálása az OPC-közzétevő](howto-opc-publisher-configure.md).

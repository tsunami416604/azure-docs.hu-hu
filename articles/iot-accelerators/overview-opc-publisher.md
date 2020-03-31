---
title: Mi az OPC Kiadó - Azure | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az OPC Publisher szolgáltatásairól. Lehetővé teszi a kódolt JSON-telemetriai adatok közzétételét egy JSON-tartalom használatával az Azure IoT Hubon.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2310a81d3cfaeff203134af9968bc2d5caea3e9c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73824776"
---
# <a name="what-is-opc-publisher"></a>Mi az OPC Publisher?

Az OPC Publisher egy referenciaimplementáció, amely bemutatja, hogyan:

- Csatlakozás meglévő OPC UA-kiszolgálókhoz.
- JSON-kódolású telemetriai adatok közzététele az OPC UA-kiszolgálókról OPC UA Pub/Sub formátumban, JSON-tartalom használatával az Azure IoT Hubba.

Az Azure IoT Hub-ügyfél SDK által támogatott átviteli protokollok bármelyikét használhatja: HTTPS, AMQP és MQTT.

A referencia-megvalósítás a következőket tartalmazza:

- OpC *UA-ügyfél* a hálózaton lévő opc ua-kiszolgálókhoz való csatlakozáshoz.
- Az OPC *UA-kiszolgáló* a 62222-es porton, amely segítségével kezelheti a közzétett és az IoT Hub közvetlen metódusok ugyanezt.

Az [OPC Publisher referenciaimplementációját](https://github.com/Azure/iot-edge-opc-publisher) a GitHubról töltheti le.

Az alkalmazás a .NET Core technológiával van megvalósítva, és a .NET Core által támogatott bármely platformon futtatható.

Az OPC Publisher újrapróbálkozási logikát valósít meg olyan végpontok kapcsolatok létrehozásához, amelyek nem válaszolnak egy bizonyos számú életben tartó kérésre. Ha például egy OPC UA-kiszolgáló áramkimaradás miatt nem válaszol.

Az opc ua-kiszolgálóra irányuló minden egyes külön közzétételi időközhöz az alkalmazás külön előfizetést hoz létre, amelyen keresztül az ezzel a közzétételi időközzel rendelkező összes csomópont frissül.

Az OPC Publisher támogatja az IoT Hubnak küldött adatok kötegelését a hálózati terhelés csökkentése érdekében. Ez a kötegelés csak akkor küld csomagot az IoT Hubnak, ha elérte a beállított csomagméretet.

Ez az alkalmazás az OPC Foundation OPC UA referenciaverem, mint NuGet csomagok. Tekintse [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) meg a licencelési feltételeket.

### <a name="next-steps"></a>További lépések

Most, hogy megtanulta, mi az OPC Publisher, a javasolt következő lépés az [OPC Publisher konfigurálásának ismertetése.](howto-opc-publisher-configure.md)

---
title: Mi az az OPC-közzétevő – Azure | Microsoft Docs
description: Ez a cikk az OPC-közzétevő funkcióinak áttekintését tartalmazza. Lehetővé teszi kódolt JSON-telemetria adatok közzétételét JSON-adattartalommal az Azure IoT Hubba.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2310a81d3cfaeff203134af9968bc2d5caea3e9c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824776"
---
# <a name="what-is-opc-publisher"></a>Mi az OPC-közzétevő?

Az OPC-közzétevő egy olyan hivatkozás, amely a következőket mutatja be:

- Kapcsolódjon meglévő OPC UA-kiszolgálókhoz.
- Az OPC UA-kiszolgálókról származó, a JSON-ban kódolt telemetria adatok közzététele a JSON-adattartalom használatával, az Azure IoT Hub.

Az Azure IoT Hub Client SDK által támogatott átviteli protokollok bármelyike használható: HTTPS, AMQP és MQTT.

A hivatkozás implementációja az alábbiakat tartalmazza:

- OPC UA- *ügyfél* a hálózaton lévő meglévő OPC ua-kiszolgálókhoz való csatlakozáshoz.
- Egy OPC UA- *kiszolgáló* a 62222-as porton, amely a közzétett és IoT hub közvetlen metódusok használatával felügyelhető.

Az [OPC-közzétevői referenciát](https://github.com/Azure/iot-edge-opc-publisher) letöltheti a githubról.

Az alkalmazás a .NET Core Technology használatával valósul meg, és a .NET Core által támogatott bármely platformon futtatható.

Az OPC-közzétevő az újrapróbálkozási logikát implementálja olyan végpontok kapcsolatainak létrehozásához, amelyek nem válaszolnak bizonyos számú életben tartási kérelemre. Ha például egy OPC UA-kiszolgáló áramszünet miatt nem válaszol.

Az alkalmazás minden egyes különböző közzétételi intervallum esetében egy OPC UA-kiszolgálóhoz hoz létre egy külön előfizetést, amely felett az adott közzétételi időközt tartalmazó összes csomópont frissül.

Az OPC-közzétevő támogatja a hálózati terhelés csökkentése érdekében IoT Hub elküldett adatfeldolgozást. Ez a kötegelt feldolgozás csak akkor küld IoT Hub csomagot, ha elérte a beállított csomagméret méretét.

Ez az alkalmazás az OPC Foundation OPC UA Reference stacket használja NuGet-csomagként. Lásd: [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) a licencelési feltételekhez.

### <a name="next-steps"></a>További lépések

Most, hogy megismerte az OPC-közzétevőt, a javasolt következő lépés az [OPC-közzétevő konfigurálásának](howto-opc-publisher-configure.md)ismertetése.

---
title: Az Azure IoT Hub kommunikációs protokollok és portok |} Microsoft Docs
description: Fejlesztői útmutató - ismerteti a támogatott kommunikációs protokollok a eszközről a felhőbe és a felhő-eszköz- és a portszámok, amelyek nyitva kell lennie.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 0fe3dd719877dac23410ff1ca00d559636a5ed60
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633002"
---
# <a name="reference---choose-a-communication-protocol"></a>Olyan kommunikációs protokollt hivatkozhat - a

Az IoT-központ lehetővé teszi, hogy az eszköz a következő protokollok eszközoldali kommunikációra használja:

* [MQTT][lnk-mqtt]
* MQTT WebSocketen keresztül
* [AMQP][lnk-amqp]
* AMQP WebSocketen keresztül
* HTTPS

Ezeket a protokollokat hogyan támogatják a különböző funkciók IoT-központ kapcsolatos információkért lásd: [eszközről a felhőbe kommunikációs útmutatást] [ lnk-d2c-guidance] és [felhő eszközre kommunikációs útmutatást] [lnk-c2d-guidance].

A következő táblázat a magas szintű javaslatok a kiválasztott protokoll:

| Protokoll | Ha ez a protokoll válasszon |
| --- | --- |
| MQTT <br> MQTT WebSocket keresztül |Használjon minden eszközön, amelyekhez nem szükséges több eszközön (minden saját eszköz hitelesítő adatokkal rendelkező) azonos a TLS-kapcsolaton keresztül csatlakozni. |
| AMQP <br> AMQP WebSocket keresztül |A mezőt, és a felhő átjárók segítségével előnyeit multiplexáló eszközök közötti kapcsolat. |
| HTTPS |Használjon más protokollt nem támogató eszközökön. |

Ha úgy dönt, hogy a eszközoldali kommunikációs protokollja, vegye figyelembe a következő szempontokat:

* **Felhő eszközre mintát**. HTTPS nincs hatékonyan kiszolgáló leküldéses végrehajtásához. Ha HTTPS PROTOKOLLT használ, eszközök kérdezze le az IoT-központ az felhő-eszközre küldött üzenetek. Ez a módszer nem hatékony, az eszköz és az IoT-központ is. Az aktuális HTTPS irányelveit minden egyes eszköz kell kérdezze le az üzenetek 25 percenként vagy több. MQTT és AMQP kiszolgáló leküldéses támogatja a felhő-eszközre küldött üzenetek fogadásakor. Lehetővé teszik az azonnali leküldéses értesítések az üzenetek az IoT-központ az eszközre. Kézbesítési késés fontos, ha MQTT vagy AMQP a a legjobb protokollok használatára. A ritkán csatlakoztatott eszközök esetében a HTTPS is működik.
* **Átjárók mezőben**. MQTT és a HTTPS használata esetén nem lehet csatlakoztatni (minden saját eszköz hitelesítő adatokkal rendelkező) több eszközön ugyanazt a TLS-kapcsolatot használja. A [mezőben az átjáró alkalmazásának típusai] [ lnk-azure-gateway-guidance] , amely egy TLS kapcsolatot igényelnek a mező átjáró és az IoT-központ között az egyes csatlakoztatott eszközök esetén ezeket a protokollokat optimálisnál.
* **Kevés erőforrás eszközök**. A MQTT és a HTTPS-tárak egy kisebb kezdjen, mint az AMQP szalagtárak rendelkezik. Ha az eszköz csak korlátozott erőforrásokat (például kisebb, mint 1 MB RAM), ezeket a protokollokat lehet a kizárólag protokollmegvalósítás érhető el.
* **A hálózati átviteli**. A szabványos AMQP-protokoll által használt port: 5671, és MQTT 8883 portot figyeli. Ezek a portok használata problémákat okozhatnak hálózatokban, amelyek nem HTTPS protokollokhoz be van zárva. Keresztül használja a MQTT websocket elemeket, AMQP websocket elemeket, vagy a HTTPS ebben a forgatókönyvben.
* **Terhelés méretének**. MQTT és AMQP a bináris protokollok, következtében a HTTPS-nál több kompakt hasznos adat található.

> [!WARNING]
> HTTPS használata esetén minden egyes eszköz kell a lekérésének felhő-eszközre küldött üzenetek 25 percenként vagy több. Azonban során fejlesztési, a rendszer, és kérdezze le a gyakrabban 25 percenként elfogadható.

## <a name="port-numbers"></a>Portszámok

Eszközök képesek kommunikálni az Azure-ban a különböző protokollok IoT-központot. A választott protokoll jellemzően a megoldás a meghatározott követelmények vezérlik. A következő táblázat az eszközök egy adott protokollt használhatják nyitva kell lennie a kimenő portokat sorolja fel:

| Protokoll | Port |
| --- | --- |
| MQTT |8883 |
| MQTT WebSocketen keresztül |443 |
| AMQP |5671 |
| AMQP WebSocketen keresztül |443 |
| HTTPS |443 |

Miután létrehozta az IoT-központ Azure-régióban, az IoT hub megtartja ugyanazt a címet, hogy az IoT-központ teljes. Azonban ha a Microsoft szolgáltatás-minőségi fenntartásához különböző méretezési egység az IoT hub kerül, majd ezt a jogosultságot egy új IP-címet.


## <a name="next-steps"></a>További lépések

Hogyan IoT-központ megvalósítja a MQTT protokoll kapcsolatos további információkért lásd: [kommunikáljon az IoT hub MQTT protokollal a][lnk-mqtt-support].

[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-mqtt-support]: iot-hub-mqtt-support.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways

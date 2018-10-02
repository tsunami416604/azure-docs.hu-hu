---
title: Az Azure IoT Hub-kommunikációs protokollokat és portokat |} A Microsoft Docs
description: Fejlesztői útmutató – eszközről a felhőbe és a felhőből az eszközre irányuló kommunikáció és a portszámok, amelyeket meg kell nyitni a támogatott kommunikációs protokollok ismerteti.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 3079b2414c44fc97bc8aff4b207e0943e94c7457
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584336"
---
# <a name="reference---choose-a-communication-protocol"></a>Hivatkozás - kommunikációs protokoll kiválasztása

Az IoT Hub lehetővé teszi, hogy az eszközök a következő protokollokra eszközoldali kommunikációhoz:

* [MQTT-RŐL](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT WebSocketen keresztül
* [AZ AMQP](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP WebSocketen keresztül
* HTTPS

Ezeket a protokollokat hogyan támogatják a különböző funkciók az IoT Hub kapcsolatos információkért lásd: [eszközt a felhőbe irányuló kommunikáció útmutatóját](iot-hub-devguide-d2c-guidance.md) és [felhőből az eszközre irányuló kommunikáció útmutatóját](iot-hub-devguide-c2d-guidance.md).

A következő táblázat tartalmazza az Ön által választott magas szintű javaslatok protokoll:

| Protokoll | Ha ezt a protokollt kell kiválasztani |
| --- | --- |
| MQTT <br> MQTT websocketen |Használja a minden eszközön, amelyre nincs szükség több eszközön (mindegyik saját eszközönkénti hitelesítő adatait) az ugyanazon a TLS-kapcsolaton keresztül csatlakozni. |
| AMQP <br> Az AMQP websocketen |A mező és a felhő-átjárókon használatával előnyeit multiplexálási eszközök közötti kapcsolat. |
| HTTPS |Más protokollt nem támogató eszközökhöz használja. |

Ha úgy dönt, hogy az eszköz ügyféloldali kommunikációhoz protokollt, vegye figyelembe a következőket:

* **Felhőből az eszközre minta**. HTTPS nincs hatékonyan leküldés folyamatkiszolgálóról megvalósításához. Emiatt a HTTPS használja, amikor eszközök lekérdezi az IoT Hub a felhőből az eszközre irányuló üzenetek. Ez a módszer nem hatékony, az eszköz és az IoT Hub számára. A HTTPS aktuális irányelveit minden egyes eszköz kell lekérdezi a üzenetek minden 25 percig vagy tovább is. MQTT, AMQP és támogatja a kiszolgálói adatleküldés felhőből az eszközre irányuló üzenetek fogadására. Lehetővé teszik az üzenetek azonnali leküldések az eszköznek az IoT hubról. Kézbesítési késés problémát jelent, mqtt-ről vagy AMQP-e a legjobb protokollok használatára. A ritkán csatlakoztatott eszközök esetében a HTTPS is működik.

* **Átjárók mezőben**. Mqtt-ről és a HTTPS használata esetén nem lehet csatlakoztatni több eszközön (mindegyik saját eszközönkénti hitelesítő adatait) azonos a TLS-kapcsolattal. A [mezőt az átjáró alkalmazásának típusai](iot-hub-devguide-endpoints.md#field-gateways) egy TLS-kapcsolatot a helyszíni átjáró és az IoT Hub között, amely esetében minden csatlakoztatott eszközhöz, ezeket a protokollokat optimálisnál.

* **Kevés erőforrás eszközök**. Az MQTT- és HTTPS-kódtárak rendelkezik egy kisebb, mint az AMQP-tárak üzembe helyezésének előkészítése. Mint ilyen Ha az eszköz csak korlátozott erőforrások (például kisebb, mint 1 MB-os RAM), ezeket a protokollokat lehet az egyetlen elérhető protokollmegvalósítás.

* **Hálózati bejárási**. A szabványos AMQP protokollt használja az 5671-es porton, és az MQTT 8883 portot figyeli. Ezek a portok használata problémákat okozhat a hálózatokban, amelyek nem-HTTPS protokollhoz nincs megnyitva. Használja MQTT-en keresztül a websockets protokoll, AMQP a websockets protokoll és a HTTPS ebben a forgatókönyvben.

* **Adattartalom-méretkorlát**. MQTT, AMQP és a bináris protokollok, ami tömörebb hasznos adat található, mint a HTTPS.

> [!WARNING]
> HTTPS használata esetén minden eszköz kell elindítja a lekérdezést a felhőből az eszközre irányuló üzenetek minden 25 percig vagy tovább is. Azonban során fejlesztési feladatokhoz, a rendszer minden 25 percnél gyakrabban lekérdezésére elfogadható.

## <a name="port-numbers"></a>Portszámok

Eszközök IoT Hub, az Azure-ban különböző protokollok használatával kommunikálhat. Általában a kiválasztott protokoll határozzák meg a megoldás egyéni követelményei. Az alábbi táblázat az eszközöket, hogy egy adott protokollt használhatják nyitva kell lennie kimenő portokat sorolja fel:

| Protokoll | Port |
| --- | --- |
| MQTT |8883 |
| MQTT WebSocketen keresztül |443 |
| AMQP |5671 |
| AMQP WebSocketen keresztül |443 |
| HTTPS |443 |

Azure-régióban létrehozott egy IoT hubot, ha az IoT hub biztosítja, hogy ugyanazon IP-címét, hogy az IoT hub élettartama. Azonban ha a Microsoft IoT hub szolgáltatás-minőségi fenntartásához különböző méretezési egység, majd hozzárendelték egy új IP-címet.

## <a name="next-steps"></a>További lépések

Hogyan valósítja meg a IoT Hub az MQTT protokoll kapcsolatos további információkért lásd: [kommunikáljon az IoT hubbal, az MQTT protokoll használatával](iot-hub-mqtt-support.md).

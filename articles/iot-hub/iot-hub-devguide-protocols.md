---
title: Azure IoT Hub kommunikációs protokolljai és portjai | Microsoft dokumentumok
description: Fejlesztői útmutató – ismerteti az eszközről a felhőbe és a felhőbe irányuló kommunikáció támogatott kommunikációs protokolljait, valamint a megnyitott portszámokat.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 3e7f31371a0582a6f4941efbfa0087119278d2d1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729118"
---
# <a name="reference---choose-a-communication-protocol"></a>Hivatkozás - kommunikációs protokoll kiválasztása

Az IoT Hub az alábbi protokollok használatát teszi lehetővé az eszközoldali kommunikációhoz:

* [MQTT](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf)
* MQTT WebSocketen keresztül
* [AMQP](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
* AMQP WebSocketen keresztül
* HTTPS

Arról, hogy ezek a protokollok hogyan támogatják az IoT Hub adott funkcióit, az [Eszközről a felhőbe irányuló kommunikációról](iot-hub-devguide-d2c-guidance.md) szóló útmutatásban és [a felhőből az eszközre irányuló kommunikációra vonatkozó útmutatásban](iot-hub-devguide-c2d-guidance.md)talál tájékoztatást.

Az alábbi táblázat a választott protokollra vonatkozó magas szintű javaslatokat tartalmazza:

| Protocol (Protokoll) | Mikor kell kiválasztania ezt a protokollt |
| --- | --- |
| MQTT <br> MQTT a WebSocket felett |Minden olyan eszközön használható, amelynem igényel több eszközt (mindegyik saját eszközönkénti hitelesítő adatokkal) ugyanazon a TLS-kapcsolaton keresztül. |
| AMQP <br> AMQP a WebSocket felett |Helyszíni és felhőalapú átjárókon is használhatja a különböző eszközök közötti kapcsolatmultiplexing előnyeit. |
| HTTPS |Olyan eszközökhöz használható, amelyek más protokollokat nem támogatnak. |

Ha az eszközoldali kommunikációprotokollt választja, vegye figyelembe a következő pontokat:

* **Felhőből az eszközre minta.** A HTTPS nem rendelkezik hatékony módszerrel a kiszolgálóleküldéses megvalósításához. Mint ilyen, ha HTTPS-t használ, az eszközök lekérik az IoT Hubot az eszközről az eszközre irányuló üzenetekhez. Ez a megközelítés nem hatékony mind az eszköz, mind az IoT Hub számára. A jelenlegi HTTPS-irányelvek szerint minden eszköznek 25 percenként vagy annál több percenként kell lekérnie az üzeneteket. Az MQTT és az AMQP támogatási kiszolgáló leküldéses felhőből az eszközre irányuló üzenetek fogadásakor. Lehetővé teszik az Üzenetek azonnali leküldéseit az IoT Hubról az eszközre. Ha a kézbesítési késés aggodalomra ad okot, mqtt vagy AMQP a legjobb protokollok használata. A ritkán csatlakoztatott eszközök esetében a HTTPS is működik.

* **Helyszíni átjárók**. Az MQTT és a HTTPS tls-kapcsolatonként csak egyetlen eszközidentitást (eszközazonosítót és hitelesítő adatokat) támogat. Emiatt ezek a protokollok nem támogatottak [a Field gateway forgatókönyvek,](iot-hub-devguide-endpoints.md#field-gateways) amelyek több eszközidentitást használó multiplexüzeneteket igényelnek az IoT Hub hoz egy vagy egy upstream kapcsolatkészleten keresztül. Az ilyen átjárók olyan protokollt használhatnak, amely kapcsolatonként több eszközidentitást támogat, például az AMQP-t a felsőbb réteghez vezető forgalomhoz.

* **Alacsony erőforrás-erőforrásokkal rendelkeznek**. Az MQTT- és HTTPS-kódtárak alapigénye kisebb, mint az AMQP-kódtáraké. Így, ha az eszköz korlátozott erőforrásokkal rendelkezik (például kevesebb, mint 1 MB RAM), ezek a protokollok lehetnek az egyetlen rendelkezésre álló protokollimplementációk.

* **Hálózati bejárás**. A szabványos AMQP protokoll az 5671-es portot használja, az MQTT pedig a 8883-as porton figyel. Ezeknek a portoknak az USe-je problémákat okozhat a nem HTTPS protokollok számára bezárt hálózatokban. Ebben az esetben használja az MQTT-t a WebSockets, az AMQP websocketeken vagy https protokollon keresztül.

* **Hasznos teher mérete**. Az MQTT és az AMQP bináris protokollok, amelyek a HTTPS-nél kompaktabb hasznos terhet eredményeznek.

> [!WARNING]
> HTTPS használata esetén minden eszköznek legfeljebb 25 percenként kell lekérnie a felhőből az eszközre irányuló üzeneteket. A fejlesztés során minden eszköz lekérdezési gyakrabban, ha szükséges.

## <a name="port-numbers"></a>Portszámok

Az eszközök különböző protokollok használatával kommunikálhatnak az Azure-beli IoT Hubbal. A protokoll kiválasztását általában a megoldás speciális követelményei határozzák meg. Az alábbi táblázat azokat a kimenő portokat sorolja fel, amelyeknek meg kell nyitva lenniük ahhoz, hogy egy eszköz egy adott protokollt használhasson:

| Protocol (Protokoll) | Port |
| --- | --- |
| MQTT |8883 |
| MQTT WebSocketen keresztül |443 |
| AMQP |5671 |
| AMQP WebSocketen keresztül |443 |
| HTTPS |443 |

Miután létrehozott egy IoT-központot egy Azure-régióban, az IoT hub megtartja ugyanazt az IP-címet az IoT hub élettartama alatt. Ha azonban a Microsoft áthelyezi az IoT-központot egy másik méretezési egységre a szolgáltatás minőségének fenntartása érdekében, akkor új IP-címet kap.

## <a name="next-steps"></a>További lépések

Ha többet szeretne tudni arról, hogy az IoT Hub hogyan valósítja meg az MQTT protokollt, olvassa el [az IoT hub kommunikációja az MQTT protokoll használatával című témakört.](iot-hub-mqtt-support.md)

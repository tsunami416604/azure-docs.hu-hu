---
title: Az Azure IoT Hub hibáinak elhárítása 401003 IoTHubUnauthorized
description: Ismerje meg, hogyan javíthatja a 401003-es hibát a IoTHubUnauthorized
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f46d41c8287d03cbe9582ed560244cbd85cdeeaa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759591"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Ez a cikk a **401003 IoTHubUnauthorized** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

### <a name="symptom-1"></a>1. tünet

A diagnosztikai naplókban megtekintheti az **401003 IoTHubUnauthorized**és a **404104 DeviceConnectionClosedRemotely**, majd a röviddel később sikeresen összekapcsoló eszközök mintáját.

### <a name="symptom-2"></a>2. tünet

A következő hibaüzenetek egyikével sikertelen IoT Hub kérelmek:

* Az engedélyezési fejléc hiányzik
* A (\*z) "" IotHub nem tartalmazza a\*megadott "" eszközt
* A (z\*) "" engedélyezési szabály nem engedélyezi\*a (z) "" elérését
* Az eszköz hitelesítése nem sikerült, a jogkivonat vagy tanúsítvány megújítása és újracsatlakoztatás
* Az ujjlenyomat nem felel meg a konfigurációnak: ujjlenyomat\*: SHA1Hash =\*, SHA2Hash =; Konfiguráció: PrimaryThumbprint =\*, SecondaryThumbprint =\*

## <a name="cause"></a>Ok

### <a name="cause-1"></a>1. ok

A MQTT esetében egyes SDK-k az IoT Hub-t használják, hogy kiállítsák a leválasztást, amikor az SAS-token lejár a frissítéshez. Így 

1. Az SAS-token lejár
1. IoT Hub észleli a lejáratot, és leválasztja az eszközt a **401003 IoTHubUnauthorized**
1. Az eszköz befejezi a leválasztást a **404104 DeviceConnectionClosedRemotely**
1. A IoT SDK új SAS-jogkivonatot hoz létre
1. Az eszköz újrakapcsolódik IoT Hub sikeresen

### <a name="cause-2"></a>2. ok

A IoT Hub nem tudta hitelesíteni az Auth fejlécét, szabályát vagy kulcsát.

## <a name="solution"></a>Megoldás

### <a name="solution-1"></a>1. megoldás

Nincs szükség beavatkozásra, ha az IoT SDK-t használja az eszköz kapcsolati karakterláncával való kapcsolathoz. A IoT SDK újragenerálta az új tokent az SAS-jogkivonat lejáratának újrakapcsolódásához. 

Ha a hibák mennyisége aggodalomra ad okot, váltson a C SDK-ra, amely megújítja az SAS-jogkivonatot a lejárat előtt. Emellett a AMQP az SAS-jogkivonat a kapcsolat leválasztása nélkül is frissíthető.

### <a name="solution-2"></a>2. megoldás

Általánosságban elmondható, hogy a hibaüzenet kijavításának módját a következő üzenet ismerteti:. Ha valamilyen okból nem fér hozzá a hibaüzenet részleteihez, ügyeljen a következőre:

- Az SAS vagy más használt biztonsági jogkivonat nem járt le. 
- Az engedélyezési hitelesítő adat jól formázott a használt protokollhoz. További információ: [IoT hub hozzáférés-vezérlés](iot-hub-devguide-security.md).
- A használt engedélyezési szabály jogosult a kért műveletre.

## <a name="next-steps"></a>További lépések

Ahhoz, hogy könnyebben IoT Hub a hitelesítés, javasoljuk, hogy az [Azure IoT SDK](iot-hub-devguide-sdks.md)-kat használja.
---
title: Az Azure IoT Hub 401003-as hibahiba
description: Ismerje meg, hogyan lehet kijavítani a 401003-as IoTHubUnauthorized hibát
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: d0a9f643516af5945037acb3dd1da24b06944171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284407"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

Ez a cikk a **401003 IoTHubUnauthorized** hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Probléma

### <a name="symptom-1"></a>1. tünet

A diagnosztikai naplókban megjelenik a **401003 IoTHubUnauthorized**kapcsolattal leválasztott eszközök mintája, majd **a 404104 DeviceConnectionClosedRemotely**, majd röviddel ezután sikeresen csatlakozik.

### <a name="symptom-2"></a>2. tünet

Az IoT Hubra irányuló kérelmek sikertelenek az alábbi hibaüzenetek egyikével:

* Hiányzik az engedélyezési fejléc
* Az IotHub '\*' nem tartalmazza\*a megadott eszközt '
* Az engedélyezési\*szabály " '\*nem engedélyezi a hozzáférést '
* A hitelesítés nem sikerült az eszközön, megújult a jogkivonat vagy a tanúsítvány, és újra csatlakozhat
* Az ujjlenyomat nem felel meg a konfigurációnak:\*Ujjlenyomat: SHA1Hash= , SHA2Hash=\*; Konfiguráció: PrimaryThumbprint=\*, SecondaryThumbprint=\*

## <a name="cause"></a>Ok

### <a name="cause-1"></a>1. ok

Az MQTT egyes SDK-k az IoT Hub-ra támaszkodnak a sas-jogkivonat lejártakor történő kapcsolatbontás esetén, hogy tudják, mikor kell frissíteni. Szóval 

1. A SAS-token lejár
1. Az IoT Hub észleli a lejárati időt, és leválasztja az eszközt a **401003 IoTHubUnauthorized**
1. Az eszköz befejezi a **404104 DeviceConnectionClosedWithLeLeglezárva**
1. Az IoT SDK új SAS-jogkivonatot hoz létre
1. Az eszköz sikeresen csatlakozik az IoT Hubhoz

### <a name="cause-2"></a>2. ok

Az IoT Hub nem tudta hitelesíteni az auth fejlécet, szabályt vagy kulcsot.

## <a name="solution"></a>Megoldás

### <a name="solution-1"></a>1. megoldás

Nincs szükség műveletre, ha az IoT SDK-t használja az eszköz kapcsolati karakterláncát használó kapcsolathoz. Az IoT SDK újragenerálja az új jogkivonatot a SAS-jogkivonat lejárati újracsatlakozásához. 

Ha a hibák mennyisége aggodalomra ad okot, váltson a C SDK-ra, amely megújítja a SAS-jogkivonatot a lejárat előtt. Emellett az AMQP esetében a SAS-jogkivonat leválasztás nélkül is frissíthető.

### <a name="solution-2"></a>2. megoldás

A bemutatott hibaüzenetnek általában el kell magyaráznia, hogyan lehet kijavítani a hibát. Ha valamilyen okból nem fér hozzá a hibaüzenet részleteihez, győződjön meg arról, hogy:

- A SAS vagy más biztonsági jogkivonat használata nem járt le. 
- Az engedélyezési hitelesítő adatok jól formázott a használt protokollhoz. További információ: [IoT Hub access control](iot-hub-devguide-security.md).
- A használt engedélyezési szabály rendelkezik a kért művelet engedélyével.

## <a name="next-steps"></a>További lépések

Az IoT Hub hitelesítésének megkönnyítése érdekében javasoljuk az [Azure IoT SDK-k](iot-hub-devguide-sdks.md)használatát.
---
title: Az Azure IoT Device kiépítési szolgáltatás MQTT-támogatásának ismertetése | Microsoft Docs
description: Fejlesztői útmutató – az Azure IoT-eszköz kiépítési szolgáltatásához csatlakozó eszközök támogatása a MQTT protokoll használatával.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.openlocfilehash: fb39979e321c4815aec74c801cd2d04e81b7e533
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74093903"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Kommunikáció a DPS használatával a MQTT protokollal

A DPS lehetővé teszi, hogy az eszközök a következő paranccsal kommunikáljanak a DPS-eszköz végpontjának használatával:

* [MQTT v 3.1.1](https://mqtt.org/) a 8883-es porton
* [MQTT v 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) a 443-es porton keresztüli WebSocket-en keresztül.

A DPS nem a teljes funkcionalitású MQTT-közvetítő, és nem támogatja az MQTT v 3.1.1 szabványban megadott összes viselkedést. Ez a cikk azt ismerteti, hogyan használhatók az eszközök a támogatott MQTT-viselkedésekkel a DPS-vel való kommunikációhoz.

A DPS-vel való kommunikációt a TLS/SSL protokollal kell biztosítani. Ezért a DPS nem támogatja a nem biztonságos kapcsolatokat a 1883-as porton keresztül.

 > [!NOTE] 
 > A DPS jelenleg nem támogatja a TPM- [igazolási mechanizmust](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) használó eszközöket az MQTT protokollon keresztül.

## <a name="connecting-to-dps"></a>Csatlakozás a DPS-hez

Az eszközök a MQTT protokoll használatával csatlakozhatnak a DPS-hez az alábbi lehetőségek bármelyikével.

* Az [Azure IoT üzembe](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks)helyezési SDK-k könyvtárai.
* A MQTT protokoll közvetlenül.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>A MQTT protokoll használata közvetlenül (eszközként)

Ha egy eszköz nem tudja használni az eszköz SDK-kat, akkor továbbra is csatlakozhat a nyilvános eszközök végpontjaihoz a 8883-es porton az MQTT protokoll használatával. A **csatlakoztatási** csomagban az eszköznek a következő értékeket kell használnia:

* A **ClientId** mezőben használja a **regisztrációban**.

* A **Felhasználónév** mezőben használja a `{idScope}/registrations/{registration_id}/api-version=2019-03-31`, ahol a `{idScope}` a DPS [idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) .

* A **Password (jelszó** ) mezőben használjon sas-tokent. Az SAS-token formátuma megegyezik a HTTPS és a AMQP protokollok esetében is:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` a resourceURI formátumának `{idScope}/registrations/{registration_id}`nak kell lennie. A szabályzat nevének `registration`nak kell lennie.

  > [!NOTE]
  > Ha X. 509 tanúsítványalapú hitelesítést használ, az SAS-jogkivonat jelszavai nem szükségesek.

  Az SAS-tokenek létrehozásával kapcsolatos további információkért tekintse [meg a DPS hozzáférés vezérlése](how-to-control-access.md#security-tokens)a biztonsági jogkivonatok című szakaszt.

A következő lista felsorolja a DPS implementáció-specifikus viselkedéseit:

 * A DPS nem támogatja a **CleanSession** jelző funkciójának **0-ra**való beállítását.

 * Ha egy eszköz egy, a **QoS 2**szolgáltatással foglalkozó témakörre van előfizetve, a DPS maximális QoS-szintet biztosít a **SUBACK** -csomagban. Ezután a DPS a QoS 1 használatával továbbítja az üzeneteket az eszköznek.

## <a name="tlsssl-configuration"></a>TLS/SSL-konfiguráció

Ahhoz, hogy az ügyfél közvetlenül használhassa a MQTT protokollt, a TLS 1,2-en keresztül *kell* csatlakoznia. Csatlakozási hibák esetén a lépés kihagyása sikertelen.


## <a name="registering-a-device"></a>Eszköz regisztrálása

Az eszköz DPS-n keresztüli regisztrálásához az eszköznek **a `$dps/registrations/res/#`** használatával kell előfizetnie. A témakör szűrő többszintű helyettesítő karakteres `#` csak arra szolgál, hogy az eszköz további tulajdonságokat kapjon a témakör nevében. A DPS nem engedélyezi a `#` vagy `?` helyettesítő karakter használatát az altémakörök szűréséhez. Mivel a DPS nem általános célú pub-sub üzenetküldési közvetítő, csak a dokumentált témakörök nevét és a témakör szűrőit támogatja.

Az eszköznek közzé kell tennie egy regisztrációs üzenetet a DPS-ben a `$dps/registrations/PUT/iotdps-register/?$rid={request_id}`ként, a **témakör neve**alapján. A hasznos adatoknak JSON formátumban kell tartalmazniuk az [eszköz regisztrációs](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) objektumát.
Sikeres forgatókönyv esetén az eszköz választ kap a `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` témakör nevére, ahol az x az újrapróbálkozási érték másodpercben. A válasz hasznos tartalma JSON formátumban fogja tartalmazni a [RegistrationOperationStatus](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) objektumot.

## <a name="polling-for-registration-operation-status"></a>A regisztrációs művelet állapotának lekérdezése

Az eszköznek rendszeresen le kell kérdezni a szolgáltatást, hogy megkapja az eszköz regisztrációs műveletének eredményét. Feltételezve, hogy az eszköz már előfizetett a `$dps/registrations/res/#` témakörre a fentiekben leírtak szerint, közzéteheti a Get operationstatus üzenetet a `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` témakör nevében. Az üzenetben szereplő műveleti AZONOSÍTÓnak az előző lépésben RegistrationOperationStatus válaszüzenetben kapott értéknek kell lennie. A sikeres esetben a szolgáltatás reagálni fog a `$dps/registrations/res/200/?$rid={request_id}` témakörre. A válasz hasznos tartalma a RegistrationOperationStatus objektumot fogja tartalmazni. Az eszköznek továbbra is le kell kérdezni a szolgáltatást, ha a válasz kódja 202 az újrapróbálkozási időszakot megegyező késés után. Az eszköz regisztrációs művelete sikeres, ha a szolgáltatás 200 állapotkódot ad vissza.

## <a name="connecting-over-websocket"></a>Csatlakozás WebSocket-kapcsolaton keresztül
WebSocket-kapcsolaton keresztüli kapcsolódáskor adja meg az alprotokollot `mqtt`ként. Kövesse az [RFC 6455](https://tools.ietf.org/html/rfc6455)-et.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az MQTT protokollról, tekintse meg a [MQTT dokumentációját](https://mqtt.org/documentation).

A DPS képességeinek további megismeréséhez lásd:

* [Tudnivalók a IoT DPS-ről](about-iot-dps.md)

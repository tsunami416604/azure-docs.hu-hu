---
title: Az Azure IoT-eszközkiépítési szolgáltatás MQTT-támogatásának ismertetése | Microsoft dokumentumok
description: Fejlesztői útmutató – az Azure IoT-eszközkiépítési szolgáltatáshoz (DPS) eszközfelé néző végponthoz az MQTT protokoll használatával csatlakozó eszközök támogatása.
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.openlocfilehash: ea6ece7e34ddb9c25f9f8349239ab3a1c3405abf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973373"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Kommunikáció a DPS-szel az MQTT protokoll használatával

A DPS lehetővé teszi, hogy az eszközök a következő eszközök használatával kommunikáljanak a DPS-eszköz végpontjával:

* [MQTT v3.1.1](https://mqtt.org/) a 8883-as porton
* [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) a 443-as porton lévő WebSocket felett.

A DPS nem teljes értékű MQTT bróker, és nem támogatja az MQTT v3.1.1 szabványban megadott összes viselkedést. Ez a cikk azt ismerteti, hogy az eszközök hogyan használhatják a támogatott MQTT-viselkedéseket a DPS-szel való kommunikációhoz.

A DPS-szel folytatott minden eszközkommunikációt TLS/SSL-lel kell biztosítani. Ezért a DPS nem támogatja a nem biztonságos kapcsolatokat az 1883-as porton keresztül.

 > [!NOTE] 
 > A DPS jelenleg nem támogatja a [TPM-tanúsítványmechanizmust](https://docs.microsoft.com/azure/iot-dps/concepts-device#attestation-mechanism) használó eszközöket az MQTT protokollon keresztül.

## <a name="connecting-to-dps"></a>Csatlakozás a DPS-hez

Az eszközök az MQTT protokoll segítségével csatlakozhatnak a DPS-hez az alábbi lehetőségek bármelyikével.

* Az [Azure IoT-kiépítési SDK-k könyvtárai.](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#microsoft-azure-provisioning-sdks)
* Közvetlenül az MQTT protokoll.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Az MQTT protokoll közvetlen használata (eszközként)

Ha egy eszköz nem tudja használni az eszköz SDK-k, továbbra is csatlakozhat a nyilvános eszköz végpontok segítségével MQTT protokoll port 8883. A **CONNECT** csomagban az eszköznek a következő értékeket kell használnia:

* Az **Ügyfélazonosító** mezőben használja a **registrationId mezőt.**

* A **Felhasználónév** mezőben `{idScope}/registrations/{registration_id}/api-version=2019-03-31`használja `{idScope}` a mezőt, ahol a DPS [idScope](https://docs.microsoft.com/azure/iot-dps/concepts-device#id-scope) hatóköre található.

* A **Jelszó** mezőben használjon SAS-jogkivonatot. A SAS-jogkivonat formátuma megegyezik a HTTPS és az AMQP protokollok formátumával:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration`A resourceURI formátumban `{idScope}/registrations/{registration_id}`kell lennie. A házirend nevének a legyen `registration`.

  > [!NOTE]
  > Ha X.509 tanúsítványhitelesítést használ, a SAS-jogkivonat-jelszavak nem szükségesek.

  A SAS-jogkivonatok létrehozásáról a [DPS-hez való hozzáférés szabályozása](how-to-control-access.md#security-tokens)biztonsági jogkivonatok című szakaszában talál további információt.

Az alábbi lista a DPS implementáció-specifikus viselkedések:

 * A DPS nem támogatja a **CleanSession** jelző **0-ra**beállított működését.

 * Amikor egy eszközalkalmazás **qos 2-vel**rendelkező témakörre fizet elő, a DPS maximális QoS 1-es szintet biztosít a **SUBACK** csomagban. Ezt követően a DPS üzeneteket küld az eszköznek a QoS 1 használatával.

## <a name="tlsssl-configuration"></a>TLS/SSL konfiguráció

Az MQTT protokoll közvetlen használatához az *ügyfélnek* a TLS 1.2-es protokollon keresztül kell csatlakoznia. A lépés kihagyására tett kísérletek csatlakozási hibákkal sikertelenek.


## <a name="registering-a-device"></a>Eszköz regisztrálása

Ha egy eszközt DPS-en keresztül `$dps/registrations/res/#` szeretne regisztrálni, az eszköznek **témakörszűrőként**kell előfizetnie. A témakörszűrőben `#` lévő többszintű helyettesítő karakter csak arra szolgál, hogy az eszköz további tulajdonságokat fogadhasson a témakör nevében. A DPS nem engedélyezi `#` a `?` vagy helyettesítő karakterek használatát az altémakörök szűréséhez. Mivel a DPS nem egy általános célú pub-sub üzenetküldő bróker, csak a dokumentált témakörneveket és témakörszűrőket támogatja.

Az eszköznek közzé kell tennie `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` egy regiszterüzenetet a DPS-nek **a témakörnévként**használva. A hasznos adatnak JSON formátumban kell tartalmaznia az [eszközregisztrációs](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) objektumot.
Sikeres esetben az eszköz választ kap a `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` témakör nevére, ahol x az újrapróbálkozás utáni érték másodpercben. A válasz hasznos terhelése [JSON](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) formátumban tartalmazza a RegistrationOperationStatus objektumot.

## <a name="polling-for-registration-operation-status"></a>Lekérdezés a regisztrációs művelet állapotához

Az eszköznek rendszeres időközönként le kell hallgatnia a szolgáltatást, hogy megkapja az eszköz regisztrációs műveletének eredményét. Feltételezve, hogy az eszköz `$dps/registrations/res/#` már előfizetett a témakörre a fent jelzettek szerint, közzétehet egy get operationstatus üzenetet a `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` témakör nevére. Az üzenetben szereplő műveletazonosítónak az előző lépésben a RegistrationOperationStatus válaszüzenetben kapott értéknek kell lennie. A sikeres esetben a szolgáltatás válaszol `$dps/registrations/res/200/?$rid={request_id}` a témában. A válasz hasznos terhelése tartalmazza a RegistrationOperationStatus objektumot. Az eszköz nek folytatnia kell a szolgáltatás lekérdezését, ha a válaszkód 202 az újrapróbálkozásutáni időszaknak megfelelő késleltetés után. Az eszközregisztrációs művelet sikeres, ha a szolgáltatás 200-as állapotkódot ad vissza.

## <a name="connecting-over-websocket"></a>Csatlakozás a Websocket en keresztül
Amikor websocket en keresztül csatlakozik, `mqtt`adja meg az alprotokollt a következőként: . Kövesse [az RFC 6455 -et](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>További lépések

Az MQTT protokollról az [MQTT dokumentációban](https://mqtt.org/documentation)olvashat bővebben.

A DPS képességeinek további megismeréséhez lásd:

* [Az IoT DPS-ről](about-iot-dps.md)

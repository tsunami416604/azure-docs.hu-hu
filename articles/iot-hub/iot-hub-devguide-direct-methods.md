---
title: Megismerheti az Azure IoT Hub közvetlen metódusok |} A Microsoft Docs
description: Fejlesztői útmutató – közvetlen metódusok használata az eszközök szolgáltatás alkalmazásból származó kód meghívásához.
author: nberdy
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: nberdy
ms.openlocfilehash: 881262816fc8bd634b7f577fd05aa0c8c062e4ca
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126524"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Megismerheti, és az IoT hubról közvetlen metódusok meghívása
Az IoT Hub lehetővé teszi, az eszközökön a felhőből közvetlen metódusok meghívása. Közvetlen metódusok képviseli egy kérés-válasz interakció egy HTTP-hívással, hasonló eszközzel, abban, hogy azok sikeres, vagy közvetlenül (felhasználó által megadott időtúllépési) után sikertelen. Ez a módszer hasznos olyan forgatókönyvekben, ahol azonnali lépéseket, attól függően változik, tud válaszolni volt-e az eszközön.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Minden egyes eszköz módszer egy adott eszköz célozza. [Feladatok] [ lnk-devguide-jobs] módon több eszközön közvetlen metódusok meghívása és a leválasztott eszközöket a metódus meghívásának ütemezéséhez.

Bárki **szolgáltatás csatlakozása** az IoT hub engedélyekkel előfordulhat, hogy az eszközön metódus meghívása.

Közvetlen metódusok kérés-válasz mintát követi, és úgy van kialakítva, az eredmények azonnali megerősítési igénylő kommunikációhoz. Például interaktív vezérlő az eszköz, például ne tudják bekapcsolni a egy ventilátor.

Tekintse meg [felhőből az eszközre irányuló kommunikáció útmutatást] [ lnk-c2d-guidance] Ha kétségei vannak az kívánt tulajdonságok között, a közvetlen metódusok, vagy a felhőből az eszközre irányuló üzenetek.

## <a name="method-lifecycle"></a>Módszer életciklusa
Közvetlen metódusok vannak megvalósítva, az eszközön, és szükség lehet nulla vagy több bemeneti adatokat az a módszer hasznos megfelelően elindítását. A szolgáltatás által használt URI-t keresztül közvetlen metódus meghívása (`{iot hub}/twins/{device id}/methods/`). Egy eszköz megkapja egy eszközspecifikus MQTT témakör keresztül közvetlen metódusok (`$iothub/methods/POST/{method name}/`) vagy az AMQP-kapcsolaton keresztül (`IoThub-methodname` és `IoThub-status` alkalmazás tulajdonságai). 

> [!NOTE]
> Ön közvetlen metódus meghívása az eszköz, nevét és értékeit tartalmazhatnak US-ASCII nyomtatható alfanumerikus, kivéve a következő set: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.
> 
> 

Közvetlen módszerek a következők szinkron, és akár sikeres, vagy az időkorlát után sikertelen (alapértelmezés: 30 másodperc, állítható be 3600 másodperc). Közvetlen metódusok interaktív forgatókönyvek, ahol azt szeretné, hogy egy eszköz való működésre, csak ha az eszköz nem online és a fogadó parancsok hasznosak. Például ne tudják bekapcsolni a telefonon egy világos. Ezekben az esetekben meg szeretné tekinteni egy azonnali sikeres vagy sikertelen, így a felhőszolgáltatás működhet-e az eredmény a lehető leghamarabb. Az eszköz bizonyos üzenettörzs eredményeként a metódus adhatnak vissza, de ez nem szükséges ehhez a metódushoz. Nincs a rendezés nem tudunk garanciát vagy bármely egyidejűségi szemantika metódust hívja meg.

Közvetlen metódusok vannak csak HTTPS-felhő oldalon, és az MQTT vagy AMQP az eszköz oldaláról.

A metódus kérelmek és válaszok hasznos egy JSON-dokumentuma legfeljebb 128 KB.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Háttér-alkalmazásból származó közvetlen metódus meghívása
### <a name="method-invocation"></a>Metódus meghívása
Közvetlen metódus meghívásához egy eszközön a HTTPS-hívások alkotó:

* A *kérés URI-ja* az eszközre, az adott a [API-verzió](/rest/api/iothub/service/invokedevicemethod):

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* A hozzászólás *metódus*
* *A fejlécek* , amely tartalmazza az engedélyezési, kérelem azonosítója, a tartalom típusa és a tartalomkódolás
* A transzparens JSON *törzs* a következő formátumban:

    ```json
    {
        "methodName": "reboot",
        "responseTimeoutInSeconds": 200,
        "payload": {
            "input1": "someInput",
            "input2": "anotherInput"
        }
    }
    ```

Időtúllépés másodpercen belül van. Ha nincs beállítva időkorlát, a rendszer alapértelmezés szerint 30 másodperc.

#### <a name="example"></a>Példa

Alább talál egy barebone példa használatával `curl`. 

```bash
curl -X POST \
  https://iothubname.azure-devices.net/twins/myfirstdevice/methods?api-version=2018-06-30 \
  -H 'Authorization: SharedAccessSignature sr=iothubname.azure-devices.net&sig=x&se=x&skn=iothubowner' \
  -H 'Content-Type: application/json' \
  -d '{
    "methodName": "reboot",
    "responseTimeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}'
```

### <a name="response"></a>Válasz
A háttéralkalmazás, amely választ kap:

* *HTTP-állapotkódot*, az IoT hubról érkező hibák használt, többek között a 404-es hiba eszközök jelenleg nem csatlakoztatott
* *A fejlécek* , amely tartalmazza az ETag, kérelem azonosítója, a tartalom típusa és a tartalomkódolás
* A JSON *törzs* a következő formátumban:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    Mindkét `status` és `body` az eszköz által biztosított és az eszköz saját állapotkódot és/vagy leírás is használt.

### <a name="method-invocation-for-iot-edge-modules"></a>Metódus meghívása az IoT Edge-modulok
A C#-ban támogatott azonosító modullal vyvolání közvetlen metódusok SDK előzetes verzió (elérhető [Itt](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.16.0-preview-004)).

Erre a célra használja a `ServiceClient.InvokeDeviceMethodAsync()` metódust, és adja meg a `deviceId` és `moduleId` paraméterekként.

## <a name="handle-a-direct-method-on-a-device"></a>Kezelni egy eszközön közvetlen metódus
### <a name="mqtt"></a>MQTT
#### <a name="method-invocation"></a>Metódus meghívása
Eszköz megkapja a közvetlen metódus kérelmek MQTT témakörről: `$iothub/methods/POST/{method name}/?$rid={request id}`

A szervezet, amely az eszköz megkapja a következő formátumban kell megadni:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Módszer QoS 0 jár.

#### <a name="response"></a>Válasz
Az eszköz visszajelzést küld `$iothub/methods/res/{status}/?$rid={request id}`, ahol:

* A `status` tulajdonság metódus végrehajtása az eszköz által megadott állapotát.
* A `$rid` tulajdonság a metódus meghívásának kapott az IoT Hub, a kérelem azonosítója.

A szervezet az eszköz által van beállítva, és bármilyen állapot.

### <a name="amqp"></a>AMQP
#### <a name="method-invocation"></a>Metódus meghívása
Az eszköz megkaphatná a közvetlen metódus kéréseket a cím egy olyan receive hivatkozás létrehozásával `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

Az AMQP üzenet érkezik a receive hivatkozásra, amely a metódus kérelem jelöli. A következőket tartalmazza:
* A korrelációs azonosító tulajdonsággal, amely tartalmazza a kérés azonosítója, amely a megfelelő módszer visszajelzéshez kell átadni
* Egy alkalmazás tulajdonsághoz `IoThub-methodname`, amely tartalmazza a meghívott metódus neve
* Az AMQP üzenettörzs tartalmazó a módszer hasznos JSON-fájlként

#### <a name="response"></a>Válasz
Az eszköz metódus a választ adja a cím a küldő hivatkozást hoz létre. `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

A metódusnak a válaszánál küldő hivatkozásra adja vissza, és a következőképpen épül:
* A korrelációs azonosító tulajdonsággal, amely tartalmazza a kérés azonosítója a kérelemüzenetben a metódus átadott
* Egy alkalmazás tulajdonsághoz `IoThub-status`, a felhasználó, amely tartalmazza a megadott metódusállapot
* Az AMQP üzenettörzs tartalmazó JSON-fájlként a metódusra adott válasz

## <a name="additional-reference-material"></a>További – referenciaanyag
Az IoT Hub fejlesztői útmutató más referencia témakörei a következők:

* [IoT Hub-végpontok] [ lnk-endpoints] ismerteti a különféle végpontok, amely minden IoT-központ közzéteszi a futásidejű és felügyeleti műveletekhez.
* [Sávszélesség-szabályozási és kvóták] [ lnk-quotas] ismerteti, amelyek érvényesek a kvóták és a szabályozás működésék, számítson, ha az IoT hubot használni.
* [Az Azure IoT eszköz- és szolgáltatásspecifikus SDK-k] [ lnk-sdks] felsorolja a különböző nyelvű SDK-ban is használhatja az IoT Hub szolgáltatással kommunikáló eszközt és szolgáltatást is alkalmazások fejlesztése során.
* [Az IoT Hub lekérdezési nyelv az ikereszközökhöz, feladatokkal és üzenet-útválasztása] [ lnk-query] ismerteti az IoT Hub lekérdezési nyelv az ikereszközökhöz és feladatokhoz kapcsolatos adatok lekérését az IoT Hub segítségével.
* [IoT Hub MQTT-támogatás] [ lnk-devguide-mqtt] további információ az IoT Hub-támogatásról nyújt az MQTT protokoll.

## <a name="next-steps"></a>További lépések
Most megtanulhatta, hogyan használhatja a közvetlen metódusok, érdekelheti, a következő cikkben az IoT Hub fejlesztői útmutató:

* [Feladatok ütemezése több eszközön][lnk-devguide-jobs]

Ha szeretné, és próbálja ki azokat a jelen cikkben ismertetett fogalmakat, érdekelheti, a következő IoT Hub-oktatóanyag:

* [Közvetlen metódusok használata][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md

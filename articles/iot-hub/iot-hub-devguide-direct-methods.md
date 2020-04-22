---
title: Az Azure IoT Hub közvetlen metódusainak megismerése | Microsoft dokumentumok
description: Fejlesztői útmutató – közvetlen módszerekkel hívja meg a kódot az eszközökön egy szolgáltatásalkalmazásból.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/17/2018
ms.author: rezas
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 13936a55baed59d5b6257f13f69305a1ce72927a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730388"
---
# <a name="understand-and-invoke-direct-methods-from-iot-hub"></a>Közvetlen metódusok megismerése és meghívása az IoT Hubból

Az IoT Hub lehetővé teszi a közvetlen metódusok meghívását a felhőből származó eszközökön. A közvetlen metódusok a HTTP-híváshoz hasonló eszközzel folytatott kérés-válasz interakciót jelentik, mivel azok azonnal sikeresek vagy sikertelenek (a felhasználó által megadott időkérés után). Ez a megközelítés olyan esetekben hasznos, ahol az azonnali cselekvés folyamata eltérő attól függően, hogy az eszköz képes volt-e válaszolni.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Minden eszközmetódus egyetlen eszközt céloz meg. [A feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md) bemutatja, hogyan lehet közvetlen metódusokat meghívni több eszközön, és hogyan ütemezheti a metódusok meghívását a leválasztott eszközökre.

Bárki, aki **szolgáltatás connect** engedélyekkel rendelkezik az IoT Hubon, meghívhat egy metódust egy eszközön.

A közvetlen módszerek a kérés-válasz mintát követik, és olyan kommunikációra szolgálnak, amely azonnali megerősítést igényel az eredményükről. Például az eszköz interaktív vezérlése, például egy ventilátor bekapcsolása.

Tekintse meg a [felhőből az eszközre irányuló kommunikáció útmutatást,](iot-hub-devguide-c2d-guidance.md) ha kétségei vannak a kívánt tulajdonságok, a közvetlen módszerek vagy a felhőből az eszközre irányuló üzenetek használata között.

## <a name="method-lifecycle"></a>Módszer életciklusa

A közvetlen metódusok az eszközön vannak megvalósítva, és a megfelelő példányszámhoz nulla vagy több bemenetre lehet szükség a metódus hasznos adatában. Közvetlen metódust egy szolgáltatásfelé néző`{iot hub}/twins/{device id}/methods/`URI ( ) keresztül hív meg. Az eszköz közvetlen metódusokat fogad egy eszközspecifikus MQTT-témakörön (`$iothub/methods/POST/{method name}/`) `IoThub-methodname` `IoThub-status` vagy AMQP-hivatkozásokon (a és az alkalmazás tulajdonságokon) keresztül. 

> [!NOTE]
> Ha közvetlen metódust hív meg egy eszközön, a tulajdonságnevek és értékek csak US-ASCII nyomtatható alfanumerikus értékeket tartalmazhatnak, kivéve a következő készletben szereplő értékeket:``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``
> 

A közvetlen metódusok szinkronok, és vagy sikeresek vagy sikertelenek az időtúlidőszak után (alapértelmezett: 30 másodperc, 5 és 300 másodperc között állítható). A közvetlen módszerek olyan interaktív helyzetekben hasznosak, amikor azt szeretné, hogy az eszköz akkor és csak akkor működjön, ha az eszköz online állapotban van, és parancsokat fogad. Például a telefon ról bekapcsolni a lámpát. Ezekben a forgatókönyvekben azonnali sikeres vagy sikertelen, hogy a felhőszolgáltatás a lehető leghamarabb az eredmény szerint jár el. Előfordulhat, hogy az eszköz a módszer eredményeképpen visszaad egy üzenettörzset, de a módszer hez nincs szükség. Nincs garancia a rendelésre vagy a metódushívások egyidejűségi szemantikájára.

A közvetlen metódusok csak https-alapúak a felhőoldalról, az MQTT vagy az AMQP pedig az eszköz oldalról.

A metóduskérések és -válaszok hasznos terhelése legfeljebb 128 KB JSON-dokumentum.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Közvetlen metódus meghívása háttéralkalmazásból

Most egy háttéralkalmazásból egy közvetlen metódus meghívása.

### <a name="method-invocation"></a>Metódus meghívása

Az eszközön a közvetlen metódusmetódusok https-hívások, amelyek a következő elemekből állnak:

* Az eszközre jellemző *kérelem URI-ja* az [API-verzióval](/rest/api/iothub/service/devicemethod/invokedevicemethod)együtt:

    ```http
    https://fully-qualified-iothubname.azure-devices.net/twins/{deviceId}/methods?api-version=2018-06-30
    ```

* A POST *módszer*

* Az engedélyezést, a kérelemazonosítót, a tartalomtípust és a tartalomkódolást tartalmazó *fejlécek.*

* Átlátszó *JSON-törzs* a következő formátumban:

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

A `responseTimeoutInSeconds` kérelemben megadott érték az az időmennyiség, amely alatt az IoT Hub-szolgáltatásnak meg kell várnia a közvetlen metódus végrehajtásának befejezését az eszközön. Állítsa be ezt az időoutot legalább addig, amíg egy eszköz közvetlen metódusa várható végrehajtási ideje. Ha nincs megadva időtúl- és időtúlérték, a rendszer a 30 másodperces alapértelmezett értéket használja. A minimális és `responseTimeoutInSeconds` maximális értékek 5, illetve 300 másodpercre vannak.

A `connectTimeoutInSeconds` kérelemhez megadott érték az az időmennyiség, amely az IoT Hub-szolgáltatás nak meg kell várnia egy leválasztott eszköz online állapotba kerülése után egy közvetlen metódus meghívása kor. Az alapértelmezett érték 0, ami azt jelenti, hogy az eszközöknek már online állapotban kell lenniük egy közvetlen metódus meghívásakor. A maximális `connectTimeoutInSeconds` érték 300 másodperc.


#### <a name="example"></a>Példa

Lásd alább a barebone `curl`példa segítségével . 

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

A háttéralkalmazás a következő elemekből álló választ kap:

* *HTTP állapotkód:*
  * A 200 a közvetlen módszer sikeres végrehajtását jelzi;
  * A 404 azt jelzi, hogy az eszközazonosító érvénytelen, vagy hogy az eszköz nem `connectTimeoutInSeconds` volt online, amikor közvetlen metódust kért, majd azt követően (a kiváltó ok megértéséhez használjon kísérő hibaüzenetet);
  * Az 504 azt jelzi, hogy az eszköz nem `responseTimeoutInSeconds`válaszol a közvetlen metódushívásra a rendszeren belül.

* Az ETag-et, a kérelemazonosítót, a tartalomtípust és a tartalomkódolást tartalmazó *fejlécek.*

* *JSON-törzs* a következő formátumban:

    ```json
    {
        "status" : 201,
        "payload" : {...}
    }
    ```

    `status` Mindkettő, `body` és az eszköz biztosítja, és az eszköz saját állapotkódjával és/vagy leírásával válaszol.

### <a name="method-invocation-for-iot-edge-modules"></a>Metódus meghívása IoT Edge-modulokhoz

A modulazonosítót használó közvetlen metódusok meghívását az [IoT Service Client C# SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/)támogatja.

Erre a célra `ServiceClient.InvokeDeviceMethodAsync()` használja a módszert, és adja át a `deviceId` és `moduleId` paraméterekként.

## <a name="handle-a-direct-method-on-a-device"></a>Közvetlen metódus kezelése eszközön

Nézzük meg, hogyan kezelhető egy közvetlen módszer egy IoT-eszközön.

### <a name="mqtt"></a>MQTT

A következő szakasz az MQTT protokollhoz kapcsolódik.

#### <a name="method-invocation"></a>Metódus meghívása

Az eszközök közvetlen metóduskéréseket kapnak `$iothub/methods/POST/{method name}/?$rid={request id}`az MQTT témában: . Az eszközönkénti előfizetések száma legfeljebb 5 lehet. Ezért ajánlott, hogy ne iratkozzon fel minden egyes közvetlen módszerre külön-külön. Ehelyett érdemes előírni, `$iothub/methods/POST/#` majd szűrni a kézbesített üzenetek et a kívánt metódusnevek alapján.

Az eszköz által kapott test formátuma a következő:

```json
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

A metóduskérelmek QoS 0.

#### <a name="response"></a>Válasz

A készülék válaszokat `$iothub/methods/res/{status}/?$rid={request id}`küld a alkalmazásnak, ahol:

* A `status` tulajdonság a metódusvégrehajtásá eszköz által biztosított állapota.

* A `$rid` tulajdonság az IoT Hubtól kapott metódus meghívási azonosítója.

A testet az eszköz állítja be, és bármilyen állapot lehet.

### <a name="amqp"></a>AMQP

A következő szakasz az AMQP protokollhoz kapcsolódik.

#### <a name="method-invocation"></a>Metódus meghívása

Az eszköz közvetlen metóduskéréseket kap a `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`cím fogadási hivatkozásának létrehozásával.

Az AMQP-üzenet a metóduskérést reprezentatot jelölő fogadási hivatkozásra érkezik. Ez tartalmazza a következő szakaszokat:

* A korrelációs azonosító tulajdonság, amely egy kérelemazonosítót tartalmaz, amelyet vissza kell adni a megfelelő metódusválasznak.

* Egy alkalmazástulajdonság, `IoThub-methodname`amely a meghívott metódus nevét tartalmazza.

* Az AMQP üzenet törzse, amely a metódus hasznos adatát json-ként tartalmazza.

#### <a name="response"></a>Válasz

Az eszköz küldő hivatkozást hoz létre, hogy visszaadja a metódusválaszt a címre. `amqps://{hostname}:5671/devices/{deviceId}/methods/deviceBound`

A metódus válasza a küldő linken kerül visszaadásra, és a következőképpen épül fel:

* A korrelációs azonosító tulajdonság, amely a metódus kérésüzenetében átadott kérelemazonosítót tartalmazza.

* Egy alkalmazástulajdonság, `IoThub-status`amely a felhasználó által megadott metódusállapotot tartalmazza.

* Az AMQP üzenet törzse, amely a metódusválaszt JSON-ként tartalmazza.

## <a name="additional-reference-material"></a>További referenciaanyagok

Az IoT Hub fejlesztői útmutatójának további referenciatémakörei a következők:

* [Az IoT Hub-végpontok](iot-hub-devguide-endpoints.md) ismerteti a különböző végpontok, amelyek az egyes IoT-központok elérhetővé teszi a futásidejű és felügyeleti műveletek.

* [A sávszélesség-szabályozás és](iot-hub-devguide-quotas-throttling.md) a kvóták ismerteti a kvótákat, amelyek vonatkoznak, és a szabályozás viselkedése várható, ha az IoT Hub használatakor.

* [Az Azure IoT-eszközök és szolgáltatások SDK-k](iot-hub-devguide-sdks.md) felsorolja a különböző nyelvi SDK-k segítségével, ha az IoT Hub-szolgáltatást használó eszköz- és szolgáltatásalkalmazásokat is fejleszt.

* [Az IoT Hub iker- és üzenettovábbítási nyelvének IoT Hub-lekérdezési nyelve](iot-hub-devguide-query-language.md) az IoT Hub-lekérdezési nyelv, amely az eszköz twins és feladatok információkat az IoT Hub.

* [Az IoT Hub MQTT-támogatása](iot-hub-mqtt-support.md) további információt nyújt az MQTT protokoll IoT Hub-támogatásáról.

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta a közvetlen módszerek használatát, érdekelheti az IoT Hub fejlesztői útmutatója a következő cikket:

* [Feladatok ütemezése több eszközön](iot-hub-devguide-jobs.md)

Ha szeretné kipróbálni a cikkben ismertetett fogalmak némelyikét, az Alábbi IoT Hub-oktatóanyag érdekelheti:

* [Közvetlen metódusok használata](quickstart-control-device-node.md)
* [Eszközfelügyelet VS Code-hoz készült Azure IoT-eszközökkel](iot-hub-device-management-iot-toolkit.md)

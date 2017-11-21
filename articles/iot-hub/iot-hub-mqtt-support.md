---
title: "Azure IoT Hub MQTT támogatás ismertetése |} Microsoft Docs"
description: "Fejlesztői útmutató - eszközök csatlakoztatása az IoT Hub eszköz felé néző végpont a MQTT protokollal támogatása. Az Azure IoT-eszközök SDK-k MQTT támogatja a beépített kapcsolatos adatokat tartalmaz."
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: 
ms.assetid: 1d71c27c-b466-4a40-b95b-d6550cf85144
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/11/2017
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 22379dd7cb0118983505237fa16f01a865a53306
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2017
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Az IoT hub a MQTT protokoll segítségével kommunikálnak.

Az IoT-központ lehetővé teszi, hogy az eszköz kommunikáljon az IoT Hub eszköz végpontjaitól az [MQTT v3.1.1] [ lnk-mqtt-org] protokoll-as port 8883 vagy MQTT v3.1.1 WebSocket protokoll 443-as porton keresztül. Az IoT-központ szükséges összes eszköz kommunikáció biztonságát, a TLS/SSL használatára (emiatt az IoT-központ nem támogatja a nem biztonságos kapcsolatok 1883 porton keresztül).

## <a name="connecting-to-iot-hub"></a>Kapcsolódás az IoT-központ

Egy eszköz a MQTT protokoll használható az IoT-központ keresztül a tárak csatlakozni a [Azure IoT SDK-k] [ lnk-device-sdks] vagy a MQTT használatával protokoll közvetlenül.

## <a name="using-the-device-sdks"></a>Az SDK-k eszközt

[Eszközoldali SDK-k] [ lnk-device-sdks] támogató a MQTT protokoll érhetők el a Java, Node.js, C, C# és Python. Az eszköz SDK-k a szabványos IoT-központ kapcsolati karakterlánc használatával kapcsolatot létrehozni az IoT-központ. A MQTT protokoll használatához az ügyfél protokoll paraméter értékre kell állítani **MQTT**. Alapértelmezés szerint az eszköz SDK-k csatlakozzon egy IoT hubot a **CleanSession** jelző beállítása **0** és **QoS 1** és az IoT hub üzenet exchange-hez.

Ha egy eszköz csatlakozik az IoT-központ, az eszköz SDK-k, amelyek lehetővé teszik az eszköz üzenetek küldését, és az IoT-központ érkező üzenetek fogadására módszerek adja meg.

A következő táblázat minden támogatott nyelven mintakódok mutató hivatkozásokat tartalmaz, és adja meg a paraméter a kapcsolatot a MQTT protokollal IoT-központ használatával.

| Nyelv | Protokoll paraméter |
| --- | --- |
| [NODE.js][lnk-sample-node] |Azure-iot-eszközök – mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Egy eszköz alkalmazásának áttelepítése AMQP MQTT

Ha használja a [eszköz SDK-k][lnk-device-sdks], az ügyfél inicializálása a protokoll paraméter módosítani, ahogy korábban is hangsúlyoztuk AMQP MQTT történő használatával való váltás kell.

Amikor megtenné, ellenőrizze, hogy a következő elemek:

* AMQP hibáit sok feltételeket, amíg a MQTT megszakítja a kapcsolatot. A kivételkezelő logikát is eredményeképpen bizonyos változásokat igényelhet.
* MQTT nem támogatja a *elutasítása* műveletek fogadásakor [felhő-eszközre küldött üzenetek][lnk-messaging]. Ha a háttér-kell az eszköz alkalmazás kapott választ, érdemes lehet [módszerek közvetlen][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Közvetlenül a MQTT protokollal
Ha egy eszköz nem tudja használni az eszköz SDK-k, hogy továbbra is kapcsolódni tud a nyilvános eszköz végpontok a MQTT protokollal 8883 porton. Az a **CONNECT** csomag az eszközt használja a következő értékeket:

* Az a **ClientId** mezőben használja a **deviceId**.
* Az a **felhasználónév** mezőben `{iothubhostname}/{device_id}/api-version=2016-11-14`, ahol a {iothubhostname} az IoT hub teljes CNAME-adatait.

    Például, ha az IoT hub neve **contoso.azure-devices.net** , és ha az eszköz nevére **MyDevice01**, a teljes **felhasználónév** mező kelltartalmaznia`contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`.
* Az a **jelszó** mezőben egy SAS-jogkivonatot használja. A SAS-jogkivonat formátuma ugyanaz, mint a HTTPS és az AMQP protokoll:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    SAS-tokenje kapcsolatos további információkért tekintse meg az eszköz részében [IoT-központ használatával biztonsági jogkivonatokat][lnk-sas-tokens].

    Ha tesztelni, használhatja a [eszköz explorer] [ lnk-device-explorer] eszköz segítségével gyorsan egy SAS-jogkivonatot másolja és illessze be a saját kódot:

  1. Lépjen a **felügyeleti** lapján **eszköz Explorer**.
  2. Kattintson a **SAS-Token** (jobb felső).
  3. A **SASTokenForm**, válassza ki az eszközt a **DeviceID** legördülő listán. Állítsa be a **TTL**.
  4. Kattintson a **Generate** a jogkivonat létrehozásához.

     A SAS-jogkivonat, amely akkor jön létre, ez a struktúra rendelkezik: `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

     Ez a token kívánja használni, mint a részét a **jelszó** MQTT segítségével szeretne csatlakozni a mező: `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

MQTT csatlakozni, és válassza le a csomagok, IoT-központot egy eseményt állít ki a a **Operations figyelés** további információkkal, melyek segíthetnek csatlakozási problémák csatorna.

### <a name="tlsssl-configuration"></a>A TLS/SSL-beállítása

Használatához a MQTT protokoll közvetlenül, az ügyfél *kell* TLS/SSL Csatornán keresztül csatlakozni. Hagyja ki ezt megpróbálja sikertelen lesz, és a csatlakozási hibák.

Ahhoz, hogy a TLS kapcsolatot szeretne letölteni, és a DigiCert Baltimore főtanúsítvány hivatkozik. Ez az Azure használ a kapcsolat biztonságossá tétele érdekében, amely itt található: a tanúsítvány a [Azure iot-sdk--c tárház][lnk-sdk-c-certs]. Ezekről a tanúsítványokról további információ található [Digicert tartozó webhely][lnk-digicert-root-certs].

Példa bemutatja, hogyan megvalósításának a Python verzióját használja a [Paho MQTT könyvtár] [ lnk-paho] által az Eclipse Foundation nézhet ki például a következő.

Először telepítse a Paho könyvtárban a parancssori környezetben:

```
>pip install paho-mqtt
```

Ezután megvalósíthatja az ügyfél egy Python-parancsfájl:

```
from paho.mqtt import client as mqtt
import ssl
  
path_to_root_cer = "...local\\path\\to\\digicert.cer"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
subdomain = "<iothub subdomain>"

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.username_pw_set(username=subdomain+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(subdomain+".azure-devices.net", port=8883)
```


### <a name="sending-device-to-cloud-messages"></a>Eszköz-felhő üzenetek küldése

Sikeres csatlakozás után eszköz üzeneteket küldhetnek az IoT-központ használatával `devices/{device_id}/messages/events/` vagy `devices/{device_id}/messages/events/{property_bag}` , egy **témakör**. A `{property_bag}` elem lehetővé teszi, hogy az eszköz további tulajdonságokkal rendelkező üzenetek küldése egy url-ként kódolt formában. Példa:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Ez `{property_bag}` elem használja, mint a lekérdezési karakterláncok a HTTPS protokoll azonos kódolást.
>
>

Az eszköz alkalmazást is használhatja `devices/{device_id}/messages/events/{property_bag}` , a **lesz a témakör neve** meghatározásához *üzenetek fog* telemetriai üzenetként továbbítását.

- Az IoT-központ nem támogatja a QoS 2 üzeneteket. Ha egy eszköz alkalmazás tesz közzé egy üzenetet, amelyben **QoS 2**, IoT-központ bezárása után a hálózati kapcsolat.
- Az IoT-központ nem maradnak megőrzése üzeneteket. Ha egy eszköz küld egy üzenetet, amelyben a **megőrzése** jelző értéke 1, az IoT-központ hozzáadja a **x-opt-megőrzése** az üzenetek alkalmazás tulajdonság. Ebben az esetben helyett a megőrzése üzenet megőrzése, IoT-központ számára továbbítja azokat a háttér-alkalmazást.
- Az IoT-központ csak eszközönként egy aktív MQTT kapcsolatot támogat. Egy új MQTT kapcsolathoz nevében az ugyanazon Eszközazonosítót hatására az IoT-központ, a meglévő kapcsolat megszakad.

További információkért lásd: [fejlesztői útmutató üzenetküldési][lnk-messaging].

### <a name="receiving-cloud-to-device-messages"></a>Felhő-eszközre küldött üzenetek fogadása

Üzenetek fogadása az IoT-központ, egy eszköz használatával ajánlatos `devices/{device_id}/messages/devicebound/#` , egy **témakör szűrő**. A több szintű helyettesítő  **#**  a témakör szűrő használatával csak az eszköz további tulajdonságok kap, a témakör neve. Az IoT-központ nem teszi lehetővé az használatát, a  **#**  vagy **?** helyettesítő karakterek alárendelt témakörök szűréséhez. Mivel az IoT-központ nem egy általános célú pub-sub üzenetkezelési broker, csak a támogatott dokumentált témakör nevét és a témakör szűrők.

Az eszköz nem fogadhat üzeneteket az IoT-központot, amíg sikeresen feliratkozott a eszközspecifikus végpontra, által képviselt a `devices/{device_id}/messages/devicebound/#` témakör szűrő. Sikeres előfizetés létrehozása után az eszköz elindítja a fogadásra csak felhőalapú eszközre elküldött hozzá az előfizetés az időpont után. Ha az eszköz csatlakozik az **CleanSession** jelző beállítása **0**, az előfizetés megőrződjenek különböző munkamenetek között. Ebben az esetben, amikor legközelebb kapcsolódik a **CleanSession 0** az eszköz megkaphatná a függőben lévő forráshoz lett rá elküldött üzenetek. Ha az eszköz által használt **CleanSession** jelző beállítása **1** azonban azt nem bármely üzenetek fogadása az IoT-központ mindaddig, amíg az eszköz-végpont feliratkozva.

Az IoT-központ lekéri az üzeneteket a **témakör** `devices/{device_id}/messages/devicebound/`, vagy `devices/{device_id}/messages/devicebound/{property_bag}` bármely üzenettulajdonságok esetén. `{property_bag}`url-kódolású kulcs/érték párok az üzenet tulajdonságait tartalmazza. Csak alkalmazáshoz és a felhasználó állítható be rendszer tulajdonságai (például **messageId** vagy **correlationId**) a tulajdonságcsomag szerepelnek. Rendszer tulajdonságnevek előtaggal van ellátva  **$** , alkalmazástulajdonságok használja az eredeti tulajdonság nevét nincs előtagja.

Ha egy eszköz alkalmazás előfizet egy témakör **QoS 2**, IoT-központ maximális QoS szintjének 1 biztosít a **SUBACK** csomagot. Ezt követően az IoT-központ kézbesíti üzenetek a QoS-1-eszközt.

### <a name="retrieving-a-device-twins-properties"></a>Egy eszköz iker tulajdonságainak beolvasása

Először egy eszköz előfizet `$iothub/twin/res/#`, a művelet válaszok fogadására. Ezt követően egy üres üzenetet küld a témakör `$iothub/twin/GET/?$rid={request id}`, ki van töltve értékkel **kérelemazonosító**. A szolgáltatás majd a témakör az eszköz iker adatokat tartalmazó válaszüzenetet küld vissza `$iothub/twin/res/{status}/?$rid={request id}`, azonos **kérelemazonosító** kérés.

Kérelemazonosító tetszőleges érvényes érték, egy üzenet tulajdonságérték lehet megfelelően [IoT Hub fejlesztői útmutató üzenetküldési][lnk-messaging], és állapota egy egész számként van hitelesítve.
Az adott válasz törzsének az eszköz iker tulajdonságok szakasza tartalmazza:

A szervezet a identitás bejegyzés például a "Tulajdonságok" tag korlátozódik:

        {
            "properties": {
                "desired": {
                    "telemetrySendFrequency": "5m",
                    "$version": 12
                },
                "reported": {
                    "telemetrySendFrequency": "5m",
                    "batteryLevel": 55,
                    "$version": 123
                }
            }
        }

A lehetséges állapota kódok a következők:

|status | Leírás |
| ----- | ----------- |
| 200 | Sikeres |
| 429 | Túl sok kérelmek (halmozódni), mint / [IoT Hub-szabályozás][lnk-quotas] |
| 5** | Kiszolgáló hibák |

További információkért lásd: [eszköz twins fejlesztői útmutató][lnk-devguide-twin].

### <a name="update-device-twins-reported-properties"></a>Eszköz iker jelentett tulajdonságainak frissítése

Az alábbi sorrendben ismerteti, hogy egy eszköz frissíti az eszköz a két az IoT hubon jelentett tulajdonságait:

1. Egy eszköz először elő kell a `$iothub/twin/res/#` úgy, hogy a művelet válaszok fogadjon IoT-központ témakör.

1. Egy eszköz, amely tartalmazza az eszköz iker frissítés üzenetet küld a `$iothub/twin/PATCH/properties/reported/?$rid={request id}` témakör. Ez az üzenet tartalmaz egy **kérelemazonosító** érték.

1. A szolgáltatás ezután elküldi a témakör a jelentett tulajdonsággyűjteményében új ETag értékét tartalmazó válaszüzenetet `$iothub/twin/res/{status}/?$rid={request id}`. A válaszüzenet használ azonos **kérelemazonosító** kérés.

A kérelem üzenettörzs egy JSON-dokumentumában, amely új értékek biztosít a jelentett tulajdonságokat (más tulajdonság vagy metaadatai nem módosíthatók) tartalmazza.
A JSON-dokumentum minden tagjának frissíti, vagy adja hozzá a megfelelő tag a két eszköz dokumentumban. Egy tag értéke `null`, törli a tagot a tartalmazó objektum. Példa:

        {
            "telemetrySendFrequency": "35m",
            "batteryLevel": 60
        }

A lehetséges állapota kódok a következők:

|status | Leírás |
| ----- | ----------- |
| 200 | Sikeres |
| 400 | Hibás kérés. Nem megfelelően formázott JSON-ban |
| 429 | Túl sok kérelmek (halmozódni), mint / [IoT Hub-szabályozás][lnk-quotas] |
| 5** | Kiszolgáló hibák |

További információkért lásd: [eszköz twins fejlesztői útmutató][lnk-devguide-twin].

### <a name="receiving-desired-properties-update-notifications"></a>A fogadó kívánt tulajdonságokkal vonatkozó frissítési értesítések

Ha egy eszköz csatlakoztatása az IoT-központ értesítéseket küld a témakör `$iothub/twin/PATCH/properties/desired/?$version={new version}`, tartalmazó végzi el a megoldás háttérrendszeréhez frissítés tartalmát. Példa:

        {
            "telemetrySendFrequency": "5m",
            "route": null
        }

Tulajdonság frissítései, mint `null` érték azt jelenti, hogy a JSON-objektum tag törlése folyamatban van.


> [!IMPORTANT] 
> Az IoT-központ állít elő változási értesítéseket csak akkor, ha a csatlakoztatott eszközök is. Ügyeljen arra, hogy implementálja a [eszköz újracsatlakozás folyamat] [ lnk-devguide-twin-reconnection] tartani a kívánt tulajdonságokkal szinkronizálja az IoT-központ és az eszköz alkalmazás között.

További információkért lásd: [eszköz twins fejlesztői útmutató][lnk-devguide-twin].

### <a name="respond-to-a-direct-method"></a>A közvetlen módszer válaszolni

Először egy eszközhöz tartozik előfizetés `$iothub/methods/POST/#`. Az IoT-központ metódus kérelmek küldése a következő témakörben `$iothub/methods/POST/{method name}/?$rid={request id}`, vagy egy érvényes JSON-adatokat, vagy egy üres szövegtörzzsel.

Válaszol, az eszközre érvényes JSON vagy üres szövegtörzzsel üzenetet küld a témakör `$iothub/methods/res/{status}/?$rid={request id}`, ahol **kérelemazonosító** meg kell egyeznie a kérelemüzenetet, a és **állapot** egész számnak kell lennie.

További információkért lásd: [közvetlen módszer fejlesztői útmutató][lnk-methods].

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

Végső szempont, mint ha testre szeretné szabni a MQTT protokoll viselkedését a felhő oldalon tekintse át a [Azure IoT protokoll-átjáró] [ lnk-azure-protocol-gateway] , amely lehetővé teszi egy nagy teljesítményű egyéni telepítése felületek, közvetlenül az IoT Hub protokoll-átjáró. Az Azure IoT protokoll átjáró lehetővé teszi az eszköz olyan brownfield MQTT központi telepítések protokoll(ok) más egyéni testreszabását. Ezt a módszert igényel, azonban, hogy futtatja, és egy egyéni protokoll-átjáró működik.

## <a name="next-steps"></a>Következő lépések

A MQTT protokoll kapcsolatos további tudnivalókért tekintse meg a [MQTT dokumentáció][lnk-mqtt-docs].

Az IoT-központ telepítésének tervezése kapcsolatos további információkért lásd:

* [Az IoT-eszközök katalógus Azure Certified][lnk-devices]
* [Támogatja a további protokollok][lnk-protocols]
* [Az Event Hubs összehasonlítása][lnk-compare]
* [Méretezés, a magas rendelkezésre ÁLLÁSÚ és vész-Helyreállítási][lnk-scaling]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Az Azure IoT peremhálózati peremeszközök AI központi telepítése][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt

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
ms.date: 03/05/2018
ms.author: elioda
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9acda980583319414cc9e8668424907947a257db
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Az IoT hub a MQTT protokoll segítségével kommunikálnak.

Az IoT-központ lehetővé teszi, hogy az eszköz kommunikáljon az IoT Hub eszköz végpontjaitól:

* [MQTT v3.1.1] [ lnk-mqtt-org] 8883 porton
* MQTT v3.1.1 WebSocket a 443-as porton keresztül.

Az IoT-központ minden eszköz kommunikációs védetté kell tennie a TLS/SSL használatára. Ezért az IoT-központ nem támogatja a nem biztonságos kapcsolatok 1883 porton keresztül.

## <a name="connecting-to-iot-hub"></a>Kapcsolódás az IoT-központ

Egy eszköz a MQTT protokoll segítségével csatlakozzon az IoT hub használatával:

* Vagy a-tárak a [Azure IoT SDK-k][lnk-device-sdks].
* Vagy közvetlenül a MQTT protokoll.

## <a name="using-the-device-sdks"></a>Az SDK-k eszközt

[Eszközoldali SDK-k] [ lnk-device-sdks] támogató a MQTT protokoll érhetők el a Java, Node.js, C, C# és Python. Az eszköz SDK-k a szabványos IoT-központ kapcsolati karakterlánc használatával kapcsolatot létrehozni az IoT-központ. A MQTT protokoll használatához az ügyfél protokoll paraméter értékre kell állítani **MQTT**. Alapértelmezés szerint az eszköz SDK-k csatlakozzon egy IoT hubot a **CleanSession** jelző beállítása **0** és **QoS 1** és az IoT hub üzenet exchange-hez.

Amikor egy eszköz csatlakozik az IoT-központ, az eszköz SDK-k biztosítanak a módszereket, amelyek lehetővé teszik az eszköz számára az IoT-központ üzeneteket.

A következő táblázat minden támogatott nyelven mintakódok mutató hivatkozásokat tartalmaz, és adja meg a paraméter a kapcsolatot a MQTT protokollal IoT-központ használatával.

| Nyelv | Protokoll paraméter |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
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

* Az a **felhasználónév** mezőben `{iothubhostname}/{device_id}/api-version=2016-11-14`, ahol `{iothubhostname}` van az IoT hub teljes CNAME-adatait.

    Például, ha az IoT hub neve **contoso.azure-devices.net** , és ha az eszköz nevére **MyDevice01**, a teljes **felhasználónév** mezőt kell tartalmaznia:

    `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`

* Az a **jelszó** mezőben egy SAS-jogkivonatot használja. A SAS-jogkivonat formátuma ugyanaz, mint a HTTPS és az AMQP protokoll:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Ha X.509 tanúsítvány alapú hitelesítést használ, a SAS-token jelszavak esetén nincs szükség. További információkért lásd: [beállítása az Azure IoT Hub X.509 biztonsága][lnk-x509]

  SAS-tokenje kapcsolatos további információkért tekintse meg az eszköz részében [IoT-központ használatával biztonsági jogkivonatokat][lnk-sas-tokens].

  Ha tesztelni, használhatja a [eszköz explorer] [ lnk-device-explorer] eszköz segítségével gyorsan egy SAS-jogkivonatot másolja és illessze be a saját kódot:

  1. Lépjen a **felügyeleti** lapján **eszköz Explorer**.
  2. Kattintson a **SAS-Token** (jobb felső).
  3. A **SASTokenForm**, válassza ki az eszközt a **DeviceID** legördülő listán. Állítsa be a **TTL**.
  4. Kattintson a **Generate** a jogkivonat létrehozásához.

     A SAS-jogkivonat, amely akkor jön létre, az alábbi szerkezettel rendelkezik:

     `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

     Ez a token kívánja használni, mint a részét a **jelszó** MQTT segítségével szeretne csatlakozni a mező:

     `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT csatlakozni, és válassza le a csomagok, IoT-központot egy eseményt állít ki a a **Operations figyelés** csatorna. Ez az esemény rendelkezik, amelyek segítségével csatlakozási problémák további információt.

Az eszköz alkalmazás megadhat egy **fog** üzenet a **CONNECT** csomagot. Az eszköz alkalmazást kell használnia `devices/{device_id}/messages/events/{property_bag}` vagy `devices/{device_id}/messages/events/{property_bag}` , a **fog** témakör nevének meghatározásához **fog** üzeneteket, telemetriai üzenetet továbbítani. Ebben az esetben ha a hálózati kapcsolat megszakad, de a **DISCONNECT** csomag korábban nem érkezett az eszközről, majd elküldi az IoT-központ a **fog** megadott üzenetet a **CONNECT** csomagot, hogy a telemetria-csatornát. A telemetriai adatok csatorna lehet vagy alapértelmezett **események** végpont vagy egy útválasztási IoT-központ által definiált egyéni végpontot. Az üzenet a **IOT hubbal-MessageType** tulajdonság értéke az **fog** rendelve.

### <a name="tlsssl-configuration"></a>TLS/SSL configuration

Használatához a MQTT protokoll közvetlenül, az ügyfél *kell* TLS/SSL Csatornán keresztül csatlakozni. Kihagyhatja ezt a lépést megpróbálja sikertelen, és a csatlakozási hibák.

Ahhoz, hogy a TLS kapcsolatot szeretne letölteni, és a DigiCert Baltimore főtanúsítvány hivatkozik. Ez a tanúsítvány, amely Azure használ a kapcsolat biztonságossá tétele érdekében. Ezt a tanúsítványt is megtalálhatja a [Azure iot-sdk--c] [ lnk-sdk-c-certs] tárházba. Ezekről a tanúsítványokról további információ található [Digicert tartozó webhely][lnk-digicert-root-certs].

Példa bemutatja, hogyan megvalósításának a Python verzióját használja a [Paho MQTT könyvtár] [ lnk-paho] által az Eclipse Foundation nézhet ki például a következő.

Először telepítse a Paho könyvtárban a parancssori környezetben:

```cmd/sh
pip install paho-mqtt
```

Ezután megvalósíthatja az ügyfél egy Python-parancsfájl. Cserélje le a helyőrzőket a következőképpen:

* `<local path to digicert.cer>` a DigiCert Baltimore főtanúsítvány tartalmazó helyi fájl elérési útja van. A fájl létrehozásához a tanúsítvány információk másolása [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) a sorok tartalmaznak az Azure IoT SDK for c `-----BEGIN CERTIFICATE-----` és `-----END CERTIFICATE-----`, távolítsa el a `"` jelek elején és minden végén, és Távolítsa el a `\r\n` karakterek minden sor végén.
* `<device id from device registry>` a rendszer hozzáadta-e az IoT hub eszköz Azonosítóját.
* `<generated SAS token>` az ebben a cikkben korábban ismertetett módon létrehozott eszköz egy SAS-jogkivonat van.
* `<iot hub name>` az IoT hub neve.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"

def on_connect(client, userdata, flags, rc):
  print ("Device connected with result code: " + str(rc))
def on_disconnect(client, userdata, rc):
  print ("Device disconnected with result code: " + str(rc))
def on_publish(client, userdata, mid):
  print ("Device sent message")

client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" + device_id, password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None, cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

### <a name="sending-device-to-cloud-messages"></a>Eszköz-felhő üzenetek küldése

Sikeres csatlakozás után eszköz üzeneteket küldhetnek az IoT-központ használatával `devices/{device_id}/messages/events/` vagy `devices/{device_id}/messages/events/{property_bag}` , egy **témakör**. A `{property_bag}` elem lehetővé teszi, hogy az eszköz további tulajdonságokkal rendelkező üzenetek küldése egy url-ként kódolt formában. Példa:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Ez `{property_bag}` elem használja, mint a lekérdezési karakterláncok a HTTPS protokoll azonos kódolást.

A következő egy IoT-központ függő viselkedések listáját:

* Az IoT-központ nem támogatja a QoS 2 üzeneteket. Ha egy eszköz alkalmazás tesz közzé egy üzenetet, amelyben **QoS 2**, IoT-központ bezárása után a hálózati kapcsolat.
* Az IoT-központ nem maradnak megőrzése üzeneteket. Ha egy eszköz küld egy üzenetet, amelyben a **megőrzése** jelző értéke 1, az IoT-központ hozzáadja a **x-opt-megőrzése** az üzenetek alkalmazás tulajdonság. Ebben az esetben helyett a megőrzése üzenet megőrzése, IoT-központ számára továbbítja azokat a háttér-alkalmazást.
* Az IoT-központ csak eszközönként egy aktív MQTT kapcsolatot támogat. Egy új MQTT kapcsolathoz nevében az ugyanazon Eszközazonosítót hatására az IoT-központ, a meglévő kapcsolat megszakad.

További információkért lásd: [fejlesztői útmutató üzenetküldési][lnk-messaging].

### <a name="receiving-cloud-to-device-messages"></a>Felhő-eszközre küldött üzenetek fogadása

Üzenetek fogadása az IoT-központ, egy eszköz használatával ajánlatos `devices/{device_id}/messages/devicebound/#` , egy **témakör szűrő**. A több szintű helyettesítő `#` a témakör szűrő használatával csak az eszköz további tulajdonságok kap, a témakör neve. Az IoT-központ nem teszi lehetővé az használatát, a `#` vagy `?` altémakörei a szűréshez helyettesítő karaktereket. Mivel az IoT-központ nem egy általános célú pub-sub üzenetkezelési broker, csak a támogatott dokumentált témakör nevét és a témakör szűrők.

Az eszköz nem fogadhat üzeneteket az IoT-központot, amíg sikeresen feliratkozott a eszközspecifikus végpontra, által képviselt a `devices/{device_id}/messages/devicebound/#` témakör szűrő. Előfizetés létrehozása után az eszköz megkaphatná a felhőből eszközre távozó üzeneteinek hozzá az előfizetés az időpont után. Ha az eszköz csatlakozik az **CleanSession** jelző beállítása **0**, az előfizetés megőrződjenek különböző munkamenetek között. Ebben az esetben, amikor az eszköz csatlakozik az **CleanSession 0** kap a leválasztott küldött függőben lévő üzeneteket. Ha az eszköz által használt **CleanSession** jelző beállítása **1** azonban azt nem bármely üzenetek fogadása az IoT-központ mindaddig, amíg az eszköz-végpont feliratkozva.

Az IoT-központ lekéri az üzeneteket a **témakör** `devices/{device_id}/messages/devicebound/`, vagy `devices/{device_id}/messages/devicebound/{property_bag}` üzenettulajdonságok esetén. `{property_bag}` url-kódolású kulcs/érték párok az üzenet tulajdonságait tartalmazza. Csak alkalmazáshoz és a felhasználó állítható be rendszer tulajdonságai (például **messageId** vagy **correlationId**) a tulajdonságcsomag szerepelnek. Rendszer tulajdonságnevek előtaggal van ellátva  **$** , alkalmazástulajdonságok használja az eredeti tulajdonság nevét nincs előtagja.

Ha egy eszköz alkalmazás előfizet egy témakör **QoS 2**, IoT-központ maximális QoS szintjének 1 biztosít a **SUBACK** csomagot. Ezt követően az IoT-központ kézbesíti üzenetek a QoS-1-eszközt.

### <a name="retrieving-a-device-twins-properties"></a>Egy eszköz iker tulajdonságainak beolvasása

Először egy eszköz előfizet `$iothub/twin/res/#`, a művelet válaszok fogadására. Ezt követően egy üres üzenetet küld a témakör `$iothub/twin/GET/?$rid={request id}`, ki van töltve értékkel **azonosítójú**. A szolgáltatás majd a témakör az eszköz iker adatokat tartalmazó válaszüzenetet küld vissza `$iothub/twin/res/{status}/?$rid={request id}`, azonos **azonosítójú** kérés.

Kérelemazonosító tetszőleges érvényes érték, egy üzenet tulajdonságérték lehet megfelelően [IoT Hub fejlesztői útmutató üzenetküldési][lnk-messaging], és állapota egy egész számként van hitelesítve.

Az adott válasz törzsének az eszköz iker tulajdonságok szakasza tartalmazza. Az alábbi kódrészletben láthatja, a szervezet a identitás bejegyzés korlátozott pedig a "Tulajdonságok" számára, például:

```json
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
```

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

1. Egy eszköz, amely tartalmazza az eszköz iker frissítés üzenetet küld a `$iothub/twin/PATCH/properties/reported/?$rid={request id}` témakör. Ez az üzenet tartalmaz egy **azonosítójú** érték.

1. A szolgáltatás ezután elküldi a témakör a jelentett tulajdonsággyűjteményében új ETag értékét tartalmazó válaszüzenetet `$iothub/twin/res/{status}/?$rid={request id}`. A válaszüzenet használ azonos **azonosítójú** kérés.

A kérelem üzenettörzs egy JSON-dokumentum, a jelentésben szereplő tulajdonságokat új értékeket tartalmazó tartalmazza. A JSON-dokumentum minden tagjának frissíti, vagy adja hozzá a megfelelő tag a két eszköz dokumentumban. Egy tag értéke `null`, törli a tagot a tartalmazó objektum. Példa:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

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

```json
{
    "telemetrySendFrequency": "5m",
    "route": null
}
```

Tulajdonság frissítései, mint `null` érték azt jelenti, hogy a JSON-objektum tag törlése folyamatban van.

> [!IMPORTANT]
> Az IoT-központ állít elő változási értesítéseket csak akkor, ha a csatlakoztatott eszközök is. Ügyeljen arra, hogy implementálja a [eszköz újracsatlakozás folyamat] [ lnk-devguide-twin-reconnection] tartani a kívánt tulajdonságokkal szinkronizálja az IoT-központ és az eszköz alkalmazás között.

További információkért lásd: [eszköz twins fejlesztői útmutató][lnk-devguide-twin].

### <a name="respond-to-a-direct-method"></a>A közvetlen módszer válaszolni

Először egy eszközhöz tartozik előfizetés `$iothub/methods/POST/#`. Az IoT-központ metódus kérelmek küldése a következő témakörben `$iothub/methods/POST/{method name}/?$rid={request id}`, vagy egy érvényes JSON-adatokat, vagy egy üres szövegtörzzsel.

Válaszol, az eszközre érvényes JSON vagy üres szövegtörzzsel üzenetet küld a témakör `$iothub/methods/res/{status}/?$rid={request id}`. Ebben az üzenetben a **azonosítójú** meg kell egyeznie a kérelemüzenetben és **állapot** egész számnak kell lennie.

További információkért lásd: [közvetlen módszer fejlesztői útmutató][lnk-methods].

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

Végső szempont, mint ha testre szeretné szabni a MQTT protokoll viselkedését a felhő oldalon tekintse át a [Azure IoT protokoll-átjáró][lnk-azure-protocol-gateway]. Ez a szoftver lehetővé teszi egy nagy teljesítményű egyéni protokoll-átjáró üzembe helyezéséhez, hogy közvetlenül az IoT-központ felületek. Az Azure IoT protokoll átjáró lehetővé teszi az eszköz olyan brownfield MQTT központi telepítések protokoll(ok) más egyéni testreszabását. Ezt a módszert igényel, azonban, hogy futtatja, és egy egyéni protokoll-átjáró működik.

## <a name="next-steps"></a>További lépések

A MQTT protokoll kapcsolatos további tudnivalókért tekintse meg a [MQTT dokumentáció][lnk-mqtt-docs].

Az IoT-központ telepítésének tervezése kapcsolatos további információkért lásd:

* [Az IoT-eszközök katalógus Azure Certified][lnk-devices]
* [Támogatja a további protokollok][lnk-protocols]
* [Az Event Hubs összehasonlítása][lnk-compare]
* [Méretezés, a magas rendelkezésre ÁLLÁSÚ és vész-Helyreállítási][lnk-scaling]

Az IoT-központ képességeit további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató][lnk-devguide]
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

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
[lnk-x509]: iot-hub-security-x509-get-started.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md
[lnk-sdk-c-certs]: https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c
[lnk-digicert-root-certs]: https://www.digicert.com/digicert-root-certificates.htm
[lnk-paho]: https://pypi.python.org/pypi/paho-mqtt

---
title: Megismerheti az Azure IoT Hub MQTT-támogatás |} A Microsoft Docs
description: Fejlesztői útmutató – eszközök csatlakoztatása az IoT Hub eszköz felé néző végpont az MQTT protokoll használatával támogatása. Mqtt-ről az Azure IoT eszközoldali SDK-kat támogatja a beépített kapcsolatos információkat tartalmaz.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: elioda
ms.openlocfilehash: 2e45422ca6a861894193600eff17f192bc20b357
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2018
ms.locfileid: "42055709"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Az IoT hubhoz az MQTT protokoll használatával kommunikálnak.

Az IoT Hub lehetővé teszi, hogy az eszköz kommunikáljon az IoT Hub-eszköz használatával végpontok:

* [MQTT v3.1.1] [ lnk-mqtt-org] 8883 porton
* MQTT v3.1.1 WebSocket 443-as porton keresztül.

Az IoT Hub nem egy teljes körű MQTT broker és az MQTT v3.1.1 normál megadott viselkedések nem támogatja. Ez a cikk bemutatja, hogyan eszközök az MQTT támogatott viselkedés az IoT hubbal való kommunikációhoz.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hub az összes eszköz kommunikációt a TLS/SSL használatával kell védeni. Ezért az IoT Hub nem támogatja a nem biztonságos kapcsolatok 1883 porton keresztül.

## <a name="connecting-to-iot-hub"></a>Csatlakozás az IoT hubhoz

Egy eszköz az MQTT protokoll használatával csatlakozik az IoT hub használatával:

* A vagy a könyvtárak a [Azure IoT SDK-k][lnk-device-sdks].
* Vagy közvetlenül az MQTT protokoll.

## <a name="using-the-device-sdks"></a>Az eszközoldali SDK-k használatával

[Eszköz SDK-k] [ lnk-device-sdks] támogató az MQTT protokoll érhetők el a Java, Node.js, C, a C# és Python. Az eszközoldali SDK-k a standard IoT Hub kapcsolati karakterlánc használatával egy kapcsolatot egy IoT hubot. Az MQTT protokoll használatával az ügyfél protokoll paramétert állítsa **MQTT**. Alapértelmezés szerint az eszköz SDK-k csatlakozni az IoT Hub a **CleanSession** jelző értékre **0** és **QoS 1** és az IoT hub üzenet Exchange-hez.

Ha egy eszköz csatlakozik az IoT hub, az eszközoldali SDK-k, amelyek lehetővé teszik az eszköz IoT hub-üzenetek módszerek adja meg.

Az alábbi táblázat minden támogatott nyelven Kódminták mutató hivatkozásokat tartalmaz, és adja meg a paraméter egy kapcsolatot az MQTT protokoll használatával az IoT Hub használatával.

| Nyelv | Protokoll paraméter |
| --- | --- |
| [NODE.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Egy eszközalkalmazás áttelepítés mqtt-ről AMQP-ről

Ha használja a [eszközoldali SDK-k][lnk-device-sdks], az ügyfél inicializálása a protokoll paraméter módosítani, ahogy korábban is hangsúlyoztuk MQTT, AMQP használatával való váltás kell.

Ha így tesz, ügyeljen arra, hogy ellenőrizze a következőket:

* AMQP számos feltételek által jelzett hibákat ad vissza, miközben MQTT lezárja a kapcsolatot. Ennek eredményeképpen a kivételkezelő logikai bizonyos változásokat igényelhet.
* MQTT nem támogatja a *elutasítása* műveletek fogadásakor [felhőből az eszközre irányuló üzenetek][lnk-messaging]. Ha a háttéralkalmazás válasz érkezik az eszközalkalmazás van szüksége, fontolja meg [közvetlen metódusok][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Közvetlenül az MQTT protokoll használatával

Ha egy eszköz nem tudja használni az eszközoldali SDK-k, továbbra is csatlakozhat a nyilvános eszköz végpontok 8883 porton az MQTT protokoll használatával. Az a **CONNECT** csomagot az eszközt kell használnia a következő értékeket:

* Az a **ClientId** mezőhöz, használja a **deviceId**.

* Az a **felhasználónév** mezőben `{iothubhostname}/{device_id}/api-version=2016-11-14`, ahol `{iothubhostname}` van a teljes CName az IoT hub.

    Például, ha a neve, az IoT hub **contoso.azure-devices.net** , és ha az eszköz nevét, **MyDevice01**, a teljes **felhasználónév** mezőnek tartalmaznia kell:

    `contoso.azure-devices.net/MyDevice01/api-version=2016-11-14`

* Az a **jelszó** mezőhöz, használja a SAS-jogkivonatát. A SAS-jogkivonat formátuma azonos a HTTPS és az AMQP protokoll:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > X.509 tanúsítvány alapú hitelesítést használ, ha SAS-token jelszavak nem szükségesek. További információkért lásd: [X.509-biztonság az Azure IoT hub beállítása][lnk-x509]

  SAS-tokeneket kapcsolatos további információkért lásd: az eszköz szakaszában [használata az IoT Hub biztonsági tokenek][lnk-sas-tokens].

  Tesztelésekor, használhatja a platformfüggetlen [Azure IoT-eszközkészlet bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) vagy a [Device Explorer] [ lnk-device-explorer] eszköz gyorsan létrehozhat egy SAS-jogkivonatát, másolja, és illessze be a saját kódját:

Az Azure IoT-eszközkészlet:

  1. Bontsa ki a **AZURE IOT HUB-eszközök** lapon, a Visual Studio Code bal alsó sarkában.
  2. Kattintson a jobb gombbal az eszközt, és válassza ki **készítése a SAS-Token eszköz**.
  3. Állítsa be **lejárati idő** nyomja le az "Enter" billentyűt.
  4. A SAS-jogkivonatát, és a vágólapra másolva.

A Device Explorer:

  1. Nyissa meg a **felügyeleti** lapján **Device Explorer**.
  2. Kattintson a **SAS-Token** (jobb felső).
  3. A **SASTokenForm**, válassza ki az eszközt a **DeviceID** legördülő menü. Állítsa be a **TTL**.
  4. Kattintson a **Generate** a token létrehozásához.

     A SAS-jogkivonat, amely akkor jön létre, az alábbi struktúrával rendelkeznek:

     `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

     Ez a token adatokként a részét a **jelszó** MQTT érdemesebb a mező:

     `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

MQTT csatlakoztatása és leválasztása a csomagok, az IoT Hub kiad egy eseményt a a **Működésfigyelés** csatorna. Ez az esemény rendelkezik, amelyek segítségével a kapcsolati hibák elhárításához további információt.

Az eszköz-alkalmazás megadhat egy **fog** jelenik meg az a **CONNECT** csomagot. Az eszköz alkalmazást kell használnia `devices/{device_id}/messages/events/{property_bag}` vagy `devices/{device_id}/messages/events/{property_bag}` , a **fog** témakör neve meghatározásához **fog** telemetriai üzenetnek számít továbbított üzenetek. Ebben az esetben, ha a hálózati kapcsolat megszakad, de egy **DISCONNECT** csomag korábban nem érkezett az eszközről, majd az IoT Hub küldi a **fog** megadott üzenetet a **CONNECT** csomagot a telemetria-csatornára. A telemetriai adatok csatorna lehet vagy az alapértelmezett **események** végpontot, illetve egy útválasztási IoT Hub által definiált egyéni végpont. Az üzenet a **iothub-MessageType** tulajdonság értéke az **fog** rendelve.

### <a name="tlsssl-configuration"></a>A TLS/SSL-konfigurációja

Használatához az MQTT protokoll közvetlenül, az ügyfél *kell* csatlakoznak a TLS/SSL-kapcsolaton keresztül. Kihagyhatja ezt a lépést tett kísérletek csatlakozási hibák miatt sikertelen.

Annak érdekében, hogy a TLS kapcsolatot létesíteni szükség lehet, töltse le, és a DigiCert Baltimore főtanúsítvány hivatkozunk. Ez a tanúsítvány, amely Azure használ a kapcsolat biztonságossá tétele érdekében. Ezt a tanúsítványt annak a [Azure-iot-sdk-c] [ lnk-sdk-c-certs] tárház. Ezekről a tanúsítványokról további információ található [Digicert a webhely][lnk-digicert-root-certs].

Példa bemutatja, hogyan megvalósításának Python verzióját használja a [Paho MQTT könyvtár] [ lnk-paho] az Eclipse alapítvány által a következőhöz hasonló lehet a következő.

Először telepítse a Paho könyvtárban, a parancssori környezetet:

```cmd/sh
pip install paho-mqtt
```

Ezt követően valósítja meg az ügyfél a Python-szkriptet. Cserélje le a helyőrzőket a következőképpen:

* `<local path to digicert.cer>` a DigiCert Baltimore főtanúsítvány tartalmazó helyi fájl elérési útja van. Ezt a fájlt másolja a tanúsítvány adatait is létrehozhat [certs.c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) c-hez készült Azure IoT SDK tartalmazzák a sorok `-----BEGIN CERTIFICATE-----` és `-----END CERTIFICATE-----`, távolítsa el a `"` elején és minden sor végére jelek és Távolítsa el a `\r\n` minden sor végén található karaktert.
* `<device id from device registry>` van egy eszközt az IoT hub hozzáadott azonosítója.
* `<generated SAS token>` a rendszer az eszközt ebben a cikkben korábban leírtak szerint létrehozott SAS-jogkivonatát.
* `<iot hub name>` az IoT hub nevére.

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

### <a name="sending-device-to-cloud-messages"></a>Eszköz a felhőbe irányuló üzenetek küldéséhez

Sikeres csatlakozás után egy eszköz küldhet üzeneteket az IoT Hub használatával `devices/{device_id}/messages/events/` vagy `devices/{device_id}/messages/events/{property_bag}` , egy **témakör neve**. A `{property_bag}` elem lehetővé teszi, hogy az eszköz további tulajdonságokkal rendelkező üzenetek küldése egy url-kódolású formátumot. Példa:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Ez `{property_bag}` elem használja, mint a lekérdezési karakterláncok a HTTPS protokoll azonos kódolást.

A következő az IoT Hub implementációspecifikus viselkedések listája:

* Az IoT Hub nem támogatja a QoS 2 üzeneteket. Ha egy eszköz alkalmazás tesz közzé egy üzenetet **QoS 2**, az IoT Hub bezárja a hálózati kapcsolatot.
* Az IoT Hub nem marad megőrzése üzeneteket. Ha egy eszköz egy üzenetet küld a **megőrzése** jelző értéke 1, az IoT Hub hozzáadja a **x-jóváhagyás – megőrzése** application tulajdonságot az üzenethez. Ebben az esetben helyett az megőrzése üzenet átlátni, az IoT Hub számára továbbítja azokat a háttér-alkalmazást.
* Az IoT Hub eszközönként egy aktív MQTT kapcsolat csak támogatja. Minden olyan új MQTT-kapcsolat nevében az ugyanazon Eszközazonosítót hatására az IoT Hub segítségével dobja el a létező kapcsolatot.

További információkért lásd: [üzenetkezelés – fejlesztői útmutató][lnk-messaging].

### <a name="receiving-cloud-to-device-messages"></a>Felhőből az eszközre irányuló üzenetek fogadása

Üzenetek fogadása az IoT hubról, hogy egy eszköz használatával ajánlatos `devices/{device_id}/messages/devicebound/#` , egy **témakör szűrő**. A többszintű helyettesítő `#` témakör szűrő csak a használják engedélyezése az eszköz számára a további tulajdonságok kapják meg a témakör nevét. Az IoT Hub nem engedélyezi a használatát a `#` vagy `?` altémakörei a szűréshez helyettesítő karaktereket. Mivel az IoT Hub nem egy általános célú pub-sub üzenetközvetítő, csak a támogatott a dokumentált témakörök nevei és témakörszűrők.

Az eszköz nem kap olyan üzeneteket az IoT hubról, amíg sikeresen előfizetett az eszközspecifikus végponthoz, képviseli a `devices/{device_id}/messages/devicebound/#` témakör szűrőt. Előfizetés létrehozása után az eszköz megkaphatná a felhőből az eszközre üzeneteinek hozzá az előfizetés az időpont után. Ha az eszköz kapcsolódik a **CleanSession** jelző értékre **0**, az előfizetés rendszer megőrzi a munkamenetek között. Ebben az esetben, amikor legközelebb az eszköz kapcsolódik a **CleanSession 0** bármely leválasztott állapotban neki küldött üzeneteket kap. Ha az eszköz **CleanSession** jelző értékre **1** , hogy nem kapja meg olyan üzeneteket az IoT hubról mindaddig, amíg az eszköz végpontja van feliratkozva.

Az IoT Hub továbbítja az üzeneteket az a **témakör neve** `devices/{device_id}/messages/devicebound/`, vagy `devices/{device_id}/messages/devicebound/{property_bag}` Ha üzenet tulajdonságai. `{property_bag}` üzenet tulajdonságai url-kódolású kulcs/érték párt tartalmaz. Csak alkalmazás és a felhasználó állítható rendszer tulajdonságai (például **üzenetazonosító** vagy **correlationId**) a tulajdonságcsomag szerepelnek. Rendszer tulajdonságnevek rendelkezik az előtag **$**, alkalmazástulajdonságok nincs előtaggal rendelkező eredeti tulajdonság nevét használja.

Amikor egy eszköz alkalmazást feliratkozik egy témakörhöz **QoS 2**, az IoT Hub maximális QoS level 1 a biztosít a **SUBACK** csomagot. Ezt követően az IoT Hub továbbítja az üzeneteket a QoS 1 eszközt.

### <a name="retrieving-a-device-twins-properties"></a>Az eszközök ikereszköze tulajdonságainak beolvasása

Első lépésként feliratkozik egy eszköz `$iothub/twin/res/#`, a művelet-válaszokat fogadni. Ezután egy üres üzenetet küld a témakör `$iothub/twin/GET/?$rid={request id}`, ki van töltve értékkel **kérelemazonosító**. A szolgáltatás majd küld egy válaszüzenetet a témakör az eszközön lévő ikereszköz adatokat tartalmazó `$iothub/twin/res/{status}/?$rid={request id}`, azonos **kérelemazonosító** a kérést.

Kérelem azonosítója bármilyen érvényes érték, egy üzenet tulajdonság értéke lehet megfelelően [az IoT Hub fejlesztői útmutató üzenetkezelési][lnk-messaging], és az egész számként érvényesítési állapota.

A válasz törzse tartalmazza az ikereszköz tulajdonságok szakaszában. Az alábbi kódrészlet például bemutatja a szervezet korlátozott identitás bejegyzés a "Tulajdonságok" tag számára:

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

A lehetséges állapotkódok a következők:

|status | Leírás |
| ----- | ----------- |
| 200 | Sikeres |
| 429 | Túl sok kérelem (szabályozott), megfelelően [szabályozása az IoT Hub][lnk-quotas] |
| 5** | Kiszolgálóhibák |

További információkért lásd: [Device twins fejlesztői útmutató][lnk-devguide-twin].

### <a name="update-device-twins-reported-properties"></a>Frissítés eszköz ikereszköz jelentett tulajdonságait

A következő szakasz ismerteti, hogyan egy eszközt az IoT Hub az ikereszköz jelentett tulajdonságainak frissítése. a:

1. Egy eszköz először elő kell a `$iothub/twin/res/#` témakörhöz, és a művelet válaszokat kaphatnak az IoT hubról.

1. Egy eszköz, amely tartalmazza az eszköz iker frissítése üzenetet küld a `$iothub/twin/PATCH/properties/reported/?$rid={request id}` témakör. Ez az üzenet tartalmaz egy **kérelemazonosító** értéket.

1. A szolgáltatás majd küld egy válaszüzenetet, amely tartalmazza az új ETag-érték a témakör a jelentett tulajdonságok gyűjtemény `$iothub/twin/res/{status}/?$rid={request id}`. A válaszüzenet ugyanazokkal a **kérelemazonosító** a kérést.

Üzenet a kéréstörzs JSON-dokumentumok, jelentett tulajdonságok új értékeket tartalmazó tartalmazza. A JSON-dokumentum minden tagjának frissíti, vagy adja hozzá a megfelelő tag az ikereszközök a dokumentumban. Egy tag beállítása `null`, a tag törlése a tartalmazó objektum. Példa:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

A lehetséges állapotkódok a következők:

|status | Leírás |
| ----- | ----------- |
| 200 | Sikeres |
| 400 | Hibás kérés. Helytelen formátumú JSON |
| 429 | Túl sok kérelem (szabályozott), megfelelően [szabályozása az IoT Hub][lnk-quotas] |
| 5** | Kiszolgálóhibák |

További információkért lásd: [Device twins fejlesztői útmutató][lnk-devguide-twin].

### <a name="receiving-desired-properties-update-notifications"></a>Fogadó kívánt tulajdonságait frissítési értesítések

Ha egy eszköz csatlakozik, az IoT Hub értesítéseket küld a témakör `$iothub/twin/PATCH/properties/desired/?$version={new version}`, amely tartalmazza a frissítés, a megoldás háttérrendszere végzi a tartalom. Példa:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null
}
```

Tulajdonság frissítései, mint `null` érték azt jelenti, hogy a JSON-objektum tag törlése folyamatban van.

> [!IMPORTANT]
> Az IoT Hub értesítéseket hoz létre, csak ha az eszköz van csatlakoztatva. Ügyeljen arra, hogy végrehajtja a [eszköz újrakapcsolódási folyamat] [ lnk-devguide-twin-reconnection] tartani a kívánt tulajdonságok, szinkronizálja az IoT Hub és az eszköz alkalmazás között.

További információkért lásd: [Device twins fejlesztői útmutató][lnk-devguide-twin].

### <a name="respond-to-a-direct-method"></a>Közvetlen metódusra válaszol

Először egy eszközhöz tartozik előfizetés a riasztásoktól `$iothub/methods/POST/#`. Az IoT Hub metódus kéréseket küld a témakör `$iothub/methods/POST/{method name}/?$rid={request id}`érvénytelen JSON és a egy üres törzse.

Válaszol, az eszköz egy érvényes JSON vagy törzse üres üzenetet küld a témakör `$iothub/methods/res/{status}/?$rid={request id}`. Ebben az üzenetben a **kérelemazonosító** meg kell egyeznie a kérelem üzenetben és **állapot** egész számnak kell lennie.

További információkért lásd: [közvetlen metódus fejlesztői útmutató][lnk-methods].

### <a name="additional-considerations"></a>Néhány fontos megjegyzés

Végső veszi figyelembe, mint ha testre kell szabnia az MQTT protokoll viselkedés a felhő oldalon tekintse át a [Azure IoT-protokollátjáró][lnk-azure-protocol-gateway]. Ezt a szoftvert, hogy a kapcsolatok közvetlenül az IoT Hub lehetővé teszi nagy teljesítményű egyéni protokoll-átjáró telepítése. Az Azure IoT-protokollátjáró lehetővé teszi az eszköz protokoll befogadására brownfield MQTT központi telepítések vagy más egyéni protokollok testreszabásához. Ezt a megközelítést igényel, azonban futtassa, és a egy egyéni protokoll-átjáró működik.

## <a name="next-steps"></a>További lépések

Az MQTT protokoll kapcsolatos további információkért tekintse meg a [MQTT dokumentáció][lnk-mqtt-docs].

Az IoT Hub üzembe helyezés tervezésével kapcsolatos további tudnivalókért lásd:

* [Az Azure Certified for IoT eszközkatalógus][lnk-devices]
* [További protokollok támogatása][lnk-protocols]
* [Az Event Hubs összehasonlítása][lnk-compare]
* [Méretezés, magas rendelkezésre ÁLLÁS és Vészhelyreállítás][lnk-scaling]

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Az IoT Hub fejlesztői útmutató][lnk-devguide]
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples
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

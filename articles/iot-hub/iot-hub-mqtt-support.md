---
title: Az Azure IoT Hub MQTT-támogatás ismertetése | Microsoft Docs
description: Fejlesztői útmutató – IoT Hub eszközre irányuló végponthoz csatlakozó eszközök támogatása a MQTT protokoll használatával. Az Azure IoT Device SDK-k beépített MQTT-támogatásával kapcsolatos információkat tartalmaz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 150927ac05cba058d1d152ce568d7a462043d076
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76937760"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Kommunikáció az IoT hub használatával a MQTT protokollal

IoT Hub lehetővé teszi, hogy az eszközök a következő használatával kommunikáljanak a IoT Hub-eszköz végpontjait:

* [MQTT v 3.1.1](https://mqtt.org/) a 8883-es porton
* MQTT v 3.1.1 a 443-es porton keresztüli WebSocket-en keresztül.

A IoT Hub nem teljes funkcionalitású MQTT-közvetítő, és nem támogatja az MQTT v 3.1.1 szabványban megadott összes viselkedést. Ez a cikk azt ismerteti, hogy az eszközök hogyan használhatják a támogatott MQTT-viselkedéseket IoT Hubekkel való kommunikációhoz.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

A IoT Hubokkal való kommunikációt a TLS/SSL protokollal kell biztosítani. Ezért a IoT Hub nem támogatja a nem biztonságos kapcsolatokat a 1883-as porton keresztül.

## <a name="connecting-to-iot-hub"></a>Csatlakozás a IoT Hubhoz

Az eszközök a MQTT protokoll használatával csatlakozhatnak egy IoT hubhoz az alábbi lehetőségek bármelyikével.

* Az [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)-k könyvtárai.
* A MQTT protokoll közvetlenül.

## <a name="using-the-device-sdks"></a>Az eszköz SDK-k használata

A MQTT protokollt támogató [eszközök SDK](https://github.com/Azure/azure-iot-sdks) -k Java, Node. js, C, C#és Python rendszerekhez érhetők el. Az eszköz SDK-k a standard IoT Hub kapcsolati karakterláncot használják az IoT hub-kapcsolat létesítéséhez. Az MQTT protokoll használatához a **MQTT**értékre kell állítani az ügyfél protokoll paraméterét. Alapértelmezés szerint az eszköz SDK-k egy, a **CleanSession** jelzővel rendelkező IoT hubhoz csatlakoznak **, és az** IoT hub használatával történő üzenetváltáshoz a **QoS 1** eszközt használják.

Ha egy eszköz egy IoT-hubhoz csatlakozik, az eszköz SDK-k olyan metódusokat biztosítanak, amelyek lehetővé teszik, hogy az eszköz üzeneteket cseréljen az IoT hub-ra.

Az alábbi táblázat az egyes támogatott nyelvekre mutató hivatkozásokat tartalmaz, és meghatározza a paramétert, amellyel kapcsolatot létesíthet IoT Hub a MQTT protokoll használatával.

| Nyelv | Protokoll paraméter |
| --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) |Azure-IOT-Device-mqtt |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |IotHubClientProtocol. MQTT |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) |MQTT_Protocol |
| [C#](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) |TransportType. Mqtt |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) |Alapértelmezés szerint mindig támogatja a MQTT |

### <a name="default-keep-alive-timeout"></a>Alapértelmezett életben tartási időkorlát 

Annak biztosítása érdekében, hogy az ügyfél/IoT Hub-kapcsolatok életben maradjanak, a szolgáltatás és az ügyfél is rendszeresen küld egy *életben tartási* pingelést. A IoT SDK-t használó ügyfél a következő táblázatban meghatározott időközönként elküld egy életben tartást:

|Nyelv  |Alapértelmezett életben tartási időköz  |Konfigurálható  |
|---------|---------|---------|
|Node.js     |   180 másodperc      |     Nem    |
|Java     |    230 másodperc     |     Nem    |
|C#     | 240 másodperc |  [Igen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 másodperc |  [Igen](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python (v2)   | 60 másodperc |  Nem   |

A következő [MQTT spec](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081), IoT hub Keep-Alive ping intervalluma 1,5 alkalommal az ügyfél életben tartási értéke. A IoT Hub azonban korlátozza a kiszolgálóoldali időtúllépési korlátot 29,45 percre (1767 másodpercre), mert az összes Azure-szolgáltatás az Azure Load Balancer TCP üresjárati időkorlátjának (29,45 perc) van kötve. 

Egy Java SDK-t használó eszköz például a Keep-Alive ping üzenetet küldi, majd elveszíti a hálózati kapcsolatot. 230 másodperccel később az eszköz elmulasztja a Keep-Alive pingelést, mert offline állapotban van. IoT Hub azonban nem kapcsolja le azonnal a kapcsolatot – megvárja egy másik `(230 * 1.5) - 230 = 115` másodpercet, mielőtt leválasztja az eszközt a [404104](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)-as hiba miatti DeviceConnectionClosedRemotely. 

A maximálisan beállítható ügyfél életben tartási értéke `1767 / 1.5 = 1177` másodperc. Minden forgalom alaphelyzetbe állítja a Keep-Alive-t. Egy sikeres SAS-jogkivonat frissítése például alaphelyzetbe állítja a Keep-Alive állapotot.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Eszköz alkalmazás migrálása a AMQP-ből a MQTT-be

Ha az [eszköz SDK](https://github.com/Azure/azure-iot-sdks)-kat használja, a AMQP-ről MQTT-re való áttéréshez a korábban megadott módon módosítania kell a protokoll paramétert az ügyfél inicializálása során.

Ha ezt teszi, ellenőrizze a következő elemeket:

* A AMQP számos feltételnél hibákat ad vissza, míg a MQTT megszakítja a kapcsolódást. Ennek eredményeképpen előfordulhat, hogy a kivételek kezelésére szolgáló logikának valamilyen módosításra van szüksége.

* A MQTT nem támogatja az *elutasítási* műveleteket a [felhőből az eszközre irányuló üzenetek](iot-hub-devguide-messaging.md)fogadásakor. Ha a háttér-alkalmazásnak választ kell kapnia az eszköz alkalmazástól, érdemes lehet [közvetlen módszereket](iot-hub-devguide-direct-methods.md)használni.

* A AMQP nem támogatott a Python SDK-ban

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>A MQTT protokoll használata közvetlenül (eszközként)

Ha egy eszköz nem tudja használni az eszköz SDK-kat, akkor továbbra is csatlakozhat a nyilvános eszközök végpontjaihoz a 8883-es porton az MQTT protokoll használatával. A **csatlakoztatási** csomagban az eszköznek a következő értékeket kell használnia:

* A **ClientId** mezőben használja a **deviceId**-t.

* A **Felhasználónév** mezőben használja a `{iothubhostname}/{device_id}/?api-version=2018-06-30`, ahol a `{iothubhostname}` az IoT hub teljes CNAME-je.

    Ha például az IoT hub neve **contoso.Azure-Devices.net** , és ha az eszköz neve **MyDevice01**, a teljes **Felhasználónév** mezőben a következőket kell tartalmaznia:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* A **Password (jelszó** ) mezőben használjon sas-tokent. Az SAS-token formátuma megegyezik a HTTPS és a AMQP protokollok esetében is:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Ha X. 509 tanúsítványalapú hitelesítést használ, az SAS-jogkivonat jelszavai nem szükségesek. További információkért lásd: [az X. 509 Biztonság beállítása az Azure-IoT hub](iot-hub-security-x509-get-started.md) , és kövesse az [alábbi](#tlsssl-configuration)kód utasításait.

  Az SAS-tokenek létrehozásával kapcsolatos további információkért tekintse meg a [IoT hub biztonsági jogkivonatok használata](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)című témakör eszközét.

  A tesztelés során a Visual Studio Code-hoz készült platformfüggetlen [Azure IoT-eszközökkel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) vagy a [Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/tools/DeviceExplorer) eszközzel gyorsan létrehozhat egy sas-tokent, amely a saját kódjába másolható és beilleszthető:

### <a name="for-azure-iot-tools"></a>Azure IoT-eszközökhöz

1. Bontsa ki az **Azure IOT hub-eszközök** fület a Visual Studio Code bal alsó sarkában.
  
2. Kattintson a jobb gombbal az eszközre, és válassza a **sas-jogkivonat előállítása az eszközhöz**lehetőséget.
  
3. Adja meg a **lejárati időt** , és nyomja le az ENTER billentyűt.
  
4. A rendszer létrehoz egy SAS-tokent, és átmásolja a vágólapra.

### <a name="for-device-explorer"></a>Device Explorer

1. Lépjen a Device Explorer **felügyelet** lapjára.

2. Kattintson a **sas-jogkivonat** (jobb felső) elemre.

3. A **SASTokenForm**lapon válassza ki az eszközt a **DeviceID** legördülő menüből. Állítsa be az **élettartamot**.

4. A jogkivonat létrehozásához kattintson a **Létrehozás** gombra.

   A generált SAS-jogkivonat a következő szerkezettel rendelkezik:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   Ennek a tokennek a része a MQTT használatával való kapcsolódáshoz használt **jelszó** mező:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

A MQTT csatlakoztatásához és a csomagok leválasztásához IoT Hub a **műveletek figyelése** csatornán eseményt ad ki. Ez az esemény további információkat tartalmaz, amelyek segíthetnek a kapcsolódási problémák elhárításában.

Az alkalmazás megadhat **egy üzenetet** a **csatlakoztatási** csomagban. Az alkalmazásnak `devices/{device_id}/messages/events/` vagy `devices/{device_id}/messages/events/{property_bag}`t kell használnia, mivel a (z) definiálni **kívánt témakör neve** telemetria üzenetként **fog** továbbítani. Ebben az esetben, ha a hálózati kapcsolat be van zárva, de a **leválasztási** csomag korábban nem érkezett meg az eszközről, a IoT hub elküldi a telemetria csatornához a **kapcsolódási** csomagban **megadott üzenetet.** Az telemetria csatorna az alapértelmezett **események** végpontja vagy IoT hub útválasztás által definiált egyéni végpont lehet. Az üzenet a **iothub-MessageType** tulajdonsággal rendelkezik, amelynek a értéke hozzá **lesz** rendelve.

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>C kód példája az Azure IoT C SDK nélküli MQTT használatával
Ebben a [tárházban](https://github.com/Azure-Samples/IoTMQTTSample)néhány C/C++ bemutató projektet talál, amelyek bemutatják, hogyan küldhet telemetria-üzeneteket az IoT hub használatával az Azure IoT C SDK használata nélkül. 

Ezek a minták az Eclipse Mosquitto könyvtár használatával küldenek üzenetet az IoT hub-ban megvalósított MQTT-közvetítőnek.

A tárház tartalma:

**Windows esetén:**

• TelemetryMQTTWin32: olyan kódot tartalmaz, amely telemetria üzenetet küld egy Azure IoT hub-ra, amely egy Windows rendszerű gépen lett létrehozva és futtatva.

• SubscribeMQTTWin32: olyan kódot tartalmaz, amely egy Windows rendszerű gépen lévő adott IoT hub eseményeire fizet elő.

• DeviceTwinMQTTWin32: olyan kódot tartalmaz, amely lekérdezi és Előfizeti a Windows rendszerű számítógépeken lévő Azure IoT hub eszközön található eszközök kettős eseményeit.

• PnPMQTTWin32: olyan kódot tartalmaz, amely egy telemetria-üzenetet küld a IoT beépülő & modulnak, amely a Windows rendszerű gépeken létrehozott és futtatott Azure-beli IoT-eszközökön is megtekinthető. További információ a IoT Plug & [játékról](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**Linux esetén:**

• MQTTLinux: tartalmazza a kód és a Build szkriptet a Linuxon való futtatáshoz (WSL, Ubuntu és Raspbian).

• LinuxConsoleVS2019: ugyanazt a kódot tartalmazza, de egy VS2019-projekt WSL (Windows Linux alrendszer). Ez a projekt lehetővé teszi a Linux lépésről lépésre futó kód hibakeresését a Visual studióból.

**Mosquito_pub esetén:**

• Ez a mappa két mintát tartalmaz, amelyeket a Mosquitto.org által biztosított mosquitto_pub Utility eszközzel használ.

Mosquitto_sendmessage: egyszerű szöveges üzenet küldése egy eszközként működő Azure IoT hub-nak.

Mosquitto_subscribe: az Azure IoT hub-ban előforduló események megtekintéséhez.


## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>A MQTT protokoll használata közvetlenül (modulként)

Ha a modul-identitás használatával csatlakozik a MQTT-hez IoT Hubhoz, az eszközhöz hasonlóan (lásd [fentebb](#using-the-mqtt-protocol-directly-as-a-device)), de a következőket kell használnia:

* Állítsa be az ügyfél-azonosítót `{device_id}/{module_id}`ra.

* Ha felhasználónévvel és jelszóval végzi a hitelesítést, állítsa be `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` a felhasználónevet, és használja a modul identitásához társított SAS-tokent jelszóként.

* A telemetria közzétételéhez használja a `devices/{device_id}/modules/{module_id}/messages/events/` témakört.

* A következő módon használhatja a `devices/{device_id}/modules/{module_id}/messages/events/`.

* A Twin GET és a PATCH témakörök azonosak a modulok és az eszközök esetében.

* A Twin status témakör azonos a modulok és az eszközök esetében.

## <a name="tlsssl-configuration"></a>TLS/SSL-konfiguráció

Ahhoz, hogy az ügyfél közvetlenül használhassa a MQTT protokollt, a TLS/SSL protokollon keresztül *kell* csatlakoznia. Csatlakozási hibák esetén a lépés kihagyása sikertelen.

A TLS-kapcsolat létesítéséhez előfordulhat, hogy le kell töltenie és hivatkoznia kell a DigiCert Baltimore főtanúsítványára. Ez a tanúsítvány az Azure által a kapcsolatok biztonságossá tételéhez használt tanúsítvány. Ezt a tanúsítványt az [Azure-IOT-SDK-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) tárházban találja. A tanúsítványokról további információt a [Digicert webhelyén](https://www.digicert.com/digicert-root-certificates.htm)találhat.

Egy példa arra, hogyan valósítható meg ez a [PAHO MQTT Library](https://pypi.python.org/pypi/paho-mqtt) Python-verziójának használatával az Eclipse Foundation által az alábbihoz hasonló módon.

Először telepítse a PAHO könyvtárat a parancssori környezetből:

```cmd/sh
pip install paho-mqtt
```

Ezután implementálja az ügyfelet egy Python-parancsfájlban. Cserélje le a helyőrzőket a következőképpen:

* `<local path to digicert.cer>` egy helyi fájl elérési útja, amely a DigiCert Baltimore főtanúsítványát tartalmazza. Ezt a fájlt úgy is létrehozhatja, hogy átmásolja a tanúsítvány adatait a (z) C Azure IoT SDK [-ban.](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) a sorok közé tartozik a `-----BEGIN CERTIFICATE-----` és a `-----END CERTIFICATE-----`, az összes sor elején és végén távolítsa el a `"` jeleket, és távolítsa el a `\r\n` karaktereket minden sor végén.

* `<device id from device registry>` az IoT hub-hoz hozzáadott eszköz azonosítója.

* a `<generated SAS token>` az eszközhöz a cikkben korábban leírt módon létrehozott SAS-jogkivonat.

* `<iot hub name>` az IoT hub nevét.

```python
from paho.mqtt import client as mqtt
import ssl

path_to_root_cert = "<local path to digicert.cer file>"
device_id = "<device id from device registry>"
sas_token = "<generated SAS token>"
iot_hub_name = "<iot hub name>"


def on_connect(client, userdata, flags, rc):
    print("Device connected with result code: " + str(rc))


def on_disconnect(client, userdata, rc):
    print("Device disconnected with result code: " + str(rc))


def on_publish(client, userdata, mid):
    print("Device sent message")


client = mqtt.Client(client_id=device_id, protocol=mqtt.MQTTv311)

client.on_connect = on_connect
client.on_disconnect = on_disconnect
client.on_publish = on_publish

client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=sas_token)

client.tls_set(ca_certs=path_to_root_cert, certfile=None, keyfile=None,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)
client.tls_insecure_set(False)

client.connect(iot_hub_name+".azure-devices.net", port=8883)

client.publish("devices/" + device_id + "/messages/events/", "{id=123}", qos=1)
client.loop_forever()
```

Az eszköz tanúsítványának használatával történő hitelesítéshez frissítse a fenti kódrészletet a következő módosításokkal (lásd: [X. 509 hitelesítésszolgáltatói tanúsítvány beszerzése](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) a tanúsítványalapú hitelesítés előkészítéséhez):

```python
# Create the client as before
# ...

# Set the username but not the password on your client
client.username_pw_set(username=iot_hub_name+".azure-devices.net/" +
                       device_id + "/?api-version=2018-06-30", password=None)

# Set the certificate and key paths on your client
cert_file = "<local path to your certificate file>"
key_file = "<local path to your device key file>"
client.tls_set(ca_certs=path_to_root_cert, certfile=cert_file, keyfile=key_file,
               cert_reqs=ssl.CERT_REQUIRED, tls_version=ssl.PROTOCOL_TLSv1_2, ciphers=None)

# Connect as before
client.connect(iot_hub_name+".azure-devices.net", port=8883)
```

## <a name="sending-device-to-cloud-messages"></a>Eszközről a felhőbe irányuló üzenetek küldése

A sikeres kapcsolódást követően az eszközök `devices/{device_id}/messages/events/` vagy `devices/{device_id}/messages/events/{property_bag}` használatával küldhetnek üzeneteket IoT Hub a **témakör neveként**. A `{property_bag}` elem lehetővé teszi, hogy az eszköz további tulajdonságokkal rendelkező üzeneteket küldjön URL-kódolású formátumban. Példa:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Ez a `{property_bag}` elem ugyanazt a kódolást használja, mint a lekérdezési karakterláncok a HTTPS protokollban.

Az alábbi lista a IoT Hub implementáció-specifikus viselkedéseit sorolja fel:

* A IoT Hub nem támogatja a QoS 2 üzeneteit. Ha egy eszköz egy, a **QoS 2**eszközzel közzétett üzenetet tesz közzé, IoT hub zárja be a hálózati kapcsolatokat.

* A IoT Hub nem tartja fenn az üzenetek megőrzését. Ha egy eszköz a **megőrzési** jelzővel ellátott üzenetet küld az 1 értékre, IoT hub hozzáadja az alkalmazáshoz az **x-opt-retain** Application tulajdonságot az üzenethez. Ebben az esetben a megőrzött üzenet megőrzése helyett a IoT Hub átadja a háttérbeli alkalmazásnak.

* A IoT Hub eszközön csak egy aktív MQTT-kapcsolatok támogatottak. Az azonos eszköz AZONOSÍTÓjának nevében az új MQTT-kapcsolatok IoT Hub a meglévő kapcsolatok eldobásához.

További információ: [üzenetküldési fejlesztői útmutató](iot-hub-devguide-messaging.md).

## <a name="receiving-cloud-to-device-messages"></a>Felhőből az eszközre irányuló üzenetek fogadása

Az IoT Hub érkező üzenetek fogadásához az eszköznek a `devices/{device_id}/messages/devicebound/#` használatával kell előfizetnie a **témakör-szűrőként**. A témakör szűrő többszintű helyettesítő karakteres `#` csak arra szolgál, hogy az eszköz további tulajdonságokat kapjon a témakör nevében. A IoT Hub nem engedélyezi a `#` vagy a `?` helyettesítő karakter használatát az altémakörök szűréséhez. Mivel IoT Hub nem egy általános célú pub-sub Messaging Broker, csak a dokumentált témakörök nevét és a témakör szűrőit támogatja.

Az eszköz nem kap semmilyen üzenetet a IoT Hubtól, amíg sikeresen előfizetett az eszközre vonatkozó végpontra, amelyet az `devices/{device_id}/messages/devicebound/#` témakör szűrője képvisel. Az előfizetés létrejötte után az eszköz megkapja a felhőből az eszközre irányuló üzeneteket, amelyeket az előfizetés időpontja után küldtek el. Ha az eszköz a **CleanSession** jelzővel csatlakozik **0-ra**, az előfizetés a különböző munkamenetek között marad meg. Ebben az esetben a következő alkalommal, amikor az eszköz a **CleanSession** -hez csatlakozik, a kapcsolat megszakadása közben minden, a számára elküldött függő üzenetet fogad. Ha az eszköz a **CleanSession** jelzőt **1-re** állítja, akkor a IoT hub addig nem kap üzenetet, amíg előfizet az eszköz-végpontra.

A IoT Hub üzeneteket küld a **témakör neve** `devices/{device_id}/messages/devicebound/`vagy `devices/{device_id}/messages/devicebound/{property_bag}`, ha vannak üzenet tulajdonságai. a `{property_bag}` URL-kódolású kulcs/érték párokat tartalmaz az üzenet tulajdonságainál. Csak az alkalmazás tulajdonságai és a felhasználó által beállítható Rendszertulajdonságok (például **messageId** vagy **correlationId**) szerepelnek a tulajdonság táskájában. A Rendszertulajdonságok nevei **$** előtaggal rendelkeznek, az alkalmazás tulajdonságai az eredeti tulajdonságnév előtag nélküli nevet használnak.

Ha egy eszköz egy, a **QoS 2-es**verzióra való előfizetést tartalmaz, a IoT hub maximális QoS-szintet biztosít a **SUBACK** -csomagban. Ezután a IoT Hub a QoS 1 használatával továbbítja az üzeneteket az eszköznek.

## <a name="retrieving-a-device-twins-properties"></a>Az eszközök Twin tulajdonságainak beolvasása

Először egy eszköz előfizet a `$iothub/twin/res/#`ra, hogy megkapja a művelet válaszait. Ezt követően üres üzenetet küld a `$iothub/twin/GET/?$rid={request id}`témakörnek, amely a **kérelem azonosítójának**feltöltésére szolgál. A szolgáltatás ezután elküld egy válaszüzenetet, amely tartalmazza az eszköz Twin-adatokat a (z) `$iothub/twin/res/{status}/?$rid={request id}`témakörben, a kérelem **azonosítójának** használatával.

A kérelem azonosítója lehet bármely érvényes érték az üzenet tulajdonságának értékeként, a [IoT hub üzenetkezelési fejlesztői útmutatója](iot-hub-devguide-messaging.md)szerint, az állapot pedig egész számként lesz érvényesítve.

A válasz törzse a Twin eszköz tulajdonságok szakaszát tartalmazza, ahogy az a következő példában látható:

```json
{
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
```

A lehetséges állapotkódok a következők:

|Állapot | Leírás |
| ----- | ----------- |
| 204 | Sikeres (a rendszer nem ad vissza tartalmat) |
| 429 | Túl sok kérés (szabályozott) a [IoT hub szabályozása](iot-hub-devguide-quotas-throttling.md) szerint |
| 5 * * | Kiszolgálói hibák |

További információ: [Device ikrek fejlesztői útmutatója](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>A Device Twin szolgáltatás jelentett tulajdonságainak frissítése

A jelentett tulajdonságok frissítéséhez az eszköz egy, a kijelölt MQTT-témakörben közzétett kiadványon keresztül bocsát ki IoT Hub kérelmet. A kérelem feldolgozása után IoT Hub válaszol a frissítési művelet sikerességi vagy meghibásodási állapotára egy kiadványon keresztül egy másik témakörbe. Ez a témakör előfizethet az eszközön, hogy értesítést kapjon a kettős frissítési kérelem eredményéről. Az ilyen típusú kérelmek/válaszok interakciójának a MQTT-ben való megvalósításához kihasználjuk az eszköz által kezdetben a frissítési kérelemben megadott kérelem-azonosító (`$rid`) fogalmát. Ez a kérelem-azonosító a IoT Hub válaszában is szerepel, hogy az eszköz korrelálja az adott korábbi kérelemre adott választ.

A következő szakasz azt ismerteti, hogyan frissíti az eszköz a jelentett tulajdonságokat az eszköz két IoT Hubjában:

1. Az eszköznek először elő kell fizetnie a `$iothub/twin/res/#` témakörre, hogy megkapja a műveletnek a IoT Hubtól érkező válaszait.

2. Az eszköz egy üzenetet küld, amely tartalmazza az eszköz kettős frissítését az `$iothub/twin/PATCH/properties/reported/?$rid={request id}` témakörhöz. Ez az üzenet tartalmazza a **kérelem azonosítójának** értékét.

3. A szolgáltatás ezután egy válaszüzenetet küld, amely tartalmazza a jelentett tulajdonságok gyűjtemény új ETag értékét a következő témakörben: `$iothub/twin/res/{status}/?$rid={request id}`. Ez a válaszüzenet ugyanazt a **kérés-azonosítót** használja, mint a kérelem.

A kérelem üzenet törzse tartalmaz egy JSON-dokumentumot, amely a jelentett tulajdonságok új értékeit tartalmazza. A JSON-dokumentum minden tagja frissíti vagy hozzáadja a megfelelő tagot az eszköz Twin dokumentumához. Egy `null`re beállított tag törli a tagot a tartalmazó objektumból. Példa:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

A lehetséges állapotkódok a következők:

|Állapot | Leírás |
| ----- | ----------- |
| 200 | Sikeres |
| 400 | Hibás kérelem. Helytelen formátumú JSON |
| 429 | Túl sok kérés (szabályozott) a [IoT hub szabályozása](iot-hub-devguide-quotas-throttling.md) szerint |
| 5 * * | Kiszolgálói hibák |

Az alábbi Python-kódrészlet a következőt mutatja be: MQTT (PAHO MQTT-ügyfél használata)

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

A két jelentett tulajdonság a fenti frissítési művelet sikeressége után a IoT Hub közzétételére vonatkozó üzenet a következő témakörben fog megjelenni: `$iothub/twin/res/204/?$rid=1&$version=6`, ahol a `204` a sikert jelző állapotkód, `$rid=1` az eszköz által a kódban megadott kérelem-AZONOSÍTÓnak felel meg, és `$version` a frissítés után a jelentett tulajdonságok szakaszának verziószámára vonatkozik.

További információ: [Device ikrek fejlesztői útmutatója](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>A kívánt tulajdonságok frissítési értesítéseinek fogadása

Ha egy eszköz csatlakoztatva van, IoT Hub értesítést küld a témakör `$iothub/twin/PATCH/properties/desired/?$version={new version}`, amely tartalmazza a megoldás hátterében végrehajtott frissítés tartalmát. Példa:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

A tulajdonságok frissítéseihez hasonlóan `null` érték azt jelenti, hogy a JSON-objektum tagja törölve van. Azt is vegye figyelembe, hogy `$version` a Twin tulajdonság kívánt tulajdonságok szakaszának új verzióját jelzi.

> [!IMPORTANT]
> IoT Hub csak akkor hoz létre módosítási értesítéseket, ha az eszközök csatlakoztatva vannak. Győződjön meg arról, hogy az [eszköz újrakapcsolódási folyamata](iot-hub-devguide-device-twins.md#device-reconnection-flow) megtartja a kívánt tulajdonságokat a IoT hub és az eszköz alkalmazás között szinkronizálva.

További információ: [Device ikrek fejlesztői útmutatója](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Válasz közvetlen metódusra

Első lépésként az eszköznek `$iothub/methods/POST/#`ra kell előfizetnie. IoT Hub metódus-kérelmeket küld a témakörnek `$iothub/methods/POST/{method name}/?$rid={request id}`, amely érvényes JSON-vagy üres törzstel rendelkezik.

A válaszadáshoz az eszköz egy érvényes JSON-vagy üres törzsű üzenetet küld a `$iothub/methods/res/{status}/?$rid={request id}`témakörnek. Ebben az üzenetben a **kérelem azonosítójának** egyeznie kell a kérelem üzenetében szereplővel, és az **állapotnak** egész számnak kell lennie.

További információ: a [közvetlen módszer fejlesztői útmutatója](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

Végső megfontolásként, ha testre kell szabnia a MQTT protokoll viselkedését a felhős oldalon, tekintse át az [Azure IoT Protocol Gatewayt](iot-hub-protocol-gateway.md). Ez a szoftver lehetővé teszi egy olyan nagy teljesítményű egyéni protokoll-átjáró üzembe helyezését, amely közvetlenül a IoT Hub. Az Azure IoT Protocol Gateway lehetővé teszi, hogy testreszabja az eszköz protokollját, hogy az rozsdaövezetek rehabilitálása MQTT-telepítések vagy más egyéni protokollok is megfeleljenek. Ez a megközelítés azonban megköveteli, hogy egy egyéni protokoll-átjárót futtasson és működtessen.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az MQTT protokollról, tekintse meg a [MQTT dokumentációját](https://mqtt.org/documentation).

Ha többet szeretne megtudni a IoT Hub központi telepítésének megtervezéséről, olvassa el a következőt:

* [Azure Certified for IoT eszközkatalógus](https://catalog.azureiotsolutions.com/)
* [További protokollok támogatása](iot-hub-protocol-gateway.md)
* [Összehasonlítás Event Hubs](iot-hub-compare-event-hubs.md)
* [Skálázás, HA és DR](iot-hub-scaling.md)

A IoT Hub képességeinek további megismeréséhez lásd:

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [AI üzembe helyezése az Edge-eszközökön Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

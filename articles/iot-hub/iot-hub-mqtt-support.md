---
title: Az Azure IoT Hub MQTT támogatásának ismertetése | Microsoft dokumentumok
description: Fejlesztői útmutató – az MQTT protokoll használatával IoT Hub-eszközfelé néző végponthoz csatlakozó eszközök támogatása. Az Azure IoT-eszköz SDK-k beépített MQTT-támogatásával kapcsolatos információkat tartalmaz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: robinsh
ms.openlocfilehash: 9ccfaa57b8e8fdea325bed908ffe8815b09d0d15
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257793"
---
# <a name="communicate-with-your-iot-hub-using-the-mqtt-protocol"></a>Kommunikáció az IoT hubbal az MQTT protokoll használatával

Az IoT Hub lehetővé teszi, hogy az eszközök kommunikáljanak az IoT Hub-eszköz végpontjaival a következők használatával:

* [MQTT v3.1.1](https://mqtt.org/) a 8883-as porton
* MQTT v3.1.1 a 443-as porton lévő WebSocket felett.

Az IoT Hub nem teljes szolgáltatásokat nyújtó MQTT-közvetítő, és nem támogatja az MQTT 3.1.1-es standard verziójában megadott összes viselkedést. Ez a cikk azt ismerteti, hogy az eszközök hogyan használhatják a támogatott MQTT-viselkedéseket az IoT Hubbal való kommunikációhoz.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az IoT Hubbal való összes eszközkommunikációt TLS/SSL használatával kell biztosítani. Ezért az IoT Hub nem támogatja a nem biztonságos kapcsolatokat az 1883-as porton keresztül.

## <a name="connecting-to-iot-hub"></a>Csatlakozás az IoT Hubhoz

Egy eszköz használhatja az MQTT protokollt, hogy csatlakozzon egy IoT hub hoz az alábbi lehetőségek bármelyikével.

* Az [Azure IoT SDK-k könyvtárai.](https://github.com/Azure/azure-iot-sdks)
* Közvetlenül az MQTT protokoll.

Az MQTT port (8883) számos vállalati és oktatási hálózati környezetben le van tiltva. Ha nem tudja megnyitni a 8883-as portot a tűzfalon, javasoljuk, hogy az MQTT-t használja a websocketeken keresztül. Az MQTT websocketeken keresztül a 443-as porton keresztül kommunikál, amely szinte mindig nyitva van hálózati környezetben. Az MQTT és az MQTT websocketek protokollokon való megadásáról az Azure IoT SDK-k használata kor című témakörben olvashat: [Az eszköz SDK-k használata.](#using-the-device-sdks)

## <a name="using-the-device-sdks"></a>A készülék SDK-k használata

Az MQTT protokollt támogató [eszközSDK-k](https://github.com/Azure/azure-iot-sdks) Java, Node.js, C, C#és Python esetén érhetők el. Az eszköz SDK-k a szabványos IoT Hub-kapcsolati karakterlánc ot használ az IoT hubhoz való kapcsolat létrehozásához. Az MQTT protokoll használatához az ügyfélprotokoll paraméterét **MQTT**-re kell állítani. Az MQTT paramétert az ügyfélprotokoll-paraméterben websocketeken keresztül is megadhatja. Alapértelmezés szerint az eszköz SDK-k csatlakozni egy IoT Hub a **CleanSession** jelző **beállítása 0** és **qoS 1** üzenetváltás az IoT hub.

Ha egy eszköz egy IoT hubhoz csatlakozik, az eszköz SDK-k olyan módszereket biztosítanak, amelyek lehetővé teszik az eszköz számára, hogy üzeneteket cseréljenek egy IoT hubbal.

Az alábbi táblázat az egyes támogatott nyelvek kódmintáira mutató hivatkozásokat tartalmaz, és megadja az IoT Hubhoz az MQTT vagy az MQTT websocketek protokollon keresztül idomult kapcsolat létrehozásához használandó paramétert.

| Nyelv | MQTT protokoll paraméter | MQTT websocketek protokollparaméterén keresztül
| --- | --- | --- |
| [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/master/device/samples/simple_sample_device.js) | azúr-iot-eszköz-mqtt. Mqtt között | azúr-iot-eszköz-mqtt. MqttWs |
| [Java](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/sdk/iot/SendReceive.java) |[IotHubClientProtocol](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol?view=azure-java-stable). MQTT | IotHubClientProtocol.MQTT_WS |
| [C](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm) | [MQTT_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-h/mqtt-protocol) | [MQTT_WebSocket_Protocol](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothubtransportmqtt-websockets-h/mqtt-websocket-protocol) |
| [C #](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples) | [TransportType (TransportType ) (TransportType) (](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.transporttype?view=azure-dotnet) Mqtt között | A TransportType.Mqtt az MQTT-re esik vissza a webszoftvercsatornákon keresztül, ha az MQTT sikertelen. Ha csak webszoftvercsatornákon szeretné megadni az MQTT értéket, használja a TransportType.Mqtt_WebSocket_Only |
| [Python](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | Alapértelmezés szerint támogatja az MQTT-t | Hozzáadás `websockets=True` a híváshoz az ügyfél létrehozásához |

A következő töredék bemutatja, hogyan adható meg az MQTT websocketek protokollon keresztül az Azure IoT Node.js SDK használataesetén:

```javascript
var Client = require('azure-iot-device').Client;
var Protocol = require('azure-iot-device-mqtt').MqttWs;
var client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

A következő töredék bemutatja, hogyan adható meg az MQTT websocketek protokollon keresztül az Azure IoT Python SDK használatakor:

```python
from azure.iot.device.aio import IoTHubDeviceClient
device_client = IoTHubDeviceClient.create_from_connection_string(deviceConnectionString, websockets=True)
```

### <a name="default-keep-alive-timeout"></a>Alapértelmezett életben tartás időtúltartománya

Annak érdekében, hogy az ügyfél/IoT Hub-kapcsolat életben maradjon, mind a szolgáltatás, mind az ügyfél rendszeresen küldjön egy *életben tartást biztosító* pinget egymásnak. Az IoT SDK-t használó ügyfél az alábbi táblázatban meghatározott időközönként életben tarthatatlant küld:

|Nyelv  |Az életben tartás alapértelmezett időköze  |Konfigurálható  |
|---------|---------|---------|
|Node.js     |   180 másodperc      |     Nem    |
|Java     |    230 másodperc     |     Nem    |
|C#     | 240 másodperc |  [Igen](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md#mqtt-transport)   |
|C#     | 300 másodperc |  [Igen](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/iothub/device/src/Transport/Mqtt/MqttTransportSettings.cs#L89)   |
|Python (V2)   | 60 másodperc |  Nem   |

Az [MQTT-specifikációt](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718081)követően az IoT Hub életben tartó pingidőtartata az ügyfél életben tartásának 1,5-szerese. Az IoT Hub azonban a kiszolgálóoldali időkorlátot 29,45 percre (1767 másodpercre) korlátozza, mivel az összes Azure-szolgáltatás az Azure load balancer TCP-időtúllépéshez van kötve, ami 29,45 perc. 

Például egy java SDK-t használó eszköz elküldi az életben tartást a pingelést, majd elveszíti a hálózati kapcsolatot. 230 másodperccel később az eszköz elmulasztja az életben tartást jelző pingelést, mert offline állapotban van. Az IoT Hub azonban nem zárja le azonnal a `(230 * 1.5) - 230 = 115` kapcsolatot - még egy másodpercet vár, mielőtt leválasztaná az eszközt a [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)hibával. 

A beállítható maximális ügyfélérték másodperc. `1767 / 1.5 = 1177` Minden forgalom visszaállítja az életben tartást. Például egy sikeres SAS-token frissítése visszaállítja az életben tartást.

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Eszközalkalmazás áttelepítése AMQP-ről MQTT-re

Ha az [eszköz SDK-k,](https://github.com/Azure/azure-iot-sdks)váltás az AMQP az MQTT módosítani kell a protokoll paraméter az ügyfél inicializálása korábban megadottmódon.

Ennek során ellenőrizze a következő elemeket:

* Az AMQP számos feltételnél adja vissza a hibákat, míg az MQTT megszakítja a kapcsolatot. Ennek eredményeképpen a kivételkezelési logika néhány módosítást igényelhet.

* Az MQTT nem támogatja az *elutasítási* műveleteket, amikor [felhőből az eszközre](iot-hub-devguide-messaging.md)irányuló üzeneteket fogad. Ha a háttéralkalmazásnak választ kell kapnia az eszközalkalmazástól, fontolja meg [a közvetlen módszerek](iot-hub-devguide-direct-methods.md)használatát.

* Az AMQP nem támogatott a Python SDK-ban

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Az MQTT protokoll közvetlen használata (eszközként)

Ha egy eszköz nem tudja használni az eszköz SDK-k, továbbra is csatlakozhat a nyilvános eszköz végpontok segítségével MQTT protokoll port 8883. A **CONNECT** csomagban az eszköznek a következő értékeket kell használnia:

* Az **Ügyfélazonosító** mezőben használja az **deviceId azonosítót.**

* A **Felhasználónév** mezőben `{iothubhostname}/{device_id}/?api-version=2018-06-30`használja `{iothubhostname}` a , ahol a teljes CName az IoT hub.

    Ha például az IoT hub neve **contoso.azure-devices.net,** és az eszköz neve **MyDevice01,** a teljes **Felhasználónév** mezőnek tartalmaznia kell:

    `contoso.azure-devices.net/MyDevice01/?api-version=2018-06-30`

* A **Jelszó** mezőben használjon SAS-jogkivonatot. A SAS-jogkivonat formátuma megegyezik a HTTPS és az AMQP protokollok formátumával:

  `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`

  > [!NOTE]
  > Ha X.509 tanúsítványhitelesítést használ, a SAS-jogkivonat-jelszavak nem szükségesek. További információ: [X.509 biztonság beállítása az Azure IoT Hubban,](iot-hub-security-x509-get-started.md) és kövesse [az alábbi](#tlsssl-configuration)kódutasításokat.

  A SAS-jogkivonatok létrehozásáról az [IoT Hub biztonsági jogkivonatok használata](iot-hub-devguide-security.md#use-sas-tokens-in-a-device-app)című eszközszakaszban talál további információt.

  Tesztelés közben a platformfüggetlen Azure [IoT-eszközöket a Visual Studio-kódhoz](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) vagy a CLI-bővítmény parancsot, az [az iot hub generate-sas-tokenet](/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-generate-sas-token) is használhatja egy SAS-jogkivonat gyors létrehozásához, amelyet a saját kódjába másolhat és illeszthet be:

### <a name="for-azure-iot-tools"></a>Azure IoT-eszközökhöz

1. Bontsa ki az **AZURE IOT HUB DEVICES (Azure IOT HUB DEVICES)** lapot a Visual Studio-kód bal alsó sarkában.
  
2. Kattintson a jobb gombbal az eszközre, és válassza **a SAS-jogkivonat létrehozása az eszközhöz parancsot.**
  
3. Állítsa be **a lejárati időt,** és nyomja meg az "Enter" gombot.
  
4. A SAS-token létrejön, és a vágólapra másolva lesz.

   A létrehozott SAS-jogkivonat a következő struktúrával rendelkezik:

   `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

   A token nek az MQTT használatával történő **csatlakozáshoz a jelszó** mezőként használható része a következő:

   `SharedAccessSignature sr={your hub name}.azure-devices.net%2Fdevices%2FMyDevice01%2Fapi-version%3D2016-11-14&sig=vSgHBMUG.....Ntg%3d&se=1456481802`

Az MQTT-csomagok csatlakoztatása és leválasztása esetén az IoT Hub kiad egy eseményt az **Operations Monitoring** csatornán. Ez az esemény további információkat tartalmaz, amelyek segíthetnek a kapcsolódási problémák megoldásában.

Az eszközalkalmazás megadhat egy **Will** üzenetet a **CONNECT** csomagban. Az eszközalkalmazás `devices/{device_id}/messages/events/` kell `devices/{device_id}/messages/events/{property_bag}` használnia, vagy a **Will** témakör neve definiálni **will** üzeneteket kell továbbítani, mint egy telemetriai üzenet. Ebben az esetben, ha a hálózati kapcsolat le van zárva, de a **DISCONNECT** csomag korábban nem érkezett az eszközről, majd az IoT Hub elküldi a **Connect** csomagban küldött **Will** üzenetet a telemetriai csatornára. A telemetriai csatorna lehet az alapértelmezett **események** végpont, vagy egy egyéni végpont által meghatározott IoT Hub útválasztás. Az üzenet **iothub-MessageType** tulajdonsággal **rendelkezik,** amelyhez Will érték van rendelve.

### <a name="an-example-of-c-code-using-mqtt-without-azure-iot-c-sdk"></a>Példa c-kódra az MQTT használatával az Azure IoT C SDK nélkül
Ebben a [tárházban](https://github.com/Azure-Samples/IoTMQTTSample)talál egy pár C/C++ bemutató projektek, amelyek bemutatják, hogyan küldhet telemetriai üzeneteket, események fogadására egy IoT hub használata nélkül az Azure IoT C SDK. 

Ezek a minták az Eclipse Mosquitto könyvtár segítségével üzenetet küldhetnek az IoT hubban megvalósított MQTT Brokernek.

Ez az adattár a következőket tartalmazza:

**Windows esetén:**

* TelemetryMQTTWin32: olyan kódot tartalmaz, amely telemetriai üzenetet küld egy Azure IoT-központnak, amely windowsos gépen készült és futtatható.

* SubscribeMQTTWin32: egy Windows-gépen egy adott IoT-központ eseményeire való feliratkozáshoz kódot tartalmaz.

* DeviceTwinMQTTWin32: olyan kódot tartalmaz, amely lekérdezi és előfizet egy eszköz ikereseményeire az Azure IoT hubban egy Windows-gépen.

* PnPMQTTWin32: olyan kódot tartalmaz, amely az IoT Plug & Play-előnézeti eszközökkel rendelkező telemetriai üzenetet küld egy Windows-gépen épített és futtatott Azure IoT-központba. További információk az IoT Plug & Play-ről [itt](https://docs.microsoft.com/azure/iot-pnp/overview-iot-plug-and-play)

**Linux esetén:**

* MQTTLinux: tartalmazza a linuxos (WSL, Ubuntu és Raspbian rendszeren futtatott kódot és build szkriptet) eddig tesztelték.

* LinuxConsoleVS2019: ugyanazt a kódot tartalmazza, de egy VS2019 projektben, amely a WSL -t (Windows Linux alrendszert) célozza. Ez a projekt lehetővé teszi, hogy hibakeresés a linuxos kód lépésről lépésre a Visual Studio.

**A mosquitto_pub esetében:**

Ez a mappa két mintaparancsot tartalmaz, amelyeket mosquitto_pub Mosquitto.org által biztosított segédprogrammal használnak.

* Mosquitto_sendmessage: egy egyszerű szöveges üzenetet küldeni egy Azure IoT hub eszközként működő.

* Mosquitto_subscribe: az Azure IoT-központban bekövetkező események megtekintéséhez.

## <a name="using-the-mqtt-protocol-directly-as-a-module"></a>Az MQTT protokoll közvetlen használata (modulként)

Az IoT Hubhoz való csatlakozás MQTT-n keresztül modulidentitással hasonló az eszközhöz [(lásd fent),](#using-the-mqtt-protocol-directly-as-a-device)de a következőket kell használnia:

* Állítsa az ügyfélazonosítót `{device_id}/{module_id}`.

* Ha felhasználónévvel és jelszóval hitelesíti, `<hubname>.azure-devices.net/{device_id}/{module_id}/?api-version=2018-06-30` állítsa be a felhasználónevet, és használja a modulidentitáshoz társított SAS-tokent jelszóként.

* Témakörként használható `devices/{device_id}/modules/{module_id}/messages/events/` telemetriai adatok közzétételéhez.

* Használja `devices/{device_id}/modules/{module_id}/messages/events/` will téma.

* A két GET és PATCH téma azonos a modulok és eszközök esetében.

* A kettős állapot témakör azonos a modulok és eszközök.

## <a name="tlsssl-configuration"></a>TLS/SSL konfiguráció

Az MQTT protokoll közvetlen használatához az *ügyfélnek* TLS/SSL-en keresztül kell csatlakoznia. A lépés kihagyására tett kísérletek csatlakozási hibákkal sikertelenek.

A TLS-kapcsolat létrehozásához előfordulhat, hogy le kell töltenie és hivatkoznia kell a DigiCert Baltimore főtanúsítványra. Ez a tanúsítvány az, amelyet az Azure a kapcsolat védelmére használ. Ezt a tanúsítványt az [Azure-iot-sdk-c](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) tárházban találja. További információ ezekről a tanúsítványokról megtalálható [Digicert honlapján](https://www.digicert.com/digicert-root-certificates.htm).

Egy példa arra, hogyan valósíthatja meg ezt a Python-verziójával a [Paho MQTT könyvtár](https://pypi.python.org/pypi/paho-mqtt) az Eclipse Foundation a következőképpen nézhet ki, mint a következő.

Először telepítse a Paho könyvtárat a parancssori környezetből:

```cmd/sh
pip install paho-mqtt
```

Ezután valósítsa meg az ügyfelet egy Python-parancsfájlban. A helyőrzők cseréje az alábbiak szerint:

* `<local path to digicert.cer>`A DigiCert Baltimore Root tanúsítványt tartalmazó helyi fájl elérési útja. Ezt a fájlt úgy hozhatja létre, hogy másolja a tanúsítványadatait a [certs.c-ből](https://github.com/Azure/azure-iot-sdk-c/blob/master/certs/certs.c) az Azure IoT SDK-ban C.Tartalmazza `-----BEGIN CERTIFICATE-----` a sorokat és `-----END CERTIFICATE-----`a , távolítsa el a `"` jeleket minden sor elején és végén, és távolítsa el a `\r\n` karaktereket minden sor végén.

* `<device id from device registry>`az IoT hubhoz hozzáadott eszköz azonosítója.

* `<generated SAS token>`Egy SAS-jogkivonat a cikkben korábban ismertetett módon létrehozott eszközhöz.

* `<iot hub name>`az IoT hub nevét.

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

Ha eszköztanúsítvánnyal szeretne hitelesíteni, frissítse a fenti kódrészletet a következő módosításokkal (lásd: [X.509 hitelesítésszolgáltatói tanúsítvány beszerezése](./iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) a tanúsítványalapú hitelesítésre való felkészülésről):

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

## <a name="sending-device-to-cloud-messages"></a>Eszközről felhőbe irányuló üzenetek küldése

A sikeres kapcsolat létrehozása után az eszköz üzeneteket `devices/{device_id}/messages/events/` `devices/{device_id}/messages/events/{property_bag}` küldhet az IoT Hubnak **a témakörneve**használatával vagy néven. Az `{property_bag}` elem lehetővé teszi, hogy az eszköz további tulajdonságokkal rendelkező üzeneteket küldjön url-kódolású formátumban. Például:

```text
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Ez `{property_bag}` az elem ugyanazt a kódolást használja, mint a HTTPS protokoll lekérdezési karakterláncai.

Az alábbiakban az IoT Hub implementáció-specifikus viselkedéseit listáként listáként listáként listáként listáként listáként listázta:

* Az IoT Hub nem támogatja a QoS 2 üzeneteket. Ha egy eszközalkalmazás a **QoS 2**szolgáltatással üzenetet tesz közzé, az IoT Hub bezárja a hálózati kapcsolatot.

* Az IoT Hub nem őrzi meg az üzenetek megtartása. Ha egy eszköz üzenetet küld a **RETAIN** jelző 1-re, az IoT Hub hozzáadja az **x-opt-retain** alkalmazás tulajdonságot az üzenethez. Ebben az esetben a megőrzési üzenet megőrzése helyett az IoT Hub továbbítja azt a háttéralkalmazásnak.

* Az IoT Hub eszközönként csak egy aktív MQTT-kapcsolatot támogat. Minden új MQTT-kapcsolat ugyanazon eszközazonosító nevében az IoT Hub elejti a meglévő kapcsolatot.

További információt az [Üzenetküldés fejlesztői útmutatójában](iot-hub-devguide-messaging.md)talál.

## <a name="receiving-cloud-to-device-messages"></a>Felhőből az eszközre irányuló üzenetek fogadása

Ha üzeneteket szeretne kapni az IoT `devices/{device_id}/messages/devicebound/#` Hubtól, az eszköznek fel kell iratkoznia **témakörszűrőként.** A témakörszűrőben `#` lévő többszintű helyettesítő karakter csak arra szolgál, hogy az eszköz további tulajdonságokat fogadhasson a témakör nevében. Az IoT Hub nem engedélyezi `#` `?` a vagy a helyettesítő karakterek használatát az altémakörök szűréséhez. Mivel az IoT Hub nem egy általános célú pub-sub üzenetküldő közvetítő, csak a dokumentált témakörneveket és témakörszűrőket támogatja.

Az eszköz nem kap üzeneteket az IoT Hubtól, amíg sikeresen elő nem `devices/{device_id}/messages/devicebound/#` fizetett az eszközspecifikus végpontjára, amelyet a témakörszűrő jelöl. Az előfizetés létrehozása után az eszköz felhőből az eszközre irányuló üzeneteket kap, amelyeket az előfizetés után küldtek el. Ha az eszköz **0-ra** van **0**állítva a CleanSession jelzővel, az előfizetés a különböző munkamenetek között megmarad. Ebben az esetben a következő alkalommal, amikor az eszköz csatlakozik **a CleanSession** 0-hoz, megkapja a kapcsolat bontása közben küldött függőben lévő üzeneteket. Ha az eszköz **cleansession** jelző **1-re** van állítva, de nem kap üzeneteket az IoT Hubtól, amíg elő nem fizet az eszköz-végpontra.

Az IoT Hub a **témakör név,** `devices/{device_id}/messages/devicebound/`vagy `devices/{device_id}/messages/devicebound/{property_bag}` ha vannak üzenet tulajdonságok üzeneteket. `{property_bag}`URL-kódolású kulcs/érték üzenettulajdonságokat tartalmaz. A tulajdonságcsomag csak az alkalmazás tulajdonságokat és a felhasználó által beállítható rendszertulajdonságokat (például **messageId** vagy **correlationId)** tartalmazza. A rendszertulajdonságok nevei **$** előtaggal rendelkeznek, az alkalmazástulajdonságok az eredeti tulajdonságnevet használják előtag nélkül.

Amikor egy eszközalkalmazás előfizet egy **qoS 2-es**témakörre, az IoT Hub maximális 1-es QoS-szintet biztosít a **SUBACK** csomagban. Ezt követően az IoT Hub üzeneteket küld az eszköznek a QoS 1 használatával.

## <a name="retrieving-a-device-twins-properties"></a>Ikereszköz tulajdonságainak beolvasása

Először egy eszköz `$iothub/twin/res/#`előfizet a , hogy megkapja a művelet válaszait. Ezután üres üzenetet küld `$iothub/twin/GET/?$rid={request id}`a témakörnek, amelynek a **kérelemazonosítójának**feltöltött értéke van. A szolgáltatás ezután küld egy válaszüzenetet, `$iothub/twin/res/{status}/?$rid={request id}`amely tartalmazza az eszköz iker adatait a témában, ugyanazt a **kérésazonosítót** használva, mint a kérés.

A kérelemazonosító bármely érvényes érték lehet egy üzenettulajdonság-értékhez, az [IoT Hub üzenetküldő fejlesztői útmutatója](iot-hub-devguide-messaging.md)szerint, és az állapot egész számként érvényesítve.

A választörzs az ikereszköz tulajdonságai szakaszát tartalmazza, amint az a következő válaszpéldában látható:

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

|status | Leírás |
| ----- | ----------- |
| 200 | Sikeres |
| 429 | Túl sok kérelem (szabályozta), az [IoT Hub szabályozásának](iot-hub-devguide-quotas-throttling.md) |
| 5** | Kiszolgálóhibák |

További információ: [Device twins developer's guide](iot-hub-devguide-device-twins.md).

## <a name="update-device-twins-reported-properties"></a>Az ikereszköz jelentett tulajdonságainak frissítése

A jelentett tulajdonságok frissítéséhez az eszköz egy kérelem az IoT Hub egy adott MQTT-témakörben keresztül egy kiadvány. A kérelem feldolgozása után az IoT Hub válaszol a sikeres vagy sikertelen állapotát a frissítési művelet egy kiadvány on-t egy másik témakörben. Ezt a témakört az eszköz előfizetheti annak érdekében, hogy értesítse a kettős frissítési kérelem eredményéről. Az ilyen típusú kérés/válasz interakció mqtt- ben történő megvalósításához kihasználjuk a kérelemazonosító (`$rid`) fogalmát, amelyet az eszköz eredetileg a frissítési kérelemben biztosít. Ez a kérelemazonosító is szerepel az IoT Hub válaszában, hogy az eszköz korreláljon a választ az adott korábbi kéréshez.

A következő sorozat azt ismerteti, hogy egy eszköz hogyan frissíti a jelentett tulajdonságokat az ikereszközben az IoT Hubban:

1. Egy eszköznek először `$iothub/twin/res/#` elő kell fizetnie a témakörre, hogy megkapja a művelet válaszait az IoT Hubtól.

2. Az eszköz üzenetet küld, amely tartalmazza `$iothub/twin/PATCH/properties/reported/?$rid={request id}` az eszköz ikerfrissítése a témához. Ez az üzenet **kérésazonosító-értéket** tartalmaz.

3. A szolgáltatás ezután válaszüzenetet küld, amely a témakörben `$iothub/twin/res/{status}/?$rid={request id}`jelentett tulajdonságok gyűjteményének új ETag értékét tartalmazza. Ez a válaszüzenet ugyanazt a **kérésazonosítót** használja, mint a kérés.

A kérésüzenet törzse egy JSON-dokumentumot tartalmaz, amely a jelentett tulajdonságok új értékeit tartalmazza. A JSON-dokumentum minden tagja frissíti vagy hozzáadja a megfelelő tagot az ikereszköz dokumentumához. A tag `null`beállítása , törli a tagot a tartalmazó objektumból. Például:

```json
{
    "telemetrySendFrequency": "35m",
    "batteryLevel": 60
}
```

A lehetséges állapotkódok a következők:

|status | Leírás |
| ----- | ----------- |
| 204 | Sikeres (nem ad vissza tartalmat) |
| 400 | Rossz kérés. Hibás Antorziós JSON |
| 429 | Túl sok kérelem (szabályozta), az [IoT Hub szabályozásának](iot-hub-devguide-quotas-throttling.md) |
| 5** | Kiszolgálóhibák |

Az alábbi python-kódrészlet bemutatja az MQTT-n (Paho MQTT ügyfél használatával) keresztül jelentett ikertulajdonságok frissítési folyamatát:

```python
from paho.mqtt import client as mqtt

# authenticate the client with IoT Hub (not shown here)

client.subscribe("$iothub/twin/res/#")
rid = "1"
twin_reported_property_patch = "{\"firmware_version\": \"v1.1\"}"
client.publish("$iothub/twin/PATCH/properties/reported/?$rid=" +
               rid, twin_reported_property_patch, qos=0)
```

A fenti két jelentett tulajdonságfrissítési művelet sikeressége után az IoT `$iothub/twin/res/204/?$rid=1&$version=6`Hub `204` kiadványüzenete a következő `$rid=1` témakörrel fog rendelkezni: ahol a sikeres állapotkód `$version` felel meg az eszköz által a kódban megadott kérelemazonosítónak, és megfelel az eszköz twins jelentett tulajdonságainak a frissítés után jelentett tulajdonságszakaszának verziójának.

További információ: [Device twins developer's guide](iot-hub-devguide-device-twins.md).

## <a name="receiving-desired-properties-update-notifications"></a>A kívánt tulajdonságok frissítési értesítéseinek fogadása

Amikor egy eszköz csatlakoztatva van, az `$iothub/twin/PATCH/properties/desired/?$version={new version}`IoT Hub értesítéseket küld a témakörnek, amely tartalmazza a megoldás háttérrendszer által végrehajtott frissítés tartalmát. Például:

```json
{
    "telemetrySendFrequency": "5m",
    "route": null,
    "$version": 8
}
```

Ami a tulajdonságfrissítéseket illeti, az értékek azt jelentik, `null` hogy a JSON-objektumtag törlődik. Vegye figyelembe `$version` azt is, hogy az iker kívánt tulajdonságszakaszának új verzióját jelzi.

> [!IMPORTANT]
> Az IoT Hub csak akkor hoz létre módosítási értesítéseket, ha az eszközök csatlakoztatva vannak. Győződjön meg arról, hogy az [eszköz újrakapcsolódási folyamat](iot-hub-devguide-device-twins.md#device-reconnection-flow) ot valósítja meg, hogy a kívánt tulajdonságok szinkronizálva maradjanak az IoT Hub és az eszközalkalmazás között.

További információ: [Device twins developer's guide](iot-hub-devguide-device-twins.md).

## <a name="respond-to-a-direct-method"></a>Közvetlen metódus ra válaszolása

Először is, az `$iothub/methods/POST/#`eszköznek elő kell fizetnie. Az IoT Hub metóduskéréseket küld a témakörnek, `$iothub/methods/POST/{method name}/?$rid={request id}`érvényes JSON vagy üres törzskel.

A válaszadáshoz a készülék érvényes JSON-t vagy `$iothub/methods/res/{status}/?$rid={request id}`üres törzset küld a témakörnek. Ebben az üzenetben a **kérelemazonosítónak** meg kell egyeznie a kérésüzenetben szereplővel, és **az állapotnak** egész számnak kell lennie.

További információ: [Direct method developer's guide](iot-hub-devguide-direct-methods.md).

## <a name="additional-considerations"></a>Néhány fontos megjegyzés

Végső szempontként, ha testre kell szabnia az MQTT protokoll viselkedését a felhőoldalon, tekintse át az [Azure IoT protokoll átjáróját.](iot-hub-protocol-gateway.md) Ez a szoftver lehetővé teszi egy nagy teljesítményű egyéni protokollátjáró üzembe helyezését, amely közvetlenül kapcsolódik az IoT Hubhoz. Az Azure IoT protokoll átjáró lehetővé teszi, hogy testre szabhatja az eszköz protokoll tanmezős MQTT-telepítések vagy más egyéni protokollok befogadására. Ez a megközelítés azonban egyéni protokollátjáró futtatását és üzemeltetését igényli.

## <a name="next-steps"></a>További lépések

Az MQTT protokollról az [MQTT dokumentációban](https://mqtt.org/documentation)olvashat bővebben.

Ha többet szeretne megtudni az IoT Hub-telepítés megtervezéséről, olvassa el a következő témakört:

* [Azure Certified for IoT eszközkatalógus](https://catalog.azureiotsolutions.com/)
* [További protokollok támogatása](iot-hub-protocol-gateway.md)
* [Összehasonlítás az Eseményközpontokkal](iot-hub-compare-event-hubs.md)
* [Méretezés, HA és DR](iot-hub-scaling.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)
* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)

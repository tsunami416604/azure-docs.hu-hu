---
title: Megismerheti az Azure IoT hubhoz AMQP támogatási |} A Microsoft Docs
description: Fejlesztői útmutató – csatlakozik az IoT Hub-eszközök támogatása az AMQP protokollal rendelkező eszköz és szolgáltatás felé néző végpontok. Az Azure IoT eszközoldali SDK-k AMQP támogatja a beépített kapcsolatos információkat tartalmaz.
author: rezasherafat
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: rezas
ms.openlocfilehash: c304c9b7fe02e3396d49aee0b70576071d9fac92
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055380"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Az AMQP protokoll használatával kommunikálnak az IoT hubhoz

Az Azure IoT Hub által támogatott [OASIS speciális Message Queuing AMQP protokoll 1.0-s verzió](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) különböző funkciókkal rendelkező eszköz és szolgáltatás felé néző végpontok biztosításához. Ez a dokumentum azt ismerteti, hogyan AMQP ügyfelek csatlakozni az IoT hub, IoT Hub-funkciók használatához.

## <a name="service-client"></a>Adatszolgáltatási ügyfél

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Csatlakozás és hitelesítés egy IoT hubra (ügyfél-szolgáltatás)
Az AMQP használatával csatlakozik az IoT hub, az ügyfél használhatja a [jogcímalapú biztonsági (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) vagy [egyszerű hitelesítési és biztonsági réteg (SASL) hitelesítési](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

A következő információkat megadása kötelező a szolgáltatás-ügyfél:

| Információ | Érték | 
|-------------|--------------|
| IoT hub-eszköznév | `<iot-hub-name>.azure-devices.net` |
| Kulcsnév | `service` |
| Hozzáférési kulcs | A szolgáltatáshoz társított elsődleges vagy másodlagos kulcs |
| Közös hozzáférésű jogosultságkód | Egy rövid élettartamú közös hozzáférésű jogosultságkód a következő formátumban: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Az aláírás létrehozásához a kód lekéréséhez lásd: [férhet hozzá az IoT Hub](./iot-hub-devguide-security.md#security-token-structure).

A következő kódban kódrészletet a [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python) egy IoT hubra küldő hivatkozás-n keresztül kapcsolódni.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>' # e.g., '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Meghívása a felhőből az eszközre irányuló üzenetek (ügyfél-szolgáltatás)
A felhőből az eszközre irányuló üzenetek az exchange között a szolgáltatás és az IoT hub és az eszköz és az IoT hub közötti kapcsolatos további információkért lásd: [felhőből az eszközre irányuló üzenetek küldése az IoT hub](iot-hub-devguide-messages-c2d.md). A szolgáltatás ügyfél használ két hivatkozások üzeneteket küldjön és fogadjon korábban a visszajelzés elküldött üzenetek, eszközökről, az alábbi táblázatban leírtak szerint:

| Létrehozta | Hivatkozás típusa | Elérési útja | Leírás |
|------------|-----------|-----------|-------------|
| Szolgáltatás | Küldő hivatkozás | `/messages/devicebound` | Felhőből az eszközre irányuló üzenetek, amely az eszközök felé a szolgáltatás által küldött ezen a hivatkozáson. Ezen a kapcsolaton keresztül küldött üzenetek rendelkezik saját `To` tulajdonság beállítása a céleszköz fogadó elérési útja, `/devices/<deviceID>/messages/devicebound`. |
| Szolgáltatás | Fogadó-hivatkozás | `/messages/serviceBound/feedback` | Befejezési, elutasítás és lemondás visszajelzés érkező üzenetekre érkezett eszközök erre a hivatkozásra szolgáltatás által. Visszajelzési üzenetek kapcsolatos további információkért lásd: [felhőből az eszközre irányuló üzenetek küldése az IoT hubról](./iot-hub-devguide-messages-c2d.md#message-feedback). |

A következő kódrészletet bemutatja, hogyan hozhat létre a felhőből az eszközre irányuló üzenet, és küldje el az eszközre használatával a [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Szeretne visszajelzést kapni, a szolgáltatás ügyfele fogadó hivatkozást hoz létre. A következő kódrészletet bemutatja, hogyan hozhat létre egy hivatkozást a használatával a [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python):

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
  print('received a message')
  # Check content_type in message property to identify feedback messages coming from device
  if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
    msg_body_raw = msg.get_data()
    msg_body_str = ''.join(msg_body_raw)
    msg_body = json.loads(msg_body_str)
    print(json.dumps(msg_body, indent=2))
    print('******************')
    for feedback in msg_body:
      print('feedback received')
      print('\tstatusCode: ' + str(feedback['statusCode']))
      print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
      print('\tdeviceId: ' + str(feedback['deviceId']))
      print
  else:
    print('unknown message:', msg.properties.content_type)
```

Ahogyan a fenti kóddal, visszajelzés a felhőből az eszközre irányuló üzenet rendelkezik egy tartalomtípusa *application/vnd.microsoft.iothub.feedback.json*. Az üzenettörzs JSON tulajdonságok használatával kikövetkeztetni az eredeti üzenet kézbesítési állapota:
* Kulcs `statusCode` küldjön visszajelzést a szervezet rendelkezik a következő értékek egyikét: *Sikeres*, *lejárt*, *DeliveryCountExceeded*, *elutasított*, vagy *kiürítése*.
* Kulcs `deviceId` küldjön visszajelzést a szervezet van a céleszközön azonosítója.
* Kulcs `originalMessageId` küldjön visszajelzést a szervezet van az eredeti felhőből az eszközre irányuló üzenetre, amely a szolgáltatás által küldött azonosítója. A kézbesítési állapotának segítségével összehasonlíthatja a visszajelzést a felhőből az eszközre irányuló üzenetek.

### <a name="receive-telemetry-messages-service-client"></a>(Ügyfél-szolgáltatás) telemetriaüzenetek fogadásához

Alapértelmezés szerint az IoT hub egy beépített eseményközpont betöltött eszköz telemetriai üzeneteket tárolja. A szolgáltatásügyfél az AMQP protokollt használhatja a tárolt események fogadására.

Erre a célra a szolgáltatási ügyfél először meg kell csatlakozni az IoT hub-végponton, és a egy átirányítási-cím a beépített az event hubs fogadására. A szolgáltatásügyfél a megadott címet ezután használja a beépített event hubhoz való csatlakozáshoz.

Az ügyfél minden egyes lépésének nyújtjuk át az alábbi adatokra van szüksége:
* Érvényes hitelesítő adatokat (a szolgáltatás közös hozzáférésű jogosultságkód).
* A fogyasztói csoportot partíció érkező üzeneteket beolvasni kívánt helyes formátumú elérési útját. Egy adott felhasználói csoport és a partíció azonosítóhoz, az elérési út formátuma a következő: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (az alapértelmezett felhasználói csoport `$Default`).
* Egy nem kötelező szűrési predikátum a partíción kiindulási pontot jelöl ki. Ez a predikátum egy feladatütemezési számát, offset vagy a várólistán lévő időbélyeg formájában is lehet.

A következő kódban kódrészletet a [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python) az előző lépések bemutatása:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI
def set_endpoint_filter(uri, endpoint_filter=''):
  source_uri = uamqp.address.Source(uri)
  source_uri.set_filter(endpoint_filter)
  return source_uri

receive_client = uamqp.ReceiveClient(set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
  batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
  # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
  receive_client.close()

  sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(redirect.address, policy_name, access_key)
  receive_client = uamqp.ReceiveClient(set_endpoint_filter(redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
  print('*** received a message ***')
  print(''.join(msg.get_data()))
  print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
  print('\t: ' + str(msg.annotations['x-opt-offset']))
  print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Egy adott eszköz azonosítója az IoT hub használja az Eszközazonosítót kivonatát meghatározásához melyik partícióba, az üzenetek tárolásához. Az előző kódrészlet bemutatja, hogyan fogadja az eseményeket egyetlen ilyen partíció. Vegye azonban figyelembe, hogy egy tipikus alkalmazás milyen gyakran kell az összes event hub-partíció tárolt események beolvasása.


## <a name="device-client"></a>Eszközügyfél

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Csatlakozás és hitelesítés egy IoT hubra (ügyfél)
Az AMQP használatával csatlakozik az IoT hub, az eszköz használhatja [jogcímalapú biztonsági (CB)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) vagy [egyszerű hitelesítési és biztonsági réteg (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) hitelesítés.

A következő információkra szükség az eszközügyfél:

| Információ | Érték | 
|-------------|--------------|
| IoT hub-eszköznév | `<iot-hub-name>.azure-devices.net` |
| Hozzáférési kulcs | Az eszközhöz társított elsődleges vagy másodlagos kulcs |
| Közös hozzáférésű jogosultságkód | Egy rövid élettartamú közös hozzáférésű jogosultságkód a következő formátumban: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`. Az aláírás létrehozásához a kód lekéréséhez lásd: [férhet hozzá az IoT Hub](./iot-hub-devguide-security.md#security-token-structure).


A következő kódban kódrészletet a [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python) egy IoT hubra küldő hivatkozás-n keresztül kapcsolódni.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(hostname=hostname, device_id=device_id), access_key, None)

operation = '<operation-link-name>' # e.g., '/devices/{device_id}/messages/devicebound'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

A következő hivatkozás útvonalak eszközművelet támogatottak:

| Létrehozta | Hivatkozás típusa | Elérési útja | Leírás |
|------------|-----------|-----------|-------------|
| Eszközök | Fogadó-hivatkozás | `/devices/<deviceID>/messages/devicebound` | Felhőből az eszközre irányuló üzenetek, amely eszközöket szánt érkezik erre a hivatkozásra minden cél eszközre. |
| Eszközök | Küldő hivatkozás | `/devices/<deviceID>messages/events` | Eszköz – felhő üzenetek egy eszközről küldött a rendszer a kapcsolaton keresztül küldi el. |
| Eszközök | Küldő hivatkozás | `/messages/serviceBound/feedback` | A szolgáltatásnak küldött e-kapcsolaton keresztül az eszközök a felhőből az eszközre irányuló üzenetek visszajelzés. |


### <a name="receive-cloud-to-device-commands-device-client"></a>Felhőből az eszközre irányuló parancsok (ügyfél)
Felhőből az eszközre irányuló parancsok eszközökre küldött az ügyfélszámítógépekre érkeznek egy `/devices/<deviceID>/messages/devicebound` hivatkozásra. Eszközök kötegekben ezeket az üzeneteket fogadni, és használja az üzenet hasznos adatforgalmat, üzenet tulajdonságait, jegyzetek vagy alkalmazás tulajdonságai üzenetben igény szerint.

A következő kódban kódrészletet a [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python)) felhőből az eszközre irányuló üzenetek fogadása egy eszközre.

```python
# ... 
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
  batch = receive_client.receive_message_batch(max_batch_size=5)
  for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))

    # Property 'to' is set to: '/devices/device1/messages/devicebound',
    print('\tto:                     ' + str(msg.properties.to))

    # Property 'message_id' is set to value provided by the service
    print('\tmessage_id:             ' + str(msg.properties.message_id))

    # Other properties are present if they were provided by the service
    print('\tcreation_time:          ' + str(msg.properties.creation_time))
    print('\tcorrelation_id:         ' + str(msg.properties.correlation_id))
    print('\tcontent_type:           ' + str(msg.properties.content_type))
    print('\treply_to_group_id:      ' + str(msg.properties.reply_to_group_id))
    print('\tsubject:                ' + str(msg.properties.subject))
    print('\tuser_id:                ' + str(msg.properties.user_id))
    print('\tgroup_sequence:         ' + str(msg.properties.group_sequence))
    print('\tcontent_encoding:       ' + str(msg.properties.content_encoding))
    print('\treply_to:               ' + str(msg.properties.reply_to))
    print('\tabsolute_expiry_time:   ' + str(msg.properties.absolute_expiry_time))
    print('\tgroup_id:               ' + str(msg.properties.group_id))

    # Message sequence number in the built-in event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Telemetria üzenetküldés (ügyfél)
Egy eszközről is küldhet telemetriai üzeneteket az AMQP használatával. Az eszköz is megadhat alkalmazástulajdonságokat tartalmazó, illetve különböző üzenet tulajdonságait, például az állapotüzenet-azonosító.

A következő kódban kódrészletet a [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python) eszköz – felhő üzeneteket küldhet egy eszközről.


```python
# ... 
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>További megjegyzések
* Az AMQP-kapcsolatok egy hálózati hiba okozhatta, vagy a hitelesítés lejárta miatt előfordulhat, hogy tartománnyá (létrehozva a kódban) tokent. A szolgáltatásügyfél kell kezelni az ilyen körülmények között, és helyreállítani a kapcsolatot és a hivatkozásokat, szükség esetén. Ha egy hitelesítési jogkivonat lejár, az ügyfél elkerülheti a kapcsolat legördülő proaktív módon megújítani a lejárat előtt a jogkivonatot.
* Az ügyfél alkalmanként megfelelően kezeli a hivatkozás átirányítások képesnek kell lennie. Ilyen művelet megismeréséhez tekintse meg az AMQP-ügyfél dokumentációját.

## <a name="next-steps"></a>További lépések

Az AMQP protokollt kapcsolatos további információkért tekintse meg a [AMQP 1.0-s verziójú specifikáció](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Az IoT Hub üzenetküldési kapcsolatos további információkért lásd:

* [Felhőből az eszközre irányuló üzenetek](./iot-hub-devguide-messages-c2d.md)
* [További protokollok támogatása](iot-hub-protocol-gateway.md)
* [A Message Queuing Telemetriai Transport (MQTT) protokoll támogatása](./iot-hub-mqtt-support.md)

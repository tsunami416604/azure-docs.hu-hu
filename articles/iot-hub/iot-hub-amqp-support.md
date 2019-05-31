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
ms.openlocfilehash: d256faa42161e276e165f95c944b9f58ac4a8927
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66297421"
---
# <a name="communicate-with-your-iot-hub-using-the-amqp-protocol"></a>Az IoT hub, az AMQP protokoll használatával kommunikálnak.

IoT Hub által támogatott [AMQP 1.0-s verzió](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) különböző funkciókkal rendelkező eszköz és szolgáltatás felé néző végpontok biztosításához. Ez a dokumentum azt ismerteti, hogyan AMQP ügyfelek csatlakozni az IoT Hub az IoT Hub-funkciók használatához.

## <a name="service-client"></a>Adatszolgáltatási ügyfél

### <a name="connection-and-authenticating-to-iot-hub-service-client"></a>Kapcsolat és az IoT Hub (ügyfél-szolgáltatás) való hitelesítése
Az IoT hubhoz AMQP használatával csatlakozni egy ügyfél használhatja a [jogcím-alapú biztonság (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) vagy [egyszerű hitelesítési és biztonsági réteg (SASL) hitelesítési](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

A következő információkat megadása kötelező a szolgáltatás-ügyfél:

| Információ | Érték | 
|-------------|--------------|
| IoT Hub Hostname | `<iot-hub-name>.azure-devices.net` |
| Kulcsnév | `service` |
| Hozzáférési kulcs | A szolgáltatáshoz társított elsődleges vagy másodlagos kulcsot |
| Közös hozzáférésű Jogosultságkód | Rövid élettartamú SAS a következő formátumban: `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}` (az aláírás létrehozásához a kód található [Itt](./iot-hub-devguide-security.md#security-token-structure)).


Az alábbi kódrészlet [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python) csatlakozni az IoT hubra küldő hivatkozás segítségével.

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

### <a name="invoking-cloud-to-device-messages-service-client"></a>Meghívja a felhőből az eszközre irányuló üzenetek (ügyfél-szolgáltatás)
A felhőből az eszközre irányuló üzenetek exchange és az IoT Hub között, valamint az eszköz és az IoT Hub között ismertetett [Itt](iot-hub-devguide-messages-c2d.md). A szolgáltatásügyfél üzeneteket küldjön és fogadjon a korábban elküldött üzenetek visszajelzés eszközökről az alábbiakban két kapcsolatot használ.

| Létrehozta | Hivatkozás típusa | Elérési útja | Leírás |
|------------|-----------|-----------|-------------|
| Szolgáltatás | Küldő hivatkozás | `/messages/devicebound` | Az eszközök felé irányuló C2D üzenetek a szolgáltatás által küldött ezen a hivatkozáson. Ezen a kapcsolaton keresztül küldött üzenetek azok `To` tulajdonság beállítása a céleszköz fogadó elérési útja: például `/devices/<deviceID>/messages/devicebound`. |
| Szolgáltatás | Fogadó-hivatkozás | `/messages/serviceBound/feedback` | Befejezési, elutasítás és lemondás visszajelzési üzenetek erre a hivatkozásra a szolgáltatás által fogadott eszközökről. Visszajelzési üzenetek kapcsolatos további információkért lásd: [Itt](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Az alábbi kódrészlet bemutatja, hogyan hozzon létre egy C2D üzenetet, majd azokat elküldi a olyan eszköz használatával [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the devicebound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full' # Alternative values are 'positive', 'negative', and 'none'
app_props = { 'iothub-ack': ack }
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props, application_properties=app_props)

# Send the message using the send client created and connected IoT Hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if not needed
send_client.close()
```

Szeretne visszajelzést kapni, a szolgáltatás ügyfele fogadó hivatkozást hoz létre. Az alábbi kódrészlet bemutatja, hogyan ehhez használatával [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python).

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Recreate the URI using the feedback path above and authenticate
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

A fentiek C2D visszajelzés üzenet oszlopának tartalomtípusa `application/vnd.microsoft.iothub.feedback.json` és a JSON-törzs tulajdonságai használhatók kikövetkeztetnünk az eredeti üzenet kézbesítési állapota:
* Kulcs `statusCode` a visszajelzéshez törzsében szerepel, ezek az értékek valamelyikét: `['Success', 'Expired', 'DeliveryCountExceeded', 'Rejected', 'Purged']`.
* Kulcs `deviceId` visszajelzés a szervezet az eszköz azonosítója, cél van.
* Kulcs `originalMessageId` visszajelzés a szervezet rendelkezik a szolgáltatás által küldött eredeti C2D üzenet azonosítója. Ez a visszajelzési C2D üzenetek korrelációját használható.

### <a name="receive-telemetry-messages-service-client"></a>(Ügyfél-szolgáltatás) telemetriaüzenetek fogadásához
Alapértelmezés szerint az IoT Hub egy beépített eseményközpont betöltött eszköz telemetriai üzeneteket tárolja. A szolgáltatásügyfél az AMQP protokollt használhatja a tárolt események fogadására.

Erre a célra a szolgáltatási ügyfél először meg kell csatlakozni az IoT Hub-végponton, és a egy átirányítási-cím a beépített az Event Hubs fogadására. Adatszolgáltatási ügyfél ezután használja a megadott cím a beépített Event hubhoz való csatlakozáshoz.

Az ügyfél minden egyes lépésének nyújtjuk át az alábbi adatokra van szüksége:
* Érvényes hitelesítő adatokat (szolgáltatás SAS-token).
* A fogyasztói csoportot partíció szándékában olvasni az üzeneteket a helyes formátumú elérési útját. Egy adott felhasználói csoport és a partíció azonosítóhoz, az elérési út formátuma a következő: `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` (az alapértelmezett felhasználói csoport `$Default`).
* Egy nem kötelező szűrési predikátum szabálytulajdonságok kiindulási pont, a partíció (Ez lehet egy feladatütemezési számát, offset vagy a várólistán lévő időbélyeg formájában).

Az alábbi kódrészlet [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python) a fenti lépések bemutatása.

```python
import json
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified using endpiont_filter
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

Egy adott eszköz azonosítója az IoT Hub használja az Eszközazonosító kivonatát meghatározásához melyik partíción tárolja az üzeneteket a. A fenti kódrészletben az események fogadását az egyetlen mutatja be ilyen partíció. Vegye figyelembe azonban, hogy egy tipikus alkalmazás gyakran kell az összes event hub-partíció tárolt események beolvasása.


## <a name="device-client"></a>Eszközügyfél

### <a name="connection-and-authenticating-to-iot-hub-device-client"></a>Kapcsolat és az IoT Hub (ügyfél) való hitelesítése
Az IoT hubhoz AMQP használatával csatlakozni egy eszköz használható a [jogcím-alapú biztonság (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) vagy [egyszerű hitelesítési és biztonsági réteg (SASL) hitelesítési](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer).

A következő információkra szükség az eszközügyfél:

| Információ | Érték | 
|-------------|--------------|
| IoT Hub Hostname | `<iot-hub-name>.azure-devices.net` |
| Hozzáférési kulcs | Az eszközhöz társított elsődleges vagy másodlagos kulcsot |
| Közös hozzáférésű Jogosultságkód | Rövid élettartamú SAS a következő formátumban: `SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}` (az aláírás létrehozásához a kód található [Itt](./iot-hub-devguide-security.md#security-token-structure)).


Az alábbi kódrészlet [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python) csatlakozni az IoT hubra küldő hivatkozás segítségével.

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
| Eszközök | Fogadó-hivatkozás | `/devices/<deviceID>/messages/devicebound` | Az eszközök felé irányuló C2D üzenetek érkezik erre a hivatkozásra minden cél eszközre. |
| Eszközök | Küldő hivatkozás | `/devices/<deviceID>messages/events` | Egy eszközről küldött D2C üzeneteket küld a kapcsolaton. |
| Eszközök | Küldő hivatkozás | `/messages/serviceBound/feedback` | C2D üzenet visszajelzés Service kapcsolaton keresztül küldi el az eszközök által. |


### <a name="receive-c2d-commands-device-client"></a>C2D parancsok (ügyfél)
Eszközökre küldött parancsok C2D az ügyfélszámítógépekre érkeznek `/devices/<deviceID>/messages/devicebound` hivatkozásra. Eszközök kötegekben ezeket az üzeneteket fogadni, és használja az üzenet hasznos adatforgalmat, üzenet tulajdonságait, jegyzetek vagy alkalmazás tulajdonságai üzenetben igény szerint.

Az alábbi kódrészlet [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python) C2D üzenetek fogadása egy eszközre.

```python
# ... 
# Create a receive client for the C2D receive link on the device
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

    # Message sequence number in the built-in Event hub
    print('\tx-opt-sequence-number:  ' + str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Telemetria üzenetküldés (ügyfél)
Telemetriai üzeneteket is lehet amqp-n keresztül eszközök által küldött. Az eszköz is megadhat alkalmazástulajdonságokat tartalmazó, illetve különböző üzenet tulajdonságai, például az állapotüzenet-azonosító.

Az alábbi kódrészlet [uAMQP kódtárat a Python](https://github.com/Azure/azure-uamqp-python) D2C üzenet küldése egy eszközről.


```python
# ... 
# Create a send client for the D2C send link on the device
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
* Az AMQP-kapcsolatok a hálózati problémáról, illetve a hitelesítési jogkivonat (létrehozva a kódban) lejárta miatt előfordulhat, hogy tartománnyá. A szolgáltatásügyfél kell kezelni az ilyen körülmények között, és újra létrehozza a kapcsolatot és a hivatkozásokat, ha szükséges. A hitelesítési jogkivonat lejárati esetben az ügyfél is proaktívan megújíthatják meg a kapcsolat legördülő elkerülése érdekében a lejárat előtt a jogkivonatot.
* Néhány esetben az ügyfél képes megfelelően kezeli a hivatkozás átirányítások kell lennie. Ez a művelet kezelése az AMQP ügyfél dokumentációban tájékozódhat.

## <a name="next-steps"></a>További lépések

Az AMQP protokollt kapcsolatos további információkért tekintse meg a [AMQP 1.0-s verziójú specifikáció](http://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Az IoT Hub üzenetküldési kapcsolatos további információkért lásd:

* [Felhőből az eszközre irányuló üzenetek](./iot-hub-devguide-messages-c2d.md)
* [További protokollok támogatása](iot-hub-protocol-gateway.md)
* [MQTT protokoll támogatása](./iot-hub-mqtt-support.md)

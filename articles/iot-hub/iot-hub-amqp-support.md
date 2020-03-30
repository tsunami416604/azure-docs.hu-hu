---
title: Az Azure IoT Hub AMQP-támogatásának ismertetése | Microsoft dokumentumok
description: Fejlesztői útmutató – az IoT Hub-eszköz- és szolgáltatásnéző végpontokhoz az AMQP protokoll használatával csatlakozó eszközök támogatása. Az Azure IoT-eszköz SDK-k beépített AMQP-támogatásával kapcsolatos információkat tartalmazza.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: 7f7e957502419b766f7da63048e8168192ea20da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284784"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Kommunikáció az IoT-központtal az AMQP protokoll használatával

Az Azure IoT Hub támogatja [az OASIS Advanced Message Queuing Protocol (AMQP) 1.0-s verzióját,](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) hogy az eszközre néző és a szolgáltatásfelé irányuló végpontokon keresztül számos funkciót biztosítson. Ez a dokumentum ismerteti az AMQP-ügyfelek használatát egy IoT hubhoz való csatlakozáshoz az IoT Hub funkcióinak használatához.

## <a name="service-client"></a>Szolgáltatási ügyfél

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Csatlakozás és hitelesítés IoT-központhoz (szolgáltatásügyfélhez)

Ha Az AMQP használatával szeretne csatlakozni egy IoT-központhoz, az ügyfél használhatja a [jogcímalapú biztonsági (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) vagy [az egyszerű hitelesítési és biztonsági réteg (SASL) hitelesítést.](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

A szolgáltatásügyfélhez a következő adatok szükségesek:

| Információ | Érték |
|-------------|--------------|
| IoT hub állomásneve | `<iot-hub-name>.azure-devices.net` |
| Kulcs neve | `service` |
| Hozzáférési kulcs | A szolgáltatáshoz társított elsődleges vagy másodlagos kulcs |
| Közös hozzáférésű jogosultságkód | Rövid ideig tartó közös hozzáférésű aláírás `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`a következő formátumban: . Az aláírás létrehozásához a Hozzáférés [szabályozása az IoT Hubhoz című](./iot-hub-devguide-security.md#security-token-structure)témakörben található.

A következő kódrészlet a [Python uAMQP-függvénytárát](https://github.com/Azure/azure-uamqp-python) használja az IoT-központhoz való csatlakozáshoz egy feladói hivatkozáson keresztül.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Felhőből az eszközre irányuló üzenetek meghívása (szolgáltatásügyfél)

A szolgáltatás és az IoT hub, valamint az eszköz és az IoT hub közötti felhőből az eszközre irányuló üzenetváltásról az [IoT-központból küldött üzenetek küldése](iot-hub-devguide-messages-c2d.md)című témakörben olvashat. A szolgáltatásügyfél két hivatkozást használ az üzenetek küldésére és az eszközökről korábban küldött üzenetekre vonatkozó visszajelzésfogadására, az alábbi táblázatban leírtak szerint:

| Létrehozta: | Hivatkozás típusa | Hivatkozás elérési útja | Leírás |
|------------|-----------|-----------|-------------|
| Szolgáltatás | Feladó hivatkozása | `/messages/devicebound` | Az eszközökre szánt felhőből az eszközre irányuló üzeneteket a szolgáltatás erre a hivatkozásra küldi. A hivatkozáson keresztül `To` küldött üzenetek tulajdonsága a céleszköz `/devices/<deviceID>/messages/devicebound`címzettjének kapcsolati útvonalára van állítva, . |
| Szolgáltatás | Vevő hivatkozása | `/messages/serviceBound/feedback` | A szolgáltatás által ezen a kapcsolaton fogadott eszközökről érkező kitöltési, elutasítási és elhagyási visszajelzési üzenetek. A visszajelzési üzenetekről további információt a [Felhőből az eszközre irányuló üzenetek küldése IoT-központból](./iot-hub-devguide-messages-c2d.md#message-feedback)című témakörben talál. |

A következő kódrészlet bemutatja, hogyan hozhat létre felhőből eszközre irányuló üzenetet, és hogyan küldheti el egy eszközre a [Python uAMQP-könyvtárával.](https://github.com/Azure/azure-uamqp-python)

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

A visszajelzés fogadásához a szolgáltatásügyfél létrehoz egy vevőkapcsolatot. A következő kódrészlet bemutatja, hogyan hozhat létre hivatkozást a [Python uAMQP könyvtárának](https://github.com/Azure/azure-uamqp-python)használatával:

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

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

Ahogy az előző kódban is látható, a felhőből az eszközre irányuló visszajelzési üzenetek *alkalmazás-/vnd.microsoft.iothub.feedback.json*tartalomtípussal rendelkeznek. Az üzenet JSON-törzsében található tulajdonságok segítségével következtethet az eredeti üzenet kézbesítési állapotára:

* A `statusCode` visszajelzési törzs kulcsának a következő értékei vannak: *Sikeres*, *Lejárt*, *DeliveryCountExceeded*, *Elutasítva*vagy *Törölve.*

* A `deviceId` visszacsatolási törzs kulcsa rendelkezik a céleszköz azonosítójával.

* A `originalMessageId` visszajelzési törzs kulcsa rendelkezik a szolgáltatás által küldött eredeti felhőből eszközre küldött üzenet azonosítójával. Ezzel a kézbesítési állapottal korrelálhatja a visszajelzést a felhőből az eszközre irányuló üzenetekhez.

### <a name="receive-telemetry-messages-service-client"></a>Telemetriai üzenetek fogadása (szolgáltatásügyfél)

Alapértelmezés szerint az IoT hub tárolja a bevitt eszköz telemetriai üzeneteket egy beépített eseményközpontban. A szolgáltatásügyfél az AMQP protokoll segítségével fogadhatja a tárolt eseményeket.

Erre a célra a szolgáltatás-ügyfél először csatlakoznia kell az IoT hub végponthoz, és kap egy átirányítási címet a beépített eseményközpontokhoz. A szolgáltatásügyfél ezután a megadott címet használja a beépített eseményközponthoz való csatlakozáshoz.

Az ügyfélnek minden lépésben a következő információkat kell bemutatnia:

* Érvényes szolgáltatáshitelesítő adatok (szolgáltatás megosztott hozzáférésű jogosultságjegy-token).

* A fogyasztói csoport partíciójának jól formázott elérési útja, amelyről üzeneteket kíván lekérni. Egy adott fogyasztói csoport és partíció azonosítója esetén `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` az elérési út `$Default`formátuma a következő: (az alapértelmezett fogyasztói csoport a ).

* Egy választható szűrési predikátum a partíció egy kezdőpontjának kijelöléséhez. Ez a predikátum lehet sorszám, eltolás vagy várólistára helyezett időbélyeg.

A következő kódrészlet a [Python uAMQP-könyvtárát](https://github.com/Azure/azure-uamqp-python) használja az előző lépések bemutatására:

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
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

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


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Egy adott eszközazonosító esetén az IoT hub az eszközazonosító kivonatát használja annak meghatározására, hogy melyik partícióban tárolja az üzeneteket. Az előző kódrészlet bemutatja, hogyan érkeznek meg az események egyetlen ilyen partícióról. Azonban vegye figyelembe, hogy egy tipikus alkalmazás gyakran kell letölteni az összes eseményközpont-partícióktárolt eseményeket.

## <a name="device-client"></a>Eszközügyfél

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Csatlakozás és hitelesítés IoT-elosztóhoz (eszközügyfélhez)

Ha Az AMQP használatával szeretne csatlakozni egy IoT-központhoz, az eszköz [jogcímalapú biztonsági (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) vagy [egyszerű hitelesítési és biztonsági réteg (SASL)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer) hitelesítést használhat.

Az eszközügyfélhez a következő adatok szükségesek:

| Információ | Érték |
|-------------|--------------|
| IoT hub állomásneve | `<iot-hub-name>.azure-devices.net` |
| Hozzáférési kulcs | Az eszközhöz társított elsődleges vagy másodlagos kulcs |
| Közös hozzáférésű jogosultságkód | Rövid ideig tartó közös hozzáférésű aláírás `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`a következő formátumban: . Az aláírás létrehozásához a Hozzáférés [szabályozása az IoT Hubhoz című](./iot-hub-devguide-security.md#security-token-structure)témakörben található.

A következő kódrészlet a [Python uAMQP-függvénytárát](https://github.com/Azure/azure-uamqp-python) használja az IoT-központhoz való csatlakozáshoz egy feladói hivatkozáson keresztül.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

Az alábbi hivatkozási útvonalak eszközműveletekként támogatottak:

| Létrehozta: | Hivatkozás típusa | Hivatkozás elérési útja | Leírás |
|------------|-----------|-----------|-------------|
| Eszközök | Vevő hivatkozása | `/devices/<deviceID>/messages/devicebound` | Az eszközökre szánt felhőből az eszközre irányuló üzeneteket ezen a kapcsolaton az egyes céleszközök fogadják. |
| Eszközök | Feladó hivatkozása | `/devices/<deviceID>/messages/events` | Az eszközről küldött eszközről felhőbe irányuló üzeneteket a rendszer ezen a hivatkozáson keresztül küldi el. |
| Eszközök | Feladó hivatkozása | `/messages/serviceBound/feedback` | Az eszközök által ezen a kapcsolaton keresztül a szolgáltatásnak küldött felhőről eszközre küldött üzenetek visszajelzései. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Felhőből az eszközre irányuló parancsok fogadása (eszközügyfél)

Az eszközökre küldött felhőből az eszközre `/devices/<deviceID>/messages/devicebound` küldött parancsok egy hivatkozásra érkeznek. Az eszközök kötegekben fogadhatják ezeket az üzeneteket, és szükség szerint használhatják az üzenetadatok hasznos adatát, az üzenet tulajdonságait, a jegyzeteket vagy az alkalmazás tulajdonságait.

A következő kódrészlet a [Python uAMQP-könyvtárát](https://github.com/Azure/azure-uamqp-python)használja a felhőből az eszközre irányuló üzenetek eszközről az eszközre való fogadásához.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

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
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Telemetriai üzenetek küldése (eszközügyfél)

Telemetriai üzeneteket is küldhet egy eszközről az AMQP használatával. Az eszköz opcionálisan megadhat egy szótárat az alkalmazás tulajdonságairól vagy különböző üzenettulajdonságokról, például az üzenetazonosítóról.

A következő kódrészlet a [Python uAMQP-könyvtárát](https://github.com/Azure/azure-uamqp-python) használja az eszközről a felhőbe irányuló üzenetek küldéséhez.

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

* Az AMQP-kapcsolatok hálózati hiba vagy a (kódban létrehozott) hitelesítési jogkivonat lejárata miatt megszakadhatnak. A szolgáltatásügyfélnek kezelnie kell ezeket a körülményeket, és szükség esetén helyre kell állítania a kapcsolatot és a kapcsolatokat. Ha egy hitelesítési jogkivonat lejár, az ügyfél elkerülheti a kapcsolat csökkenését azáltal, hogy proaktív módon megújítja a jogkivonatot annak lejárta előtt.

* Az ügyfélnek időnként képesnek kell lennie a hivatkozás-átirányítások megfelelő kezelésére. Egy ilyen művelet megértéséhez tekintse meg az AMQP-ügyfél dokumentációját.

## <a name="next-steps"></a>További lépések

Az AMQP protokollról az [AMQP 1.0-s v.0-s specifikációjában olvashat bővebben.](https://www.amqp.org/sites/amqp.org/files/amqp.pdf)

Ha többet szeretne megtudni az IoT Hub-üzenetküldésről, olvassa el a következő témakört:

* [Felhőből eszközre irányuló üzenetek](./iot-hub-devguide-messages-c2d.md)
* [További protokollok támogatása](iot-hub-protocol-gateway.md)
* [A Message Queuing telemetriai átviteli (MQTT) protokoll támogatása](./iot-hub-mqtt-support.md)

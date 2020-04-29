---
title: Eszköz biztonsági üzeneteinek küldése
description: Megtudhatja, hogyan küldhet biztonsági üzeneteket Azure Security Center használatával a IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/30/2020
ms.author: mlottner
ms.openlocfilehash: 4877493982671b1b5db686715ef854f25c2966ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81310991"
---
# <a name="send-security-messages-sdk"></a>Biztonsági üzenetek SDK küldése

Ez a útmutató a IoT szolgáltatás képességeinek Azure Security Center ismerteti, ha úgy dönt, hogy az eszköz biztonsági üzeneteit a IoT-ügynök Azure Security Center használata nélkül gyűjti és küldi el, és elmagyarázza ennek módját.

Ebből az útmutatóból a következőket tanulhatja meg:

> [!div class="checklist"]
> * Biztonsági üzenetek küldése az Azure IoT C SDK használatával
> * Biztonsági üzenetek küldése az Azure IoT C# SDK használatával
> * Biztonsági üzenetek küldése az Azure IoT Python SDK használatával
> * Biztonsági üzenetek küldése az Azure IoT Node. js SDK használatával
> * Biztonsági üzenetek küldése az Azure IoT Java SDK használatával

## <a name="azure-security-center-for-iot-capabilities"></a>A IoT képességeinek Azure Security Center

Azure Security Center a IoT képes feldolgozni és elemezni a biztonsági üzenetek bármilyen típusát, feltéve, hogy az elküldött adatmennyiség megfelel a [IoT-séma Azure Security Centernak](https://aka.ms/iot-security-schemas) , és az üzenet biztonsági üzenetként van beállítva.

## <a name="security-message"></a>Biztonsági üzenet

A IoT-Azure Security Center a következő feltételekkel határozzák meg a biztonsági üzenetet:

- Ha az üzenet el lett küldve az Azure IoT SDK-val
- Ha az üzenet megfelel a [biztonsági üzenet sémájának](https://aka.ms/iot-security-schemas)
- Ha az üzenet a küldés előtt biztonsági üzenetként lett beállítva

Minden biztonsági üzenet tartalmazza a küldő metaadatait, például a `AgentId`, `AgentVersion` `MessageSchemaVersion` és a biztonsági események listáját.
A séma határozza meg a biztonsági üzenet érvényes és szükséges tulajdonságait, beleértve az események típusait is.

> [!NOTE]
> A rendszer figyelmen kívül hagyja az elküldött üzeneteket, amelyek nem felelnek meg a sémának. Győződjön meg arról, hogy az adatküldés megkezdése előtt ellenőrizze a sémát, mivel a rendszer jelenleg nem tárolja a figyelmen kívül hagyott üzeneteket.

> [!NOTE]
> Azok az üzenetek, amelyek nem biztonsági üzenetként lettek beállítva az Azure IoT SDK használatával, nem lesznek átirányítva a IoT-folyamathoz tartozó Azure Security Center.

## <a name="valid-message-example"></a>Érvényes üzenet – példa

Az alábbi példa egy érvényes biztonsági üzenet objektumot mutat be. A példa tartalmazza az üzenet metaadatait és `ProcessCreate` egy biztonsági eseményt.

Ha biztonsági üzenetként van beállítva, és a rendszer elküldte ezt az üzenetet, a IoT Azure Security Center fogja feldolgozni.

```json
"AgentVersion": "0.0.1",
"AgentId": "e89dc5f5-feac-4c3e-87e2-93c16f010c25",
"MessageSchemaVersion": "1.0",
"Events": [
    {
        "EventType": "Security",
        "Category": "Triggered",
        "Name": "ProcessCreate",
        "IsEmpty": false,
        "PayloadSchemaVersion": "1.0",
        "Id": "21a2db0b-44fe-42e9-9cff-bbb2d8fdf874",
        "TimestampLocal": "2019-01-27 15:48:52Z",
        "TimestampUTC": "2019-01-27 13:48:52Z",
        "Payload":
            [
                {
                    "Executable": "/usr/bin/myApp",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "aUser",
                    "CommandLine": "myApp -a -b"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Biztonsági üzenetek küldése

Az Azure [IoT C](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)eszközoldali SDK, az [Azure IOT C# eszközoldali SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), az [Azure IoT Node. js SDK](https://github.com/Azure/azure-iot-sdk-node), az [Azure IoT PYTHON SDK](https://github.com/Azure/azure-iot-sdk-python)vagy az [Azure IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java)használatával küldhet biztonsági üzeneteket a IoT-ügynökkel való Azure Security Center használata *nélkül* .

Ha az eszköz adatait az eszközökről a IoT Azure Security Center történő feldolgozásra szeretné elküldeni, a következő API-k egyikével kell megjelölnie az üzeneteket a megfelelő útválasztáshoz, hogy a IoT feldolgozási folyamathoz Azure Security Center.

Az összes elküldött, még akkor is, ha a megfelelő fejléccel van megjelölve, meg kell felelnie a [IoT-üzenet sémájának Azure Security Center](https://aka.ms/iot-security-schemas).

### <a name="send-security-message-api"></a>Biztonsági üzenet API-nak küldése

A **biztonsági üzenetek küldése** API jelenleg C és C#, Python, Node. js és Java nyelven érhető el.

#### <a name="c-api"></a>C API

```c
bool SendMessageAsync(IoTHubAdapter* iotHubAdapter, const void* data, size_t dataSize) {

    bool success = true;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;

    messageHandle = IoTHubMessage_CreateFromByteArray(data, dataSize);

    if (messageHandle == NULL) {
        success = false;
        goto cleanup;
    }

    if (IoTHubMessage_SetAsSecurityMessage(messageHandle) != IOTHUB_MESSAGE_OK) {
        success = false;
        goto cleanup;
    }

    if (IoTHubModuleClient_SendEventAsync(iotHubAdapter->moduleHandle, messageHandle, SendConfirmCallback, iotHubAdapter) != IOTHUB_CLIENT_OK) {
        success = false;
        goto cleanup;
    }

cleanup:
    if (messageHandle != NULL) {
        IoTHubMessage_Destroy(messageHandle);
    }

    return success;
}

static void SendConfirmCallback(IOTHUB_CLIENT_CONFIRMATION_RESULT result, void* userContextCallback) {
    if (userContextCallback == NULL) {
        //error handling
        return;
    }

    if (result != IOTHUB_CLIENT_CONFIRMATION_OK){
        //error handling
    }
}
```

#### <a name="c-api"></a>C# API

```cs

private static async Task SendSecurityMessageAsync(string messageContent)
{
    ModuleClient client = ModuleClient.CreateFromConnectionString("<connection_string>");
    Message  securityMessage = new Message(Encoding.UTF8.GetBytes(messageContent));
    securityMessage.SetAsSecurityMessage();
    await client.SendEventAsync(securityMessage);
}
```

#### <a name="nodejs-api"></a>Node.js API

```typescript
var Protocol = require('azure-iot-device-mqtt').Mqtt

function SendSecurityMessage(messageContent)
{
  var client = Client.fromConnectionString(connectionString, Protocol);

  var connectCallback = function (err) {
    if (err) {
      console.error('Could not connect: ' + err.message);
    } else {
      var message = new Message(messageContent);
      message.setAsSecurityMessage();
      client.sendEvent(message);
  
      client.on('error', function (err) {
        console.error(err.message);
      });
  
      client.on('disconnect', function () {
        clearInterval(sendInterval);
        client.removeAllListeners();
        client.open(connectCallback);
      });
    }
  };

  client.open(connectCallback);
}
```

#### <a name="python-api"></a>Python API

A Python API használatához telepítenie kell az [Azure-IOT-Device](https://pypi.org/project/azure-iot-device/)csomagot.

A Python API használatakor a biztonsági üzenetet elküldheti a modulon keresztül, vagy az eszközön keresztül az egyedi eszköz vagy modul kapcsolódási karakterláncát használva. Ha a következő Python-parancsfájlt használja például egy eszközön, használja a **IoTHubDeviceClient**-t, és egy modullal használja a **IoTHubModuleClient**.

```python
from azure.iot.device.aio import IoTHubDeviceClient, IoTHubModuleClient
from azure.iot.device import Message

async def send_security_message_async(message_content):
    conn_str = os.getenv("<connection_string>")
    device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)
    await device_client.connect()
    security_message = Message(message_content)
    security_message.set_as_security_message()
    await device_client.send_message(security_message)
    await device_client.disconnect()
```

#### <a name="java-api"></a>Java API

```java
public void SendSecurityMessage(string message)
{
    ModuleClient client = new ModuleClient("<connection_string>", IotHubClientProtocol.MQTT);
    Message msg = new Message(message);
    msg.setAsSecurityMessage();
    EventCallback callback = new EventCallback();
    string context = "<user_context>";
    client.sendEventAsync(msg, callback, context);
}
```

## <a name="next-steps"></a>További lépések

- A IoT-szolgáltatás [áttekintésének](overview.md) Azure Security Center olvasása
- További információ a IoT- [architektúra](architecture.md) Azure Security Center
- A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- A [GYIK](resources-frequently-asked-questions.md) áttekintése
- Ismerje meg, hogyan érheti el a [nyers biztonsági információkat](how-to-security-data-access.md)
- [Javaslatok](concept-recommendations.md) ismertetése
- A [riasztások](concept-security-alerts.md) ismertetése

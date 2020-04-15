---
title: Eszközbiztonsági üzenetek küldése
description: Ismerje meg, hogyan küldhetbiztonsági üzeneteket az Azure Security Center for IoT használatával.
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
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310991"
---
# <a name="send-security-messages-sdk"></a>Biztonsági üzenetek küldése SDK

Ez az útmutató ismerteti az Azure Security Center for IoT szolgáltatás képességeit, amikor úgy dönt, hogy összegyűjti és elküldi az eszköz biztonsági üzeneteket az Azure Security Center for IoT-ügynök használata nélkül, és elmagyarázza, hogyan kell ezt megtenni.

Ebből az útmutatóból a következőket tanulhatja meg:

> [!div class="checklist"]
> * Biztonsági üzenetek küldése az Azure IoT C SDK használatával
> * Biztonsági üzenetek küldése az Azure IoT C# SDK használatával
> * Biztonsági üzenetek küldése az Azure IoT Python SDK használatával
> * Biztonsági üzenetek küldése az Azure IoT-node.js SDK használatával
> * Biztonsági üzenetek küldése az Azure IoT Java SDK használatával

## <a name="azure-security-center-for-iot-capabilities"></a>Az Azure Security Center az IoT-képességekhez

Az Azure Security Center for IoT bármilyen típusú biztonsági üzenetadatot feldolgozhat és elemezhet, amennyiben az elküldött adatok megfelelnek az [Azure Security Center for IoT-sémának,](https://aka.ms/iot-security-schemas) és az üzenet biztonsági üzenetként van beállítva.

## <a name="security-message"></a>Biztonsági üzenet

Az Azure Security Center for IoT a következő feltételekkel definiál egy biztonsági üzenetet:

- Ha az üzenetet az Azure IoT SDK-val küldték
- Ha az üzenet megfelel a [biztonsági üzenetsémának](https://aka.ms/iot-security-schemas)
- Ha az üzenet biztonsági üzenetként lett beállítva a küldés előtt

Minden biztonsági üzenet tartalmazza a feladó metaadatait, például `AgentId`a `AgentVersion`, a, `MessageSchemaVersion` és a biztonsági események listáját.
A séma határozza meg a biztonsági üzenet érvényes és szükséges tulajdonságait, beleértve az események típusait is.

> [!NOTE]
> A sémának meg nem felelő üzeneteket a rendszer figyelmen kívül hagyja. Győződjön meg arról, hogy ellenőrizze a sémát, mielőtt az adatok küldését figyelmen kívül hagyott üzenetekként való indítása jelenleg nem tárolja.

> [!NOTE]
> Az Azure IoT SDK használatával nem biztonsági üzenetként beállított üzenetek nem lesznek átirányítva az Azure Security Center for IoT-folyamathoz.

## <a name="valid-message-example"></a>Példa érvényes üzenetre

Az alábbi példa egy érvényes biztonsági üzenetobjektumot mutat be. A példa az üzenet metaadatait és egy `ProcessCreate` biztonsági eseményt tartalmaz.

Miután beállította a biztonsági üzenetet, és elküldte, ezt az üzenetet az Azure Security Center for IoT dolgozza fel.

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

Biztonsági üzenetek küldése az Azure Security Center for IoT-ügynök használata *nélkül* az [Azure IoT C-eszköz SDK,](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) [Az Azure IoT C# eszköz SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview), [Azure IoT Node.js SDK,](https://github.com/Azure/azure-iot-sdk-node) [Azure IoT Python SDK](https://github.com/Azure/azure-iot-sdk-python)vagy [az Azure IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java)használatával.

Ha az eszközök adatait az Azure Security Center for IoT-hoz történő feldolgozásra elküldi, az alábbi API-k egyikével jelölje meg az üzeneteket az Azure Security Center ioT-feldolgozási folyamathoz történő helyes útválasztáshoz.

Minden elküldött adatnak, még akkor is, ha a megfelelő fejléccel van megjelölve, meg kell felelnie az [Azure Security Center for IoT üzenetsémának](https://aka.ms/iot-security-schemas)is.

### <a name="send-security-message-api"></a>Biztonsági üzenet küldése API

A **biztonsági üzenetek küldése** API jelenleg C és C#, Python, Node.js és Java nyelven érhető el.

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

A Python API használatához telepítenie kell a csomagot [az azure-iot-device.](https://pypi.org/project/azure-iot-device/)

A Python API használatakor elküldheti a biztonsági üzenetet a modulon keresztül vagy az eszközön keresztül az egyedi eszköz vagy modul kapcsolati karakterlánc használatával. Ha a következő Python-parancsfájlpéldát használja egy eszközzel, használja **az IoTHubDeviceClient**programot, és egy modullal használja az **IoTHubModuleClient programot.**

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

- Olvassa el az Azure Security Center for IoT szolgáltatás [áttekintését](overview.md)
- További információ az Azure Security Center for IoT [Architecture szolgáltatásról](architecture.md)
- A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- Olvassa el a [GYIK-et](resources-frequently-asked-questions.md)
- További információ a [nyers biztonsági adatok](how-to-security-data-access.md) eléréséről
- Az [ajánlások megismerése](concept-recommendations.md)
- A [riasztások ismertetése](concept-security-alerts.md)

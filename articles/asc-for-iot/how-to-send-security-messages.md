---
title: Biztonsági üzeneteinek elküldése a IoT Azure Security Center részére | Microsoft Docs
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
ms.date: 07/27/2019
ms.author: mlottner
ms.openlocfilehash: c780eea15b9f064d3279c75ac2f967e8b6099ecb
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596204"
---
# <a name="send-security-messages-sdk"></a>Biztonsági üzenetek SDK küldése

Ez a útmutató a IoT szolgáltatás képességeinek Azure Security Center ismerteti, ha úgy dönt, hogy az eszköz biztonsági üzeneteit a IoT-ügynök Azure Security Center használata nélkül gyűjti és küldi el, és elmagyarázza ennek módját.  

Ebből az útmutatóból a következőket tanulhatja meg: 
> [!div class="checklist"]
> * A biztonsági üzenet küldése API használata a következőhöz:C#
> * A C biztonsági üzenet küldése API használata

## <a name="azure-security-center-for-iot-capabilities"></a>A IoT képességeinek Azure Security Center

Azure Security Center a IoT képes feldolgozni és elemezni a biztonsági üzenetek bármilyen típusát, feltéve, hogy az elküldött adatmennyiség megfelel a [IoT-séma Azure Security Centernak](https://aka.ms/iot-security-schemas) , és az üzenet biztonsági üzenetként van beállítva.

## <a name="security-message"></a>Biztonsági üzenet

A IoT-Azure Security Center a következő feltételekkel határozzák meg a biztonsági üzenetet:
- Ha az üzenet el lett küldve az Azure IoT CC# /SDK-val
- Ha az üzenet megfelel a [biztonsági üzenet sémájának](https://aka.ms/iot-security-schemas)
- Ha az üzenet a küldés előtt biztonsági üzenetként lett beállítva

Minden biztonsági üzenet tartalmazza a küldő metaadatait, például `AgentId`a `MessageSchemaVersion` , `AgentVersion`és a biztonsági események listáját.
A séma határozza meg a biztonsági üzenet érvényes és szükséges tulajdonságait, beleértve az események típusait is.

>[!Note]
> A rendszer figyelmen kívül hagyja az elküldött üzeneteket, amelyek nem felelnek meg a sémának. Győződjön meg arról, hogy az adatküldés megkezdése előtt ellenőrizze a sémát, mivel a rendszer jelenleg nem tárolja a figyelmen kívül hagyott üzeneteket. 

>[!Note]
> Az Azure IoT C/C# SDK használatával nem biztonsági üzenetként megadott üzenetek nem lesznek átirányítva a IoT-folyamathoz tartozó Azure Security Center

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

Biztonsági üzeneteket küldhet a IoT-ügynök Azure Security Center használata nélkül az [Azure C# IoT Device SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) vagy az [Azure IoT C Device SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)használatával.

Ha az eszköz adatait az eszközökről a IoT Azure Security Center történő feldolgozásra szeretné elküldeni, a következő API-k egyikével kell megjelölnie az üzeneteket a megfelelő útválasztáshoz, hogy a IoT feldolgozási folyamathoz Azure Security Center. 

Az összes elküldött, még akkor is, ha a megfelelő fejléccel van megjelölve, meg kell felelnie a [IoT-üzenet sémájának Azure Security Center](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Biztonsági üzenet API-nak küldése

A **biztonsági üzenetek küldése** API jelenleg a C-ben és C#a-ben érhető el.  

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

## <a name="next-steps"></a>További lépések
- A IoT-szolgáltatás áttekintésének [](overview.md) Azure Security Center olvasása
- További információ a IoT- [architektúra](architecture.md) Azure Security Center
- A [szolgáltatás](quickstart-onboard-iot-hub.md) engedélyezése
- A [GYIK](resources-frequently-asked-questions.md) áttekintése
- Ismerje meg, hogyan érheti el a [nyers biztonsági információkat](how-to-security-data-access.md)
- [Javaslatok](concept-recommendations.md) ismertetése
- A [riasztások](concept-security-alerts.md) ismertetése

---
title: Az Azure Security Center a biztonsági üzenetek küldése az IoT-előzetes verzió |} A Microsoft Docs
description: Ismerje meg, hogyan küldhet az Azure Security Center használata az IoT biztonsági üzeneteket.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: a9974fd15ae9c8c420992c3ae1084feebae0f57d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59797346"
---
# <a name="send-security-messages-sdk"></a>Biztonsági üzenetküldés SDK

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez az útmutató ismerteti az Azure Security Center (ASC) az IoT-szolgáltatásfunkciók, ha úgy dönt, hogy összegyűjti és elküldi az eszköz biztonsági üzenetek IoT-ügynök az ASC használata nélkül, és ennek módját ismerteti.  

Ebből az útmutatóból a következőket tanulhatja meg: 
> [!div class="checklist"]
> * A Küldés biztonsági Message API esetébenC#
> * A c nyelvhez készült biztonsági üzenetküldés API használata

## <a name="asc-for-iot-capabilities"></a>Az IoT-képességeik ASC

Az IoT ASC fel és elemezhető a bármilyen típusú biztonsági állapotüzenet-adatokat mindaddig, amíg az elküldött adatok megfelelnek a [ASC IoT-séma](https://aka.ms/iot-security-schemas) és az üzenet van beállítva biztonsági üzenetnek számít.

## <a name="security-message"></a>Biztonsági figyelmeztetés

Az IoT ASC pomocí následujících kritérií biztonsági üzenetet határozza meg:
- Ha az üzenet el lett küldve, az Azure IoT C /C# SDK
- Ha az üzenet megfelel-e a [biztonsági üzenet séma](https://aka.ms/iot-security-schemas)
- Ha az üzenet elküldése előtt biztonsági üzenetnek számít lett beállítva.

Minden egyes biztonsági üzenet például a metaadatokat, a küldő tartalmaz `AgentId`, `AgentVersion`, `MessageSchemaVersion` és a biztonsági események listája.
A séma meghatározza többek között a különböző események biztonsági üzenet érvényes és a szükséges tulajdonságait.

[!NOTE]
> Üzenetek küldése, amelyek nem felelnek meg a séma figyelmen kívül hagyja. Ellenőrizze, hogy ellenőrizze a sémát, mielőtt elindítaná a adatokat küldő, figyelmen kívül hagyott üzenetek jelenleg nem kerülnek. 
> Üzenetek küldése, amelyek nincsenek beállítva az Azure IoT C használatával biztonsági üzenetnek számít /C# SDK nem továbbítja az IoT-folyamat az ASC-hez

## <a name="valid-message-example"></a>Érvényes üzenetadatbázis-példa

Az alábbi példa bemutatja egy érvényes biztonsági üzenet objektumot. A példa tartalmazza a üzenet metaadatok és a egy `ProcessCreate` biztonsági esemény.

Beállítása után a biztonsági üzenetnek számít, és küldi el, ez az üzenet fog feldolgozni az ASC az IoT.

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
                    "Executable": "/usr/bin/echo",
                    "ProcessId": 11750,
                    "ParentProcessId": 1593,
                    "UserName": "nginx",
                    "CommandLine": "./backup .htaccess"
                }
            ]
    }
]
```

## <a name="send-security-messages"></a>Biztonsági üzenetek küldéséhez 

Biztonsági üzeneteket küldhet az ASC használata az IoT-ügynök használatával nélkül a [Azure IoT C# eszközoldali SDK](https://github.com/Azure/azure-iot-sdk-csharp/tree/preview) vagy [Azure IoT C eszközoldali SDK](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview).

Szeretne küldeni az eszközön lévő adatokat az eszközökről, az IoT ASC általi feldolgozáshoz, egyet a következő API-k való megjelöléséhez használja a megfelelő útválasztás az ASC-hez az IoT-feldolgozási folyamat üzeneteket. Ezzel a módszerrel küldött üzenetek feldolgozása lesz, és a rendszer az IoT, mind az IoT Hub vagy az Azure Security Center security insights ASC belül jelenik meg. 

Az összes elküldött adatokat, még akkor is, ha a helyes fejlécben jelölése is meg kell felelnie a [ASC IoT-üzenet séma](https://aka.ms/iot-security-schemas). 

### <a name="send-security-message-api"></a>Security API-üzenet küldése

A **biztonsági üzenetküldés** API jelenleg érhető el a C és C#.  

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
- Olvassa el az IoT-szolgáltatást az ASC [áttekintése](overview.md)
- További információ az ASC a IoT [architektúra](architecture.md)
- Engedélyezze a [szolgáltatás](quickstart-onboard-iot-hub.md)
- Olvassa el a [– gyakori kérdések](resources-frequently-asked-questions.md)
- Ismerje meg, hogyan eléréséhez [nyers biztonsági adatok](how-to-security-data-access.md)
- Megismerheti [javaslatok](concept-recommendations.md)
- Megismerheti [riasztások](concept-security-alerts.md)

---
title: A biztonsági üzenetek küldése az ASC-hez az IoT-előzetes verzió |} A Microsoft Docs
description: Megtudhatja, hogyan küldhet az ASC használata az IoT biztonsági üzeneteket.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: c611bb5c-b503-487f-bef4-25d8a243803d
ms.service: ascforiot
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 3daef0bfb2b9108e2b3c28ca0f6975a6f5768e92
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541567"
---
# <a name="tutorial-send-security-messages-sdk"></a>Oktatóanyag: Biztonsági üzenetküldés SDK

> [!IMPORTANT]
> Az IoT ASC jelenleg nyilvános előzetes verzióban érhető el.
> Ez az előnézeti verzió nélkül egy szolgáltatói szerződést, és nem javasolt éles számítási feladatok esetében. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ebben az oktatóanyagban ASC ismerteti az IoT-adatok elemzési képességeket biztosítanak, összegyűjtése és az eszköz biztonsági üzenetek küldése az IoT-ügynök az ASC használata nélkül kiválasztásakor, és ismerteti, hogyan.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni: 
> [!div class="checklist"]
> * A Küldés biztonsági Message API esetébenC#
> * A c nyelvhez készült biztonsági üzenetküldés API használata

## <a name="asc-for-iot"></a>Az IoT ASC 

Az IoT ASC fel és elemezhető a bármilyen típusú biztonsági állapotüzenet-adatokat mindaddig, amíg az elküldött adatok megfelelnek a [IoT-séma ASC](https://github.com/Azure/ASC-for-IoT-Schemas). 

## <a name="send-security-messages"></a>Biztonsági üzenetek küldéséhez 

Biztonsági üzeneteket küldhet az ASC használata az IoT-ügynök használatával nélkül a [Azure IoT eszközoldali SDK-t](https://github.com/Azure/azure-iot-sdk-csharp).

Szeretne küldeni az eszközön lévő adatokat az eszközökről, az IoT ASC általi feldolgozáshoz, egyet a következő API-k való megjelöléséhez használja a megfelelő útválasztás az ASC-hez az IoT-feldolgozási folyamat üzeneteket. Ezzel a módszerrel küldött üzenetek feldolgozása lesz, és a rendszer az IoT, mind az IoT Hub vagy az Azure Security Center security insights ASC belül jelenik meg. 

Az összes elküldött adatokat, még akkor is, ha a helyes fejlécben jelölése is meg kell felelnie a [ASC IoT-üzenet séma](https://github.com/Azure/ASC-for-IoT-Schemas). 

> [!NOTE]
> Üzenetek küldése, amelyek nem felelnek meg a séma figyelmen kívül hagyja. Ellenőrizze, hogy ellenőrizze a sémát, mielőtt elindítaná a adatokat küldő, figyelmen kívül hagyott üzenetek jelenleg nem kerülnek. 

### <a name="send-security-message-api"></a>Security API-üzenet küldése

A **biztonsági üzenetküldés** API jelenleg érhető el a C és C#.  

#### <a name="c-api"></a>C# API

```cs
private static async Task SendSecurityMessageAsync()
{
    string messageContent = "Security Data";
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
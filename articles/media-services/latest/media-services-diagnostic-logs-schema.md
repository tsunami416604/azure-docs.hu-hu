---
title: Az Azure Media Services diagnosztikai naplók sémák – Azure
description: Ez a cikk bemutatja az Azure Media Services-diagnosztikai naplók sémák.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2019
ms.author: juliako
ms.openlocfilehash: 394370738bc7996a221300540e68404986d91310
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58850625"
---
# <a name="diagnostic-logs-schemas"></a>Diagnosztikai naplók sémái

[Az Azure Monitor](../../azure-monitor/overview.md) lehetővé teszi, hogy a figyelő metrikák és diagnosztikai naplók, amelyek segítenek megérteni, hogyan az alkalmazások hajt végre. Figyelheti a Media Services diagnosztikai naplók és riasztások és értesítések a gyűjtött metrikák és naplók létrehozása. A naplókat elküldheti [Azure Storage](https://azure.microsoft.com/services/storage/), azokat a stream [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), és exportálhatja őket [Log Analytics](https://azure.microsoft.com/services/log-analytics/), vagy használja a 3. fél által nyújtott szolgáltatások.

Részletes információkért lásd: [Azure Monitor-metrikák](../../azure-monitor/platform/data-platform.md) és [Azure Monitor-diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md).

Ez a cikk bemutatja a Media Services diagnosztikai naplók sémák.

## <a name="top-level-diagnostic-logs-schema"></a>Legfelső szintű diagnosztikai naplók séma

A legfelső szintű diagnosztikai naplók séma részletes ismertetését lásd: [támogatott szolgáltatások, sémákat és kategóriák az Azure diagnosztikai naplók](../../azure-monitor/platform/tutorial-dashboards.md).

## <a name="key-delivery-log-schema"></a>Kulcskézbesítési séma

### <a name="properties"></a>Tulajdonságok

Ezek a tulajdonságok csak a kulcskézbesítési séma vonatkoznak.

|Name (Név)|Leírás|
|---|---|
|keyId|A kért kulcs azonosítója.|
|keyType|A következő értékek egyike lehet: "Törlés" (nincs titkosítás), "FairPlay", "PlayReady" vagy "Widevine".|
|PolicyName|Az Azure Resource Manager a szabályzat neve.|
|tokenType|A jogkivonat típusa.|
|statusMessage|Az állapotüzenet tartalmazza.|

### <a name="examples"></a>Példák

A kulcskézbesítési kérelmek séma tulajdonságai.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

## <a name="next-steps"></a>További lépések

[A Media Services-metrikák és diagnosztikai naplók figyelése](media-services-metrics-diagnostic-logs.md)

---
title: Az Azure Media Services diagnosztikai naplóinak sémájai – Azure
description: Ez a cikk bemutatja az Azure Media Services diagnosztikai naplók sémák.
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
ms.openlocfilehash: 37baed076ca074c1d558af36649e90959a0034c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75750870"
---
# <a name="diagnostic-logs-schemas"></a>Diagnosztikai naplók sémái

[Az Azure Monitor](../../azure-monitor/overview.md) lehetővé teszi, hogy a metrikák és diagnosztikai naplók, amelyek segítenek megérteni, hogyan teljesítenek az alkalmazások. Figyelheti a Media Services diagnosztikai naplókat, és riasztásokat és értesítéseket hozhat létre az összegyűjtött metrikákhoz és naplókhoz. Naplókat küldhet az [Azure Storage-ba,](https://azure.microsoft.com/services/storage/)streamelheti őket az [Azure Event Hubs-ba,](https://azure.microsoft.com/services/event-hubs/)és exportálhatja őket a [Log Analytics](https://azure.microsoft.com/services/log-analytics/)szolgáltatásba, vagy használhatja a külső szolgáltatásokat.

Részletes információt az [Azure Monitor metrikák](../../azure-monitor/platform/data-platform.md) és az Azure Monitor diagnosztikai naplók című [témakörben talál.](../../azure-monitor/platform/platform-logs-overview.md)

Ez a cikk a Media Services diagnosztikai naplóinak sémairől ismertet.

## <a name="top-level-diagnostic-logs-schema"></a>Legfelső szintű diagnosztikai naplók sémája

A legfelső szintű diagnosztikai naplók sémájának részletes leírását lásd: [Támogatott szolgáltatások, sémák és kategóriák az Azure diagnosztikai naplók.](../../azure-monitor/platform/tutorial-dashboards.md)

## <a name="key-delivery-log-schema"></a>Kulcskézbesítési napló sémája

### <a name="properties"></a>Tulajdonságok

Ezek a tulajdonságok a kulcskézbesítési napló sémára jellemzőek.

|Név|Leírás|
|---|---|
|keyId|A kért kulcs azonosítója.|
|kulcstípusa|Ez lehet a következő értékek egyike: "Clear" (nincs titkosítás), "FairPlay", "PlayReady" vagy "Widevine".|
|házirendnév|A szabályzat Azure Resource Manager-neve.|
|tokenType|A token típusa.|
|statusMessage|Az állapotüzenet.|

### <a name="examples"></a>Példák

A kulcskézbesítési kérelmek sémájának tulajdonságai.

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

## <a name="additional-notes"></a>További megjegyzések

* A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="next-steps"></a>További lépések

[Media Services-mérőszámok és diagnosztikai naplók figyelése](media-services-metrics-diagnostic-logs.md)

---
title: Azure Event Grid erőforráscsoport-eseménysémája
description: Az Azure Event Grid erőforráscsoport-eseményeihez megadott tulajdonságok ismertetése
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/12/2019
ms.author: spelluru
ms.openlocfilehash: 6cbfc06f380d7c4818ca82e858c23bb18849fb7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561693"
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Azure Event Grid eseménysémája erőforráscsoportokhoz

Ez a cikk az erőforráscsoport-események tulajdonságait és sémáját tartalmazza.Az eseménysémák bemutatása az [Azure Event Grid eseménysémájában.](event-schema.md)

Az Azure-előfizetések és erőforráscsoportok ugyanazokat az eseménytípusokat bocsátják ki. Az eseménytípusok erőforrás-változásokhoz vagy műveletekhez kapcsolódnak. Az elsődleges különbség az, hogy az erőforráscsoportok eseményeket bocsátanak ki az erőforráscsoporton belüli erőforrásokhoz, és az Azure-előfizetések eseményeket bocsátanak ki az erőforrásokhoz az előfizetés ben.

Az erőforrásesemények a put, patch, posta és DELETE `management.azure.com`műveletekhez jönnek létre, amelyeket a rendszer a rendszernek küld. Get műveletek nem hoznak létre eseményeket. Az adatsíkra küldött műveletek `myaccount.blob.core.windows.net`(például ) nem hoznak létre eseményeket. A műveletesemények eseményadatokat biztosítanak a műveletekhez, például egy erőforrás kulcsainak listázásához.

Amikor előfizet egy erőforráscsoport eseményeire, a végpont az adott erőforráscsoport összes eseményét megkapja. Az események tartalmazhatnak megtekinteni kívánt eseményeket, például egy virtuális gép frissítését, de olyan eseményeket is, amelyek esetleg nem fontosak az Ön számára, például egy új bejegyzés írása a központi telepítési előzményekben. A végponton az összes eseményt megkaphatja, és olyan kódot írhat, amely feldolgozza a kezelni kívánt eseményeket. Vagy beállíthat egy szűrőt az esemény-előfizetés létrehozásakor.

Az események programozott kezeléséhez az eseményeket az `operationName` érték vizsgálatával rendezheti. Előfordulhat például, hogy `Microsoft.Compute/virtualMachines/write` `Microsoft.Storage/storageAccounts/write`az eseményvégpont csak a vagy a.

Az esemény tárgya a művelet célját képező erőforrás erőforrásazonosítója. Egy erőforrás eseményeinek szűréséhez adja meg az erőforrás-azonosítót az esemény-előfizetés létrehozásakor.  Erőforrástípus szerinti szűréshez használja a következő formátumú értéket:`/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

A mintaparancsfájlok és oktatóanyagok listáját az [Erőforráscsoport eseményforrása (Erőforráscsoport eseményforrása) ismerteti.](event-sources.md#resource-groups)

## <a name="available-event-types"></a>Elérhető eseménytípusok

Az erőforráscsoportok felügyeleti eseményeket bocsátanak ki az Azure Resource Managerből, például amikor virtuális gép jön létre, vagy egy tárfiókot törölnek.

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionMégs | Az erőforráson végzett művelet megszakításakor előáll. |
| Microsoft.Resources.ResourceActionFailure | Ha az erőforráson végrehajtott művelet sikertelen. |
| Microsoft.Resources.ResourceActionSuccess | Ha az erőforrás-művelet sikeres. |
| Microsoft.Resources.ResourceDeleteCancelMégs | A törlési művelet megszakításakor előáll. Ez az esemény akkor következik be, amikor egy sablon központi telepítését megszakítják. |
| Microsoft.Resources.ResourceDeleteFailureFailure | Ha a törlési művelet sikertelen, a rendszer előáll. |
| Microsoft.Resources.ResourceDeleteSuccess | A törlési művelet sikeresse esetén előtörve. |
| Microsoft.Resources.ResourceWriteMégs | A létrehozási vagy frissítési művelet megszakításakor keletkezik. |
| Microsoft.Resources.ResourceWriteFailure | A létrehozási vagy frissítési művelet sikertelensítése esetén keletkezik. |
| Microsoft.Resources.ResourceWriteSuccess | Ha a létrehozási vagy frissítési művelet sikeres. |

## <a name="example-event"></a>Példa esemény

A következő példa egy **ResourceWriteSuccess** esemény sémáját mutatja be. Ugyanaz a séma használatos a **ResourceWriteFailure** és a `eventType` **ResourceWriteCancel** eseményekhez, amelyek különböző értékeit használják.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceWriteSuccess",
  "eventTime": "2018-07-19T18:38:04.6117357Z",
  "id": "4db48cba-50a2-455a-93b4-de41a3b5b7f6",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/write",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "{expiration}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourcegroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/write",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

A következő példa egy **ResourceDeleteSuccess** esemény sémáját mutatja be. Ugyanaz a séma használatos a **ResourceDeleteFailure** és a `eventType` **ResourceDeleteCancel** eseményekhez, amelyek különböző értékeit használják.

```json
[{
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2018-07-19T19:24:12.763881Z",
  "id": "19a69642-1aad-4a96-a5ab-8d05494513ce",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
      "action": "Microsoft.Storage/storageAccounts/delete",
      "evidence": {
        "role": "Subscription Admin"
      }
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "_claim_names": "{\"groups\":\"src1\"}",
      "_claim_sources": "{\"src1\":{\"endpoint\":\"{URI}\"}}",
      "http://schemas.microsoft.com/claims/authnclassreference": "1",
      "aio": "{token}",
      "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "{ID}",
      "e_exp": "262800",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "{last-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "{first-name}",
      "ipaddr": "{IP-address}",
      "name": "{full-name}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "onprem_sid": "{ID}",
      "puid": "{ID}",
      "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",
      "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "{user-name}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "{user-name}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "DELETE",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}?api-version=2018-02-01"
    },
    "resourceProvider": "Microsoft.Storage",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Storage/storageAccounts/{storage-name}",
    "operationName": "Microsoft.Storage/storageAccounts/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}"
}]
```

A következő példa egy **ResourceActionSuccess** esemény sémáját mutatja be. Ugyanaz a séma használatos a **ResourceActionFailure** és a `eventType` **ResourceActionCancel** eseményekhez, amelyek különböző értékeit használják.

```json
[{   
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
  "eventType": "Microsoft.Resources.ResourceActionSuccess",
  "eventTime": "2018-10-08T22:46:22.6022559Z",
  "id": "{ID}",
  "data": {
    "authorization": {
      "scope": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
      "action": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
      "evidence": {
        "role": "Contributor",
        "roleAssignmentScope": "/subscriptions/{subscription-id}",
        "roleAssignmentId": "{ID}",
        "roleDefinitionId": "{ID}",
        "principalId": "{ID}",
        "principalType": "ServicePrincipal"
      }     
    },
    "claims": {
      "aud": "{audience-claim}",
      "iss": "{issuer-claim}",
      "iat": "{issued-at-claim}",
      "nbf": "{not-before-claim}",
      "exp": "{expiration-claim}",
      "aio": "{token}",
      "appid": "{ID}",
      "appidacr": "2",
      "http://schemas.microsoft.com/identity/claims/identityprovider": "{URL}",
      "http://schemas.microsoft.com/identity/claims/objectidentifier": "{ID}",
      "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "{ID}",       "http://schemas.microsoft.com/identity/claims/tenantid": "{ID}",
      "uti": "{ID}",
      "ver": "1.0"
    },
    "correlationId": "{ID}",
    "httpRequest": {
      "clientRequestId": "{ID}",
      "clientIpAddress": "{IP-address}",
      "method": "POST",
      "url": "https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey/listKeys?api-version=2017-04-01"
    },
    "resourceProvider": "Microsoft.EventHub",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventHub/namespaces/{namespace}/AuthorizationRules/RootManageSharedAccessKey",
    "operationName": "Microsoft.EventHub/namespaces/AuthorizationRules/listKeys/action",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}"
  },
  "dataVersion": "2",
  "metadataVersion": "1",
  "topic": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}" 
}]
```

## <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény legfelső szintű adatokat rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforráselérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| Tárgy | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| id | sztring | Az esemény egyedi azonosítója |
| data | objektum | Erőforráscsoport eseményadatai. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| engedélyezés | objektum | A művelethez kért engedély. |
| Azt állítja | objektum | A követelések tulajdonságai. További információ: [JWT specifikáció](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | sztring | Hibaelhárítási műveletazonosító. |
| httpRequest (Kérés) | objektum | A művelet részletei. Ez az objektum csak meglévő erőforrás frissítésekor vagy egy erőforrás törlésekor jelenik meg. |
| resourceProvider | sztring | A művelet erőforrás-szolgáltatója. |
| resourceUri | sztring | A műveletben lévő erőforrás URI-ja. |
| operationName | sztring | A művelet, amit elvettek. |
| status | sztring | A művelet állapota. |
| subscriptionId | sztring | Az erőforrás előfizetési azonosítója. |
| tenantId | sztring | Az erőforrás bérlői azonosítója. |

## <a name="next-steps"></a>További lépések

* Az Azure Event Grid bemutatása a [Mi az eseményrács?](overview.md)
* Az Azure Event Grid-előfizetés ek létrehozásáról az [Event Grid-előfizetésséma](subscription-creation-schema.md)című témakörben talál további információt.

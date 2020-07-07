---
title: Azure-előfizetés Event Grid forrásként
description: Az előfizetési eseményekhez megadott tulajdonságokat ismerteti Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: fa88fe4e05ac968588a65d67a2f075bcae48ba7a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81393229"
---
# <a name="azure-subscription-as-an-event-grid-source"></a>Azure-előfizetés Event Grid-forrásként

Ez a cikk az Azure-előfizetési eseményekhez tartozó tulajdonságokat és sémát ismerteti.Az események sémáinak bemutatása: [Azure Event Grid Event Schema](event-schema.md).

Az Azure-előfizetések és-erőforráscsoportok azonos típusú eseményeket bocsátanak ki. Az események típusa erőforrás-változásokhoz vagy műveletekhez kapcsolódik. Az elsődleges különbség az, hogy az erőforráscsoportok az erőforráscsoport erőforrásai számára bocsátanak ki eseményeket, és az Azure-előfizetések eseményeket bocsátanak ki az előfizetésben található erőforrásokhoz.

Az erőforrás-események a következőre küldött PUT, PATCH, POST és DELETE műveletekhez jönnek létre: `management.azure.com` . A lekérési műveletek nem hoznak létre eseményeket. Az adatsíkon (például) elküldett műveletek `myaccount.blob.core.windows.net` nem hoznak létre eseményeket. A műveleti események olyan műveletekhez biztosítanak eseményeket, mint például az erőforrások kulcsainak listázása.

Amikor előfizet egy Azure-előfizetés eseményeire, a végpont megkapja az előfizetés összes eseményét. Az események tartalmazhatják a megtekinteni kívánt eseményt, például a virtuális gépek frissítését, de olyan eseményeket is, amelyek talán nem fontosak Önnek, például egy új bejegyzés írásakor az üzembe helyezési előzményekben. Az összes eseményt megtekintheti a végponton, és írhat olyan kódot, amely feldolgozza a kezelni kívánt eseményeket. Vagy beállíthat egy szűrőt az esemény-előfizetés létrehozásakor.

Az események programozott kezeléséhez az értékeket megtekintve rendezheti az eseményeket `operationName` . Előfordulhat például, hogy az esemény-végpont csak a vagy a műveletekkel kapcsolatos eseményeket dolgozza fel `Microsoft.Compute/virtualMachines/write` `Microsoft.Storage/storageAccounts/write` .

Az esemény tárgya a művelet céljaként megadott erőforrás azonosítója. Az adott erőforrás eseményeinek szűréséhez adja meg az erőforrás-azonosítót az esemény-előfizetés létrehozásakor. Az erőforrástípus alapján történő szűréshez használja a következő formátumban megadott értéket:`/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`


## <a name="event-grid-event-schema"></a>Event Grid-eseményséma

### <a name="available-event-types"></a>Elérhető események típusai

Az Azure-előfizetések Azure Resource Manager felügyeleti eseményeket bocsátanak ki, például egy virtuális gép létrehozásakor vagy egy Storage-fiók törlésekor.

| Eseménytípus | Leírás |
| ---------- | ----------- |
| Microsoft. Resources. ResourceActionCancel | Az erőforráson végrehajtandó művelet megszakításakor következik be. |
| Microsoft. Resources. ResourceActionFailure | Akkor következik be, amikor az erőforrás művelete meghiúsul. |
| Microsoft. Resources. ResourceActionSuccess | Akkor következik be, amikor az erőforrás művelete sikeres. |
| Microsoft. Resources. ResourceDeleteCancel | A törlési művelet megszakításakor következik be. Ez az esemény akkor fordul elő, ha egy sablon központi telepítése meg lett szakítva. |
| Microsoft. Resources. ResourceDeleteFailure | A törlési művelet sikertelensége esetén következik be. |
| Microsoft. Resources. ResourceDeleteSuccess | A törlési művelet sikeressége után következik be. |
| Microsoft. Resources. ResourceWriteCancel | A létrehozási vagy frissítési művelet megszakításakor következik be. |
| Microsoft. Resources. ResourceWriteFailure | A létrehozási vagy frissítési művelet sikertelensége esetén következik be. |
| Microsoft. Resources. ResourceWriteSuccess | A létrehozási vagy frissítési művelet sikeressége után következik be. |

### <a name="example-event"></a>Példa eseményre

Az alábbi példa egy **ResourceWriteSuccess** -esemény sémáját mutatja be. Ugyanezt a sémát használják a **ResourceWriteFailure** és a **ResourceWriteCancel** különböző értékekkel rendelkező események esetében `eventType` .

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
  "topic": "/subscriptions/{subscription-id}"
}]
```

Az alábbi példa egy **ResourceDeleteSuccess** -esemény sémáját mutatja be. Ugyanezt a sémát használják a **ResourceDeleteFailure** és a **ResourceDeleteCancel** különböző értékekkel rendelkező események esetében `eventType` .

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
  "topic": "/subscriptions/{subscription-id}"
}]
```

Az alábbi példa egy **ResourceActionSuccess** -esemény sémáját mutatja be. Ugyanezt a sémát használják a **ResourceActionFailure** és a **ResourceActionCancel** különböző értékekkel rendelkező események esetében `eventType` .

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
  "topic": "/subscriptions/{subscription-id}" 
}]
```

### <a name="event-properties"></a>Esemény tulajdonságai

Egy esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | Az eseményforrás teljes erőforrás-elérési útja. Ez a mező nem írható. Az értéket az Event Grid adja meg. |
| tulajdonos | sztring | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Az eseményforráshoz felvett eseménytípusok egyike. |
| eventTime | sztring | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | objektum | Előfizetési eseményekre vonatkozó adatértékek. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

Az adatobjektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| engedélyezés | objektum | A művelethez szükséges engedély. |
| jogcímek | objektum | A jogcímek tulajdonságai. További információ: JWT- [specifikáció](https://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | sztring | Egy műveleti azonosító a hibaelhárításhoz. |
| httpRequest | objektum | A művelet részletei. Ez az objektum csak meglévő erőforrás frissítésekor vagy erőforrás törlésekor szerepel. |
| resourceProvider | sztring | A művelet erőforrás-szolgáltatója. |
| resourceUri | sztring | Az erőforrás URI-ja a műveletben. |
| operationName | sztring | A végrehajtott művelet. |
| status | sztring | A művelet állapota. |
| subscriptionId | sztring | Az erőforrás előfizetés-azonosítója. |
| tenantId | sztring | Az erőforrás bérlői azonosítója. |

## <a name="tutorials-and-how-tos"></a>Oktatóanyagok és útmutatók
|Cím |Leírás  |
|---------|---------|
| [Oktatóanyag: Azure Automation a Event Grid és a Microsoft Teams szolgáltatással](ensure-tags-exists-on-new-virtual-machines.md) |Hozzon létre egy virtuális gépet, amely eseményt küld. Az esemény elindítja a virtuális gépet címkéző Automation-runbook, és elindítja a Microsoft Teams Channel szolgáltatásnak küldött üzenetet. |
| [Útmutató: az eseményekre való előfizetés a portálon keresztül](subscribe-through-portal.md) | Az Azure-előfizetések eseményeire való feliratkozáshoz használja a portált. |
| [Azure CLI: Feliratkozás az Azure-előfizetés eseményeire](./scripts/event-grid-cli-azure-subscription.md) |Parancsfájl, amely Event Grid-előfizetést hoz létre egy Azure-előfizetéshez, és eseményeket küld egy webhooknak. |
| [PowerShell: Feliratkozás az Azure-előfizetés eseményeire](./scripts/event-grid-powershell-azure-subscription.md)| Parancsfájl, amely Event Grid-előfizetést hoz létre egy Azure-előfizetéshez, és eseményeket küld egy webhooknak. |

## <a name="next-steps"></a>További lépések

* A Azure Event Grid bemutatása: [Mi az Event Grid?](overview.md)
* Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid előfizetés sémája](subscription-creation-schema.md).

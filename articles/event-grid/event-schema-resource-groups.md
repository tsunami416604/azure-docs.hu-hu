---
title: Az Azure Event Grid erőforrás csoport eseménysémája
description: Ismerteti a szolgáltatást az Azure Event Grid erőforrás-csoport események tulajdonságait
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: reference
ms.date: 10/12/2018
ms.author: tomfitz
ms.openlocfilehash: ffc9eba251cbf4d9e2542791d90943ecdd1a972a
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310572"
---
# <a name="azure-event-grid-event-schema-for-resource-groups"></a>Az erőforráscsoportok az Azure Event Grid eseménysémája

Ez a cikk a tulajdonságok és a séma biztosít erőforrás-csoport események. Eseménysémák szeretné megismerni, lásd: [Azure Event Grid-esemény séma](event-schema.md).

Az Azure-előfizetések és -erőforráscsoportok gridre bocsáthatja ki az azonos esemény típusú. Az eseménytípusok kapcsolatos műveletek és erőforrás-módosítások. Az elsődleges különbség, hogy erőforráscsoportok gridre bocsáthatja ki az eseményeket az erőforrások az erőforráscsoporton belül, és az Azure-előfizetések eseményeire a PowerShell erőforrások küldik az előfizetésből.

Erőforrás-események PUT, PATCH, bejegyzés jön létre, és a törlési műveletek küldött `management.azure.com`. GET műveleteket nem hoz létre az eseményeket. Az adatsík küldött műveletek (például `myaccount.blob.core.windows.net`) események nem hoz létre. A műveleti események eseményadatok műveletek, mint az erőforrás kulcsainak listázása adja meg.

Amikor előfizet egy erőforráscsoport eseményeire, a végpont kapja meg az erőforráscsoport összes eseményt. Az események eseményt szeretne látni, például egy virtuális gép frissítése, de események, amelyek esetleg nem fontos számunkra, például az írt új bejegyzést az üzembe helyezési előzmények tartalmazhatnak. Az összes esemény fogadása a végponton, és dolgozza fel a kezelni kívánt kód írására. Másik lehetőségként beállíthatja a szűrőt az esemény-előfizetés létrehozásakor.

Események programozott módon kezelje, rendezheti események megtekintésével a `operationName` értéket. Például az esemény-végpont lehet, hogy csak feldolgozni a műveletek eseményeit, amelyek egyenlőnek kell lennie `Microsoft.Compute/virtualMachines/write` vagy `Microsoft.Storage/storageAccounts/write`.

Az esemény áll, akkor az erőforrás, amely a művelet céljaként megadott erőforrás-Azonosítóját. Erőforrás események szűréséhez állítsa be, adja meg, hogy az erőforrás létrehozásakor az esemény-előfizetés AZONOSÍTÓJÁT.  Szűrés erőforrástípus szerint, használja az értéket a következő formátumban: `/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.Compute/virtualMachines`

Mintaszkriptek és oktatóanyagok listáját lásd: [erőforrás-csoport eseményforrás](event-sources.md#resource-groups).

## <a name="available-event-types"></a>Rendelkezésre álló események típusai

Erőforráscsoportok gridre bocsáthatja ki felügyeleti események Azure Resource Manager, például egy virtuális gép létrehozásakor, vagy a tárfiókot törölték.

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Resources.ResourceActionCancel | Jelenik meg, ha az erőforrás a művelet meg lett szakítva. |
| Microsoft.Resources.ResourceActionFailure | Jelenik meg, ha az erőforrás a művelet sikertelen lesz. |
| Microsoft.Resources.ResourceActionSuccess | Jön létre, ha az erőforrás a művelet sikeres. |
| Microsoft.Resources.ResourceDeleteCancel | Következik be, mikor törölje a művelet meg lett szakítva. Ez az esemény történik, ha a sablon központi telepítés meg lett szakítva. |
| Microsoft.Resources.ResourceDeleteFailure | Következik be, mikor törlése művelet sikertelen. |
| Microsoft.Resources.ResourceDeleteSuccess | Következik be, mikor-törlési művelet sikeres. |
| Microsoft.Resources.ResourceWriteCancel | Következik be, mikor hozzon létre vagy frissítési művelet meg lett szakítva. |
| Microsoft.Resources.ResourceWriteFailure | Következik be, mikor létrehozása vagy frissítése a művelet sikertelen lesz. |
| Microsoft.Resources.ResourceWriteSuccess | Következik be, mikor hozzon létre vagy frissítési művelet sikeres. |

## <a name="example-event"></a>Példa esemény

Az alábbi példa bemutatja a sémában egy **ResourceWriteSuccess** esemény. Ugyanazzal a sémával használt **ResourceWriteFailure** és **ResourceWriteCancel** eltérő értékek az események `eventType`.

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

Az alábbi példa bemutatja a sémában egy **ResourceDeleteSuccess** esemény. Ugyanazzal a sémával használt **ResourceDeleteFailure** és **ResourceDeleteCancel** eltérő értékek az események `eventType`.

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

Az alábbi példa bemutatja a sémában egy **ResourceActionSuccess** esemény. Ugyanazzal a sémával használt **ResourceActionFailure** és **ResourceActionCancel** eltérő értékek az események `eventType`.

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

Egy esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| témakör | sztring | A forrás teljes erőforrás elérési útja. Ez a mező nem írható. Event Grid biztosítja ezt az értéket. |
| Tulajdonos | sztring | Az esemény tárgya közzétevő által megadott elérési útja. |
| eventType | sztring | Ehhez eseményre adatforráshoz regisztrált esemény típusok egyikét. |
| eventTime | sztring | Az esemény akkor jön létre az idő alapján a szolgáltató UTC idő. |
| id | sztring | Az esemény egyedi azonosítója. |
| adatok | objektum | Erőforrás-csoport eseményadatokat. |
| dataVersion | sztring | Az adatobjektum sémaverziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | sztring | Az esemény-metaadatok sémaverziója. Event Grid sémáját, a legfelső szintű tulajdonságait határozza meg. Event Grid biztosítja ezt az értéket. |

Az objektum a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| Engedélyezési | objektum | A kért hitelesítést biztosít a műveletet. |
| jogcímek | objektum | A jogcímek tulajdonságait. További információkért lásd: [JWT-specifikáció](http://self-issued.info/docs/draft-ietf-oauth-json-web-token.html). |
| correlationId | sztring | A hibaelhárítási művelet azonosítója. |
| Törzsparaméterei | objektum | A művelet részleteit. Ez az objektum csak van hozzáadva, amikor egy meglévő erőforrás frissítése vagy töröl egy erőforrást. |
| ResourceProvider | sztring | Az erőforrás-szolgáltató a művelethez. |
| resourceUri | sztring | A műveletet az erőforrás URI azonosítója. |
| operationName | sztring | A művelet, amely kerül. |
| status | sztring | A művelet állapota. |
| subscriptionId | sztring | Az erőforrás előfizetés-azonosítója. |
| tenantId | sztring | Az erőforrás Bérlőazonosítója. |

## <a name="next-steps"></a>További lépések

* Azure Event Grid bemutatása, lásd: [Mi az Event Grid?](overview.md)
* Az Azure Event Grid-előfizetés létrehozásával kapcsolatos további információkért lásd: [Event Grid-előfizetés séma](subscription-creation-schema.md).

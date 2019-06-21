---
title: Gyakori paraméterek és fejlécek
description: A paraméterek és minden művelet, amely akkor lehet hasznos, a Key Vault-erőforrásra való közös fejlécet.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 01ac02ca0e449dcac8fcd05450566fd8138acba3
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "64696690"
---
# <a name="common-parameters-and-headers"></a>Gyakori paraméterek és fejlécek

Minden művelet, amely akkor lehet hasznos, a Key Vault-erőforrásra szokás a következő információkat:

- Cserélje le `{api-version}` az URI-t az api-verzióval rendelkező.
- Cserélje le `{subscription-id}` az az előfizetés-azonosító, az URI-ban
- Cserélje le `{resource-group-name}` az erőforráscsoport. További információkért lásd: erőforráscsoportok használata az Azure-erőforrások kezeléséhez.
- Cserélje le `{vault-name}` a kulcstartó nevére, az URI-ban.
- Set the Content-Type header to application/json.
- Az engedélyezési fejléc beállítása a JSON Web Token, hogy az Azure Active Directory (AAD). További információkért lásd: [hitelesítése az Azure Resource Manager](authentication-requests-and-responses.md) kérelmeket.

## <a name="common-error-response"></a>Gyakori hiba válasz
A szolgáltatás által használt HTTP-állapotkódok sikerességét vagy sikertelenségét jelzi. Emellett a hibák választ a következő formátumban tartalmazza:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Elem neve | Típus | Leírás |
|---|---|---|
| code | string | Hiba történt a típusa.|
| message | string | Mi okozta a hibát leírása. |



## <a name="see-also"></a>Lásd még:
 [Az Azure Key Vault REST API-referencia](/rest/api/keyvault/)

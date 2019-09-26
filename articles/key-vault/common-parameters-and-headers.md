---
title: Gyakori paraméterek és fejlécek
description: A Key Vault erőforrásokkal kapcsolatos összes művelethez közös paraméterek és fejlécek.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ec8e1df71e6513b13e9c37174a3363471be01d9
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879279"
---
# <a name="common-parameters-and-headers"></a>Gyakori paraméterek és fejlécek

Az alábbi információk a Key Vault erőforrásokhoz kapcsolódó összes művelet esetében közösek:

- Cserélje `{api-version}` le az elemet az API-verzióra az URI-ban.
- Cserélje `{subscription-id}` le az elemet az előfizetés azonosítójával az URI-ban
- Cserélje `{resource-group-name}` le az csoportot az erőforráscsoporthoz. További információ: erőforráscsoportok használata az Azure-erőforrások kezeléséhez.
- Cserélje `{vault-name}` le a elemet a Key Vault nevére az URI-ban.
- Állítsa be a Content-type fejlécet az Application/JSON értékre.
- Állítsa be az engedélyezési fejlécet olyan JSON Web Tokenre, amelyet a Azure Active Directorytól (HRE) szerez be. További információ: Azure Resource Manager kérelmek [hitelesítése](authentication-requests-and-responses.md) .

## <a name="common-error-response"></a>Gyakori hiba a válaszban
A szolgáltatás HTTP-állapotkódot használ a sikeres vagy sikertelen művelet jelzésére. Továbbá a hibák a következő formátumban választ tartalmaznak:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Elem neve | Type | Leírás |
|---|---|---|
| code | Karakterlánc | A bekövetkezett hiba típusa.|
| message | Karakterlánc | Annak leírása, hogy mi okozta a hibát. |



## <a name="see-also"></a>Lásd még:
 [Azure Key Vault REST API-hivatkozás](/rest/api/keyvault/)

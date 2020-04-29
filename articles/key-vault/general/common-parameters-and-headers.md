---
title: Gyakori paraméterek és fejlécek
description: A Key Vault erőforrásokkal kapcsolatos összes művelethez közös paraméterek és fejlécek.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d0ada9c1e6b45b1be17b15b67f67fc64fc266203
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430876"
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

|Elem neve | Típus | Leírás |
|---|---|---|
| code | sztring | A bekövetkezett hiba típusa.|
| message | sztring | Annak leírása, hogy mi okozta a hibát. |



## <a name="see-also"></a>Lásd még:
 [Azure Key Vault REST API-hivatkozás](/rest/api/keyvault/)

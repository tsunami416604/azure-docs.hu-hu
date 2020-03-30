---
title: Gyakori paraméterek és fejlécek
description: A Key Vault-erőforrásokkal kapcsolatos összes műveletre vonatkozó paraméterek és fejlécek.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197589"
---
# <a name="common-parameters-and-headers"></a>Gyakori paraméterek és fejlécek

A következő információk közösek a Key Vault-erőforrásokkal kapcsolatos összes műveletben:

- Cserélje `{api-version}` le az API-verziót az URI-ban.
- Az `{subscription-id}` előfizetés-azonosító cseréje az URI-ban
- Cserélje `{resource-group-name}` le az erőforráscsoportra. További információ: Erőforráscsoportok használata az Azure-erőforrások kezeléséhez.
- Cserélje `{vault-name}` le a kulcstartó nevét az URI-ban.
- Állítsa a Content-Type fejlécet alkalmazásra/jsonra.
- Állítsa be az engedélyezési fejlécet egy JSON webtokenre, amelyet az Azure Active Directoryból (AAD) kapott beszerezni. További információ: [Az Azure Resource Manager-kérelmek hitelesítése.](authentication-requests-and-responses.md)

## <a name="common-error-response"></a>Gyakori hibaválasz
A szolgáltatás HTTP-állapotkódokat fog használni a sikeres vagy sikertelen ség jelzésére. Ezenkívül a hibák a következő formátumú választ tartalmazzák:

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
| code | sztring | A hiba típusa.|
| message | sztring | A hiba okainak leírása. |



## <a name="see-also"></a>Lásd még:
 [Azure Key Vault REST API-útmutató](/rest/api/keyvault/)

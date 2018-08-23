---
title: Gyakori paraméterek és fejlécek
description: A paraméterek és minden művelet, amely akkor lehet hasznos, a Key Vault-erőforrásra való közös fejlécet.
services: key-vault
documentationcenter: ''
author: bryanla
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: bryanla
ms.openlocfilehash: a319dc670b5b1dab163b2d3aa623fc4fb9ce1c3a
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42055513"
---
# <a name="common-parameters-and-headers"></a>Gyakori paraméterek és fejlécek

Minden művelet, amely akkor lehet hasznos, a Key Vault-erőforrásra szokás a következő információkat:

- Cserélje le `{api-version}` az URI-t az api-verzióval rendelkező.
- Cserélje le `{subscription-id}` az az előfizetés-azonosító, az URI-ban
- Cserélje le `{resource-group-name}` az erőforráscsoport. További információkért lásd: erőforráscsoportok használata az Azure-erőforrások kezeléséhez.
- Cserélje le `{vault-name}` a kulcstartó nevére, az URI-ban.
- A Content-Type fejléc beállítása az application/json.
- Az engedélyezési fejléc beállítása a JSON Web Token, hogy az Azure Active Directory (AAD). További információkért lásd: [hitelesítése az Azure Resource Manager](authentication-requests-and-responses.md) kérelmeket.

## <a name="common-error-response"></a>Gyakori hiba válasz
A szolgáltatás által használt HTTP-állapotkódok sikerességét vagy sikertelenségét jelzi. Emellett a hibák választ a következő formátumban tartalmazza:

   {  
     "error": {}  
     "code": "BadRequest",  
     "message": "a kulcstartó termékváltozata érvénytelen."  
     }  
   }  

|Elem neve | Típus | Leírás |
|---|---|---|
| Kód | sztring | Hiba történt a típusa.|
| message | sztring | Mi okozta a hibát leírása. |



## <a name="see-also"></a>Lásd még:
 [Az Azure Key Vault REST API-referencia](/rest/api/keyvault/)

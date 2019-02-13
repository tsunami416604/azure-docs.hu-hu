---
title: Gyakori paraméterek és fejlécek
description: A paraméterek és minden művelet, amely akkor lehet hasznos, a Key Vault-erőforrásra való közös fejlécet.
services: key-vault
documentationcenter: ''
author: bryanla
manager: barbkess
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: bryanla
ms.openlocfilehash: 3fb11ad74e3d1628cbf3f00e2aae648be3eea437
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107684"
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

   {  
     "error": {}  
     "code": "BadRequest",  
     "message": "A kulcstartó termékváltozata érvénytelen."  
     }  
   }  

|Elem neve | Typo | Leírás |
|---|---|---|
| Kód | sztring | Hiba történt a típusa.|
| message | sztring | Mi okozta a hibát leírása. |



## <a name="see-also"></a>Lásd még:
 [Az Azure Key Vault REST API-referencia](/rest/api/keyvault/)

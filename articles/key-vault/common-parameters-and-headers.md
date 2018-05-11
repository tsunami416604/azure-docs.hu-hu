---
title: Közös paraméterek és fejlécek
description: A paraméterek és az összes művelethez, akkor lehet hasznos, Key Vault erőforrásokkal kapcsolatos gyakori fejlécek.
services: key-vault
documentationcenter: ''
author: lleonard-msft
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: a715d13ca9-d6e8-4e54-ac5e-0ed9400fb15b15d13ca9-d6e8-4e54-ac5e-0ed9400fb15b
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: alleonar
ms.openlocfilehash: ead1ac550c9b7c489edefd35d5672a9955e78255
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="common-parameters-and-headers"></a>Közös paraméterek és fejlécek

A következő információkat az összes művelethez, akkor lehet hasznos, Key Vault erőforrásokkal kapcsolatos gyakori:

- Cserélje le `{api-version}` URI azonosítójában lévő api-version paraméterrel.
- Cserélje le `{subscription-id}` az előfizetés az URI azonosítóval
- Cserélje le `{resource-group-name}` az erőforrás-csoporttal. További információkért lásd: erőforráscsoportok használata az Azure erőforrások kezeléséhez.
- Cserélje le `{vault-name}` a kulcstartót nevű az URI azonosító.
- Az application/json a Content-Type fejléc értéke.
- Az engedélyezési fejléc értéke egy JSON Web Token beszerezni az Azure Active Directory (AAD). További információkért lásd: [hitelesítéséhez az Azure Resource Manager](authentication-requests-and-responses.md) kérelmeket.

## <a name="common-error-response"></a>Közös hibaválaszba
A szolgáltatás által használt HTTP-állapotkódok sikerességet vagy sikertelenséget jelző. Ezenkívül hibák tartalmazzák a válasz a következő formátumban:

   {  
     "error": {  
     "kód": "BadRequest"  
     "az üzenet": "a kulcstartót termékváltozata érvénytelen."  
     }  
   }  

|Elem neve | Típus | Leírás |
|---|---|---|
| Kód | karakterlánc | Hiba történt a típusa.|
| message | karakterlánc | A hiba okáról leírása. |



## <a name="see-also"></a>Lásd még:
 [Az Azure Key Vault REST API-referencia](/rest/api/keyvault/)

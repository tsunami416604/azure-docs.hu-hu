---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: a1b12a72434034ecc6cbe527cc3de6454e4293a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79381689"
---
|Név |Leírás |Hatás(ok) |Verzió |GitHub |
|---|---|---|---|---|
|[A tárolónyilvántartásokat ügyfél által felügyelt kulccsal (CMK) kell titkosítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F5b9159ae-1701-4a6f-9a7a-aa9c8ddd0580) |Naplózza azokat a tárolónyilvántartásokat, amelyeken nincs engedélyezve a titkosítás az ügyfél által felügyelt kulcsokkal (CMK). A CMK titkosítással kapcsolatos további https://aka.ms/acr/CMKinformációkért látogasson el a következő oldalra: . |Naplózás, letiltva |1.0.0-preview |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_CMKEncryptionEnabled_Audit.json)
|[A tárolónyilvántartások nem engedhetik meg a korlátlan hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd0793b48-0edc-4296-a390-4c75d1bdfd71) |Naplózható tárolónyilvántartások, amelyek nem rendelkeznek hálózati (IP- vagy vnet-) szabályokkal, és alapértelmezés szerint lehetővé teszik az összes hálózati hozzáférést. A legalább egy IP/tűzfal szabállyal vagy konfigurált virtuális hálózattal rendelkező tárolónyilvántartások megfelelőnek minősülnek. További információ a Container Registry Network https://aka.ms/acr/vnetszabályok, kérjük, látogasson el: . |Naplózás, letiltva |1.0.0-preview |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Container%20Registry/ACR_NetworkRulesExist_Audit.json)

---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/17/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2469f11fb5135fc0f798be491b4142696f322756
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/18/2020
ms.locfileid: "94741013"
---
|Név<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Redis tartozó Azure cache-nek egy virtuális hálózaton belül kell lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7d092e0a-7acd-40d2-a975-dca21cae48c4) |A Redis-hez készült Azure cache képes a virtuális hálózaton belül tárolni, így az erőforrásnak nem nyilvános végpontja van, amelyet a felhasználó felügyel és kezel. |Naplózás, megtagadás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_CacheInVnet_Audit.json) |
|[Csak a Redis-hez készült Azure cache biztonságos kapcsolatainak engedélyezése szükséges](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |Csak az SSL-kapcsolaton keresztüli kapcsolatok engedélyezése az Azure cache-Redis. A biztonságos kapcsolatok használata biztosítja a kiszolgáló és a szolgáltatás közötti hitelesítést, és védelmet biztosít a hálózati rétegbeli támadásoktól, például az embertől a középponttól, a lehallgatás és a munkamenet-eltérítéstől. |Naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |

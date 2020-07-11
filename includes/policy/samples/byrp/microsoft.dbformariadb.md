---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 74747d51cdf584d909b2d9a70dfd414bda29feea
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86273938"
---
|Név<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Ez a házirend minden olyan Azure Database for MariaDB naplóz, amely nem engedélyezett a Geo-redundáns biztonsági mentéssel. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[A MariaDB-kiszolgálónak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Ez a házirend a MariaDB-kiszolgálókat nem virtuális hálózati szolgáltatás-végpont használatára konfigurálta. További részletekért látogasson el a következő oldalra: [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork) . |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[A privát végpontot engedélyezni kell a MariaDB-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Ez a házirend olyan MariaDB-kiszolgálókat naplóz, amelyek nem privát végpont használatára vannak konfigurálva. További részletekért látogasson el a következő oldalra: [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink) . |AuditIfNotExists, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[A MariaDB-kiszolgálók esetében le kell tiltani a nyilvános hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Ez a házirend a nyilvános hálózati hozzáféréssel rendelkező MariaDB-kiszolgálókat naplózza a környezetben. További részletekért látogasson el a következő oldalra: [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542) . |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |

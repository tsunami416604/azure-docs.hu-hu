---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d8d390256c44bc37070594596f627fe52f325c6a
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82966147"
---
|Name |Leírás |Hatás (ok) |Verzió |GitHub |
|---|---|---|---|---|
|[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MariaDB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Ez a házirend minden olyan Azure Database for MariaDB naplóz, amely nem engedélyezett a Geo-redundáns biztonsági mentéssel. |Naplózás, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[A MariaDB-kiszolgálónak virtuális hálózati szolgáltatási végpontot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |Ez a házirend a MariaDB-kiszolgálókat nem virtuális hálózati szolgáltatás-végpont használatára konfigurálta. További részletekért látogasson [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork)el a következő oldalra:. |AuditIfNotExists, letiltva |1.0.1 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[A privát végpontot engedélyezni kell a MariaDB-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |Ez a házirend olyan MariaDB-kiszolgálókat naplóz, amelyek nem privát végpont használatára vannak konfigurálva. További részletekért látogasson [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink)el a következő oldalra:. |AuditIfNotExists, letiltva |1.0.1 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[A MariaDB-kiszolgálók esetében le kell tiltani a nyilvános hálózati hozzáférést](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |Ez a házirend a nyilvános hálózati hozzáféréssel rendelkező MariaDB-kiszolgálókat naplózza a környezetben. További részletekért látogasson [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542)el a következő oldalra:. |Naplózás, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |

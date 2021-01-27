---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/25/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 9a569c0e6010c75552fcd25e9ae08561ceb622d4
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805086"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |A Service Fabric három védelmi szintet biztosít (nincs, aláírás és EncryptAndSign) a csomópontok közötti kommunikációhoz az elsődleges fürt tanúsítványának használatával. Állítsa be a védelmi szintet annak biztosítására, hogy minden csomópont-csomópont típusú üzenet titkosítva legyen, és digitálisan alá legyen írva. |Naplózás, megtagadás, letiltva |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Az ügyfél-hitelesítés használatának naplózása csak Azure Active Directoryon keresztül Service Fabric |Naplózás, megtagadás, letiltva |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

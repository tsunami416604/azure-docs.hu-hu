---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/16/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: afd9eb9fd0499935c73d32077b8fe3ec25e5d22a
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982611"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |A Service Fabric három védelmi szintet biztosít (nincs, aláírás és EncryptAndSign) a csomópontok közötti kommunikációhoz az elsődleges fürt tanúsítványának használatával. Állítsa be a védelmi szintet annak biztosítására, hogy minden csomópont-csomópont típusú üzenet titkosítva legyen, és digitálisan alá legyen írva. |Naplózás, megtagadás, letiltva |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |
|[Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Az ügyfél-hitelesítés használatának naplózása csak Azure Active Directoryon keresztül Service Fabric |Naplózás, megtagadás, letiltva |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |

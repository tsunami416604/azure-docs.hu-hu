---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: efddb110f776bd370756685eb5667ab985f7960b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79381822"
---
|Név |Leírás |Hatás(ok) |Verzió |GitHub |
|---|---|---|---|---|
|[A Service Fabric-fürtöknek encryptAndSign-re kell állítaniuk a ClusterProtectionLevel tulajdonságot](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |A Service Fabric három védelmi szintet biztosít (Nincs, Aláírás és EncryptAndSign) a csomópontok közötti kommunikációhoz egy elsődleges fürttanúsítvány használatával. A védelmi szint beállítása annak érdekében, hogy az összes csomópont-csomópont üzenet titkosítva legyen és digitálisan aláírva legyen |Naplózás, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json)
|[A Service Fabric-fürtök csak az Azure Active Directoryt használhatják az ügyfélhitelesítéshez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |Az ügyfélhitelesítés használatának naplózása csak az Azure Active Directory szolgáltatáson keresztül a Service Fabricben |Naplózás, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json)

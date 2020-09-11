---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 2215a3a4aa2a77e17b8f2c56cad42f8c15fb9ba7
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2020
ms.locfileid: "90014980"
---
## <a name="azure-security-benchmark"></a>Azure-biztonsági teljesítményteszt

Az [Azure biztonsági teljesítményteszt](../../../../articles/security/benchmarks/overview.md) az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. Ha szeretné megtekinteni, hogy a szolgáltatás hogyan képezi le teljesen az Azure biztonsági Teljesítménytesztét, tekintse meg az [Azure biztonsági teljesítményteszt-leképezési fájljait](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Ha szeretné áttekinteni, hogy az összes Azure-szolgáltatáshoz rendelkezésre álló Azure Policy hogyan jelennek meg a megfelelőségi szabványhoz, tekintse meg a [Azure Policy szabályozási megfelelőség – Azure biztonsági teljesítményteszt](../../../../articles/governance/policy/samples/azure-security-benchmark.md)című témakört.

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Házirend verziója<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Identitás- és hozzáférés-vezérlés |3.9 |Az Azure Active Directory használata |[Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |
|Adatvédelem |4,8 |Bizalmas adatok titkosítása a nyugalmi állapotban |[Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F617c02be-7f02-4efd-8836-3180d47b6c68) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |

## <a name="nist-sp-800-53-r4"></a>NIST SP 800-53 R4

Ha szeretné áttekinteni, hogy az összes Azure-szolgáltatáshoz elérhető beépített Azure Policy hogyan képezi le a megfelelőségi szabványt, tekintse meg a [Azure Policy szabályozási megfelelőség – NIST SP 800-53 R4](../../../../articles/governance/policy/samples/nist-sp-800-53-r4.md)című témakört.
További információ erről a megfelelőségi szabványról: [NIST SP 800-53 R4](https://nvd.nist.gov/800-53).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Házirend verziója<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Hozzáférés-vezérlés |AC-2 (7) |Fiókkezelés \| szerepköralapú sémái |[Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb54ed75b-3e1a-44ac-a333-05ba39b99ff0) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |


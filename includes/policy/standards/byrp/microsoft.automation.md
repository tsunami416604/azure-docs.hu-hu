---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 12/01/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c2385d07ccb81041bd340a8bec0412a8f14cef56
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96476976"
---
## <a name="azure-security-benchmark"></a>Azure-biztonsági teljesítményteszt

Az [Azure biztonsági teljesítményteszt](../../../../articles/security/benchmarks/overview.md) az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. Ha szeretné megtekinteni, hogy a szolgáltatás hogyan képezi le teljesen az Azure biztonsági Teljesítménytesztét, tekintse meg az [Azure biztonsági teljesítményteszt-leképezési fájljait](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Ha szeretné áttekinteni, hogy az összes Azure-szolgáltatáshoz rendelkezésre álló Azure Policy hogyan jelennek meg a megfelelőségi szabványhoz, tekintse meg a [Azure Policy szabályozási megfelelőség – Azure biztonsági teljesítményteszt](../../../../articles/governance/policy/samples/azure-security-benchmark.md)című témakört.

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Házirend verziója<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Adatvédelem |4.8 |Bizalmas adatok titkosítása a nyugalmi állapotban |[Az Automation-fiók változóit titkosítani kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3657f5a0-770e-44a3-b44e-9431ba1e9735) |[1.1.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Automation/Automation_AuditUnencryptedVars_Audit.json) |

> [!NOTE]
> Amikor létrehoz egy Automation-fiók változót, a titkosítását és tárolását Azure Automation biztonságos eszközként adhatja meg. A változó létrehozása után nem változtathatja meg a titkosítási állapotát a változó újbóli létrehozása nélkül. Ha rendelkezik olyan Automation-fiókkal, amely még nem titkosított bizalmas adatokat tárol, akkor törölnie kell őket, és újra létre kell hoznia őket titkosított változókként. Azure Security Center javasoljuk, hogy titkosítsa az összes Azure Automation változót az [Automation-fiók változói](../../../../articles/security-center/recommendations-reference.md#recs-computeapp)között leírt módon. Ha olyan titkosítatlan változókkal rendelkezik, amelyeket ki szeretne zárni ebből a biztonsági javaslatból, tekintse meg a mentesítési szabály létrehozásával kapcsolatos tudnivalókat [és a biztonságos pontszámokat](../../../../articles/security-center/exempt-resource.md) .
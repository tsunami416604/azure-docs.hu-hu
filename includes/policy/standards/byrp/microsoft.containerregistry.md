---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/04/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: dac00d732389304eabb776219c31c975aabdbdbe
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89487172"
---
## <a name="azure-security-benchmark"></a>Azure-biztonsági teljesítményteszt

Az [Azure biztonsági teljesítményteszt](../../../../articles/security/benchmarks/overview.md) az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. Ha szeretné megtekinteni, hogy a szolgáltatás hogyan képezi le teljesen az Azure biztonsági Teljesítménytesztét, tekintse meg az [Azure biztonsági teljesítményteszt-leképezési fájljait](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Ha szeretné áttekinteni, hogy az összes Azure-szolgáltatáshoz rendelkezésre álló Azure Policy hogyan jelennek meg a megfelelőségi szabványhoz, tekintse meg a [Azure Policy szabályozási megfelelőség – Azure biztonsági teljesítményteszt](../../../../articles/governance/policy/samples/azure-security-benchmark.md)című témakört.

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Házirend verziója<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Hálózati biztonság |1,1 |Az erőforrások védelme hálózati biztonsági csoportokkal vagy a Virtual Network Azure Firewall használatával |[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |

## <a name="hipaa-hitrust-92"></a>HIPAA HITRUST 9,2

Ha szeretné áttekinteni, hogy az összes Azure-szolgáltatáshoz rendelkezésre álló Azure Policy hogyan jelennek meg a megfelelőségi szabványhoz, tekintse meg a [Azure Policy szabályozási megfelelőség – HIPAA HITRUST 9,2](../../../../articles/governance/policy/samples/hipaa-hitrust-9-2.md)című témakört.
További információ erről a megfelelőségi szabványról: [HIPAA HITRUST 9,2](https://www.hhs.gov/hipaa/for-professionals/security/laws-regulations/index.html).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Házirend verziója<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Elkülönítés a hálózatokban |0805.01 m1Organizational. 12 – 01. m |A szervezet biztonsági átjárói (például tűzfalak) kikényszerítik a biztonsági házirendeket, és úgy vannak konfigurálva, hogy szűrik a tartományok közötti forgalmat, blokkolják a jogosulatlan hozzáférést, és a belső vezetékes, belső vezeték nélküli és külső hálózati szegmensek (például az Internet) közötti elkülönítést használják, beleértve a DMZ és az egyes tartományok hozzáférés-vezérlési szabályzatának érvényesítését. |[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Elkülönítés a hálózatokban |0806.01 m2Organizational. 12356-01. m |A szervezetek hálózata logikailag és fizikailag szegmentálva van egy meghatározott biztonsági kerületben és egy beosztott vezérlőkkel, beleértve a nyilvánosan elérhető rendszerösszetevők alhálózatait is, amelyek logikailag elkülönülnek a belső hálózattól a szervezeti követelmények alapján. a forgalom ellenőrzése a szükséges funkciókon és az adatok/rendszerek besorolásán alapul, kockázatértékelés és a rájuk vonatkozó biztonsági követelmények alapján. |[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Elkülönítés a hálózatokban |0894.01 m2Organizational. 7 – 01. m |A hálózatok a fizikai kiszolgálók, alkalmazások vagy adatok virtualizált kiszolgálókra való áttelepítése során elkülönítettek a termelési szintű hálózatokból. |[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Hálózati vezérlők |0868.09 m3Organizational. 18 – 09. m |A szervezet létrehoz egy tűzfal-konfigurációt a bejövő és a kimenő forgalom korlátozására, amely a kezelt adatkörnyezethez szükséges. |[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Hálózati vezérlők |0869.09 m3Organizational. 19-09. m |Az útválasztó konfigurációs fájljai biztonságosak és szinkronizálhatók. |[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Hálózati vezérlők |0870.09 m3Organizational. 20-09. m |A rendszer megtagadja az összes proxy elérését, kivéve azokat a gazdagépeket, portokat és szolgáltatásokat, amelyekre kifejezetten szükség van. |[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |
|Hálózati vezérlők |0871.09 m3Organizational. 22-09. m |A mérvadó DNS-kiszolgálók belső és külső szerepkörökbe vannak különítve. |[Container Registry virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc4857be7-912a-4c75-87e6-e30292bcdf78) |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_ContainerRegistry_Audit.json) |


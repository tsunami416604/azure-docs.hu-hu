---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/22/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: cf56786656d830d2fea4c9e916f350fc392f5cf0
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89379217"
---
## <a name="azure-security-benchmark"></a>Azure-biztonsági teljesítményteszt

Az [Azure biztonsági teljesítményteszt](../../../../articles/security/benchmarks/overview.md) az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. Ha szeretné megtekinteni, hogy a szolgáltatás hogyan képezi le teljesen az Azure biztonsági Teljesítménytesztét, tekintse meg az [Azure biztonsági teljesítményteszt-leképezési fájljait](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Ha szeretné áttekinteni, hogy az összes Azure-szolgáltatáshoz rendelkezésre álló Azure Policy hogyan jelennek meg a megfelelőségi szabványhoz, tekintse meg a [Azure Policy szabályozási megfelelőség – Azure biztonsági teljesítményteszt](../../../../articles/governance/policy/samples/azure-security-benchmark.md)című témakört.

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Házirend verziója<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Hálózati biztonság |1,1 |Az erőforrások védelme hálózati biztonsági csoportokkal vagy a Virtual Network Azure Firewall használatával |[A magánhálózati végpontot engedélyezni kell a MySQL-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|Adatvédelem |4.4 |Az összes bizalmas adat titkosítása az átvitel során |[Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|Adat-helyreállítás |9,1 |Rendszeres automatizált biztonsági másolatok biztosítása |[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|Adat-helyreállítás |9,2 |A teljes rendszerbiztonsági másolatok és az ügyfelek által felügyelt kulcsok biztonsági mentésének végrehajtása |[A Geo-redundáns biztonsági mentést engedélyezni kell a Azure Database for MySQL](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |

## <a name="cis-microsoft-azure-foundations-benchmark"></a>CIS Microsoft Azure Foundations Benchmark

Ha szeretné áttekinteni, hogy az összes Azure-szolgáltatáshoz elérhető beépített Azure Policy hogyan képezi le a megfelelőségi szabványt, tekintse meg a [Azure Policy szabályozási megfelelőség – CIS Microsoft Azure alapítványok teljesítményteszt 1.1.0](../../../../articles/governance/policy/samples/cis-azure-1-1-0.md)című témakört.
További információ erről a megfelelőségi szabványról: [CIS Microsoft Azure founds benchmark](https://www.cisecurity.org/benchmark/azure/).

|Tartomány |Vezérlő azonosítója |Vezérlő címe |Szabályzat<br /><sub>(Azure Portal)</sub> |Házirend verziója<br /><sub>GitHub</sub>  |
|---|---|---|---|---|
|Adatbázis-szolgáltatások |4,11 |Győződjön meg arról, hogy az "SSL-kapcsolat érvényesítése" beállítás értéke "ENABLED" a MySQL adatbázis-kiszolgáló számára |[Az SSL-kapcsolat kényszerített engedélyezése a MySQL adatbázis-kiszolgálókon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |


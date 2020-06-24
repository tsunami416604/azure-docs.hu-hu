---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: b28641a184503dc36b5e3f8315e810c69f3be9d3
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84686519"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Cosmos DB engedélyezett helyei](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0473574d-2d43-4217-aefe-941fcdf7e684) |Ez a szabályzat lehetővé teszi a szervezet által megadható helyeknek a Azure Cosmos DB erőforrások telepítésekor való korlátozását. A földrajzi megfelelőségi követelmények betartására szolgál. |[parameters (' policyEffect ')] |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json) |
|[Azure Cosmos DB kulcs alapú metaadatok írási hozzáférését le kell tiltani](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4750c32b-89c0-46af-bfcb-2e4541a818d5) |Ez a szabályzat lehetővé teszi annak biztosítását, hogy az összes Azure Cosmos DB-fiók letiltsa a kulcs alapú metaadatok írási hozzáférését. |hozzáfűzése |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_DisableMetadata_Append.json) |
|[Azure Cosmos DB átviteli sebességnek korlátozottnak kell lennie](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0b7ef78e-a035-4f23-b9bd-aff122a1b1cf) |Ez a szabályzat lehetővé teszi, hogy korlátozza a szervezet által megadható maximális átviteli sebességet Azure Cosmos DB adatbázisok és tárolók erőforrás-szolgáltatón keresztüli létrehozásakor. Blokkolja az autoscale-erőforrások létrehozását. |naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_MaxThroughput_Deny.json) |
|[Cosmos DB virtuális hálózati szolgáltatás végpontját kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Ez a házirend minden olyan Cosmos DB naplóz, amely nem a virtuális hálózati szolgáltatás végpontjának használatára van konfigurálva. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[A Cosmos DB-fiókok komplex veszélyforrások elleni védelmének üzembe helyezése](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |Ez a szabályzat lehetővé teszi a komplex veszélyforrások elleni védelmet Cosmos DB-fiókok között. |DeployIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |

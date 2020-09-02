---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: d02f30c6ad61d3828f6d39c2192d4c15db2d6e38
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298571"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A CORS nem engedheti meg, hogy minden tartomány hozzáférjen a FHIR API-hoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé, hogy az összes tartomány hozzáférjen az API-hoz a FHIR. Az API-k FHIR való ellátásához távolítsa el az összes tartományhoz való hozzáférést, és explicit módon határozza meg a kapcsolódáshoz engedélyezett tartományokat. |naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

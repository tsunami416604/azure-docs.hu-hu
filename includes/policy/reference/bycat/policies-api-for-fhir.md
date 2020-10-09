---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: be7b3499cec1a6f467e5d043f81d8752b695f858
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91821020"
---
|Név<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A CORS nem engedheti meg, hogy minden tartomány hozzáférjen a FHIR API-hoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0fea8f8a-4169-495d-8307-30ec335f387d) |A több eredetű erőforrás-megosztás (CORS) nem teszi lehetővé, hogy az összes tartomány hozzáférjen az API-hoz a FHIR. Az API-k FHIR való ellátásához távolítsa el az összes tartományhoz való hozzáférést, és explicit módon határozza meg a kapcsolódáshoz engedélyezett tartományokat. |naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/API%20for%20FHIR/HealthcareAPIs_RestrictCORSAccess_Audit.json) |

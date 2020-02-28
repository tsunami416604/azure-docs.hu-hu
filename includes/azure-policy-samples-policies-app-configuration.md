---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/26/2020
ms.author: dacoulte
ms.openlocfilehash: 7d27fb69cab76fe653d784c787460ed453cb64d1
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77780148"
---
|Name (Név) |Leírás |Hatás (ok) |Verzió |Forrás |
|---|---|---|---|---|
|[Az alkalmazás konfigurációjának ügyfél által felügyelt kulcsot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Ez a házirend minden olyan alkalmazás-konfigurációs példányt naplóz, amely nem használ ügyfél által felügyelt kulcsot. |Naplózás, letiltva |1.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json)
|[Az alkalmazás konfigurációjának privát hivatkozást kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Ez a házirend minden olyan alkalmazás-konfigurációs példányt naplóz, amely nem használ privát hivatkozást. |AuditIfNotExists, letiltva |1.0.0 |[GitHub](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json)

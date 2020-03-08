---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/05/2020
ms.author: dacoulte
ms.openlocfilehash: b52977be0d9c3ca06ee8971ba854339298cd5c28
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2020
ms.locfileid: "78668424"
---
|Name (Név) |Leírás |Hatás (ok) |Verzió |GitHub |
|---|---|---|---|---|
|[Az alkalmazás konfigurációjának ügyfél által felügyelt kulcsot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F967a4b4b-2da9-43c1-b7d0-f98d0d74d0b1) |Ez a házirend minden olyan alkalmazás-konfigurációs példányt naplóz, amely nem használ ügyfél által felügyelt kulcsot. |Naplózás, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/CustomerManagedKey_Audit.json)
|[Az alkalmazás konfigurációjának privát hivatkozást kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fca610c1d-041c-4332-9d88-7ed3094967c7) |Ez a házirend minden olyan alkalmazás-konfigurációs példányt naplóz, amely nem használ privát hivatkozást. |AuditIfNotExists, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Configuration/PrivateLink_Audit.json)

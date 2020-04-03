---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 813384776b850fc5d782bc6dae3aae3e247f4cf6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624294"
---
|Név |Leírás |Hatás(ok) |Verzió |GitHub |
|---|---|---|---|---|
|[A Batch-fiókok diagnosztikai naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F428256e6-1fac-4f48-a757-df34c2b3336d) |Diagnosztikai naplók engedélyezése. Ez lehetővé teszi a vizsgálati célokra használt tevékenységnyomvonalak újbóli létrehozását; biztonsági incidens esetén, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Letiltva |2.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditDiagnosticLog_Audit.json)
|[Metrikariasztási szabályokat kell konfigurálni a Batch-fiókokban](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F26ee67a2-f81a-4ba8-b9ce-8550bd5ee1a7) |Metrikariasztási szabályok naplózási konfigurációjának naplózása a Batch-fiókban a szükséges metrika engedélyezéséhez |AuditIfNotExists, Letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Batch/Batch_AuditMetricAlerts_Audit.json)

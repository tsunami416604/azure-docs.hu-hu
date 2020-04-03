---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: e8b79c8a2e83a77f1f0de344306b8cf825c428c6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80624508"
---
|Név |Leírás |Hatás(ok) |Verzió |GitHub |
|---|---|---|---|---|
|[A RootManageSharedAccessKey kivételével minden engedélyezési szabályt el kell távolítani a Service Bus névteréből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa1817ec0-a368-432a-8057-8371e17ac6ee) |A Service Bus-ügyfelek nem használhatnak névtérszintű hozzáférési házirendet, amely hozzáférést biztosít a névtér összes várólistájához és témaköréhez. A legkisebb jogosultsági szintű biztonsági modellhez való igazodás érdekében az entitás szintjén hozzáférési szabályzatokat kell létrehoznia a várólistákhoz és a témakörökhöz, hogy csak az adott entitáshoz biztosítson hozzáférést. |Naplózás, Megtagadás, Letiltva |1.0.1 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditNamespaceAccessRules_Audit.json)
|[A Service Bus diagnosztikai naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff8d36e2f-389b-4ee4-898d-21aeb69a0f45) |Diagnosztikai naplók engedélyezése. Ez lehetővé teszi a vizsgálati célokra használt tevékenységnyomvonalak újbóli létrehozását; biztonsági incidens esetén, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Letiltva |2.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Service%20Bus/ServiceBus_AuditDiagnosticLog_Audit.json)

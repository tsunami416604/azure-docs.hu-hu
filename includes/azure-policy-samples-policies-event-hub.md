---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/02/2020
ms.author: dacoulte
ms.openlocfilehash: 0b44908973425378ee5a95f817675125183871db
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80623931"
---
|Név |Leírás |Hatás(ok) |Verzió |GitHub |
|---|---|---|---|---|
|[A RootManageSharedAccessKey kivételével minden engedélyezési szabályt el kell távolítani az Event Hub névteréből](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb278e460-7cfc-4451-8294-cccc40a940d7) |Az Event Hub-ügyfelek nem használhatnak névtérszintű hozzáférési házirendet, amely hozzáférést biztosít a névtér összes várólistájához és témaköréhez. A legkisebb jogosultsági szintű biztonsági modellhez való igazodás érdekében az entitás szintjén hozzáférési szabályzatokat kell létrehoznia a várólistákhoz és a témakörökhöz, hogy csak az adott entitáshoz biztosítson hozzáférést. |Naplózás, Megtagadás, Letiltva |1.0.1 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditNamespaceAccessRules_Audit.json)
|[Az Event Hub-példány engedélyezési szabályait meg kell határozni](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4826e5f-6a27-407c-ae3e-9582eb39891d) |Az Event Hub-entitások engedélyezési szabályainak meglétének naplózása a legkevésbé privilegizált hozzáférés biztosításához |AuditIfNotExists, Letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditEventHubAccessRules_Audit.json)
|[Az Event Hub diagnosztikai naplóit engedélyezni kell](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83a214f7-d01a-484b-91a9-ed54470c9a6a) |Diagnosztikai naplók engedélyezése. Ez lehetővé teszi a vizsgálati célokra használt tevékenységnyomvonalak újbóli létrehozását; biztonsági incidens esetén, vagy ha a hálózat biztonsága sérül |AuditIfNotExists, Letiltva |2.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Hub/EventHub_AuditDiagnosticLog_Audit.json)

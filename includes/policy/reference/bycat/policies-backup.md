---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 345cc8d5c1bc362973c8fae0e1c5605faf6e6e2d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298558"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Ez a szabályzat segít ellenőrizni, hogy Azure Backup szolgáltatás engedélyezve van-e az összes virtuális gépen. Azure Backup egy költséghatékony, egykattintásos biztonsági mentési megoldás egyszerűsíti az adathelyreállítást, és könnyebben engedélyezhető, mint a többi felhőalapú biztonsági mentési szolgáltatás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Egy helyen lévő virtuális gépek biztonsági mentésének konfigurálása ugyanazon a helyen lévő központi tárba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Ez a házirend egy adott helyen lévő virtuális gépek Azure Backup védelmét konfigurálja egy meglévő központi tárolóra ugyanazon a helyen. Csak azokra a virtuális gépekre vonatkozik, amelyek még nincsenek konfigurálva a biztonsági mentéshez. Javasoljuk, hogy ezt a házirendet ne több mint 200 virtuális géphez rendelje hozzá. Ha a házirend több mint 200 virtuális géphez van hozzárendelve, akkor a biztonsági mentés néhány órával a definiált ütemezésen kívül is elindítható. Ez a szabályzat több virtuálisgép-lemezkép támogatásához lesz kialakítva. |deployIfNotExists, auditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |

---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 03/12/2020
ms.author: dacoulte
ms.openlocfilehash: 4a6c0cad4a93d0d99573a348070823b909c75c7e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79299413"
---
|Név |Leírás |Hatás(ok) |Verzió |GitHub |
|---|---|---|---|---|
|[Az Azure Backup biztonsági mentést engedélyezni kell a virtuális gépekhez](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Ez a szabályzat segít naplózni, ha az Azure Backup szolgáltatás engedélyezve van az összes virtuális gépek. Az Azure Backup egy költséghatékony, egykattintásos biztonsági mentési megoldás leegyszerűsíti az adat-helyreállítást, és könnyebben engedélyezhetővé, mint más felhőalapú biztonsági mentési szolgáltatások. |AuditIfNotExists, Letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json)
|[Biztonsági mentés konfigurálása egy hely virtuális gépein egy meglévő központi tárolóba ugyanazon a helyen](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Ez a szabályzat konfigurálja az Azure biztonsági mentési védelmet egy adott helyen lévő virtuális gépeken egy ugyanazon a helyen lévő meglévő központi tárolóba. Csak azokra a virtuális gépekre vonatkozik, amelyek még nincsenek konfigurálva a biztonsági mentéshez. Ajánlott, hogy ez a szabályzat legfeljebb 200 virtuális géphez van hozzárendelve. Ha a szabályzat 200-nál több virtuális géphez van hozzárendelve, a biztonsági mentés néhány órával a megadott ütemezésen túl aktiválódik. Ez a szabályzat további virtuálisgép-lemezképek támogatása érdekében. |deployIfNotExists, auditIfNotExists, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json)

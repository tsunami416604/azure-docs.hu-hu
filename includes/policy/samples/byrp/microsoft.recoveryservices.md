---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7baa1c030c8d70b76eb661d6a6fac7429a80a50e
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2020
ms.locfileid: "84709902"
---
|Name<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Ez a szabályzat segít ellenőrizni, hogy Azure Backup szolgáltatás engedélyezve van-e az összes virtuális gépen. Azure Backup egy költséghatékony, egykattintásos biztonsági mentési megoldás egyszerűsíti az adathelyreállítást, és könnyebben engedélyezhető, mint a többi felhőalapú biztonsági mentési szolgáltatás. |AuditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Egy helyen lévő virtuális gépek biztonsági mentésének konfigurálása ugyanazon a helyen lévő központi tárba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Ez a házirend egy adott helyen lévő virtuális gépek Azure Backup védelmét konfigurálja egy meglévő központi tárolóra ugyanazon a helyen. Csak azokra a virtuális gépekre vonatkozik, amelyek még nincsenek konfigurálva a biztonsági mentéshez. Javasoljuk, hogy ezt a házirendet ne több mint 200 virtuális géphez rendelje hozzá. Ha a házirend több mint 200 virtuális géphez van hozzárendelve, akkor a biztonsági mentés néhány órával a definiált ütemezésen kívül is elindítható. Ez a szabályzat több virtuálisgép-lemezkép támogatásához lesz kialakítva. |deployIfNotExists, auditIfNotExists, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[A Recovery Services-tároló diagnosztikai beállításainak üzembe helyezése erőforrás-specifikus kategóriák Log Analytics munkaterületre.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |A Recovery Services tároló diagnosztikai beállításainak üzembe helyezése az erőforrás-specifikus kategóriák Log Analytics munkaterületre való továbbításához. Ha az erőforrás-specifikus kategóriák bármelyike nincs engedélyezve, létrejön egy új diagnosztikai beállítás. |deployIfNotExists |[1.0.0 – előzetes verzió](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |

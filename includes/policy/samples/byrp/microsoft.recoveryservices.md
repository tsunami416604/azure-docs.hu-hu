---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/13/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: bf8b98db98faf2aef3e634518da4914d46142739
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83635824"
---
|Name |Leírás |Hatás (ok) |Verzió |GitHub |
|---|---|---|---|---|
|[A Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Ez a szabályzat segít ellenőrizni, hogy Azure Backup szolgáltatás engedélyezve van-e az összes virtuális gépen. Azure Backup egy költséghatékony, egykattintásos biztonsági mentési megoldás egyszerűsíti az adathelyreállítást, és könnyebben engedélyezhető, mint a többi felhőalapú biztonsági mentési szolgáltatás. |AuditIfNotExists, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Egy helyen lévő virtuális gépek biztonsági mentésének konfigurálása ugyanazon a helyen lévő központi tárba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Ez a házirend egy adott helyen lévő virtuális gépek Azure Backup védelmét konfigurálja egy meglévő központi tárolóra ugyanazon a helyen. Csak azokra a virtuális gépekre vonatkozik, amelyek még nincsenek konfigurálva a biztonsági mentéshez. Javasoljuk, hogy ezt a házirendet ne több mint 200 virtuális géphez rendelje hozzá. Ha a házirend több mint 200 virtuális géphez van hozzárendelve, akkor a biztonsági mentés néhány órával a definiált ütemezésen kívül is elindítható. Ez a szabályzat több virtuálisgép-lemezkép támogatásához lesz kialakítva. |deployIfNotExists, auditIfNotExists, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[A Recovery Services-tároló diagnosztikai beállításainak üzembe helyezése erőforrás-specifikus kategóriák Log Analytics munkaterületre.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |A Recovery Services tároló diagnosztikai beállításainak üzembe helyezése az erőforrás-specifikus kategóriák Log Analytics munkaterületre való továbbításához. Ha az erőforrás-specifikus kategóriák bármelyike nincs engedélyezve, létrejön egy új diagnosztikai beállítás. |deployIfNotExists |1.0.0 – előzetes verzió |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |

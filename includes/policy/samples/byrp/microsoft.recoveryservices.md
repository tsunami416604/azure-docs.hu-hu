---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/26/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3b169d7c460696d2ef6591c9108f9c7458b8d227
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82629804"
---
|Name (Név) |Leírás |Hatás (ok) |Verzió |GitHub |
|---|---|---|---|---|
|[A Azure Backup engedélyezni kell a Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F013e242c-8828-4970-87b3-ab247555486d) |Ez a szabályzat segít ellenőrizni, hogy Azure Backup szolgáltatás engedélyezve van-e az összes virtuális gépen. Azure Backup egy költséghatékony, egykattintásos biztonsági mentési megoldás egyszerűsíti az adathelyreállítást, és könnyebben engedélyezhető, mint a többi felhőalapú biztonsági mentési szolgáltatás. |AuditIfNotExists, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachines_EnableAzureBackup_Audit.json) |
|[Egy helyen lévő virtuális gépek biztonsági mentésének konfigurálása ugyanazon a helyen lévő központi tárba](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F09ce66bc-1220-4153-8104-e3f51c936913) |Ez a házirend egy adott helyen lévő virtuális gépek Azure Backup védelmét konfigurálja egy meglévő központi tárolóra ugyanazon a helyen. Csak azokra a virtuális gépekre vonatkozik, amelyek még nincsenek konfigurálva a biztonsági mentéshez. Javasoljuk, hogy ezt a házirendet ne több mint 200 virtuális géphez rendelje hozzá. Ha a házirend több mint 200 virtuális géphez van hozzárendelve, akkor a biztonsági mentés néhány órával a definiált ütemezésen kívül is elindítható. Ez a szabályzat több virtuálisgép-lemezkép támogatásához lesz kialakítva. |deployIfNotExists, auditIfNotExists, letiltva |1.0.0 |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Backup/VirtualMachineBackup_Backup_DeployIfNotExists.json) |
|[A Recovery Services-tároló diagnosztikai beállításainak üzembe helyezése erőforrás-specifikus kategóriák Log Analytics munkaterületre.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc717fb0c-d118-4c43-ab3d-ece30ac81fb3) |A Recovery Services tároló diagnosztikai beállításainak üzembe helyezése az erőforrás-specifikus kategóriák Log Analytics munkaterületre való továbbításához. Ha az erőforrás-specifikus kategóriák bármelyike nincs engedélyezve, létrejön egy új diagnosztikai beállítás. |deployIfNotExists |1.0.0 – előzetes verzió |[Hivatkozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/EnableRecoveryServiceVaultDiagnosticSetting_Backup_DeployIfNotExist.json) |

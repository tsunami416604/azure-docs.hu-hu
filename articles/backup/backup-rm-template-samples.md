---
title: Azure Resource Manager-sablonok az Azure Backuphoz
description: Azure Backup PowerShell-minták
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7bd502f86cabfcfb5048921d118802a780cd86e4
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464953"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Resource Manager-sablonok az Azure Backuphoz

A következő táblázat a helyreállítási tárakhoz és az Azure Backup funkcióihoz használható Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz. A JSON szintaxisáról és tulajdonságairól a [Microsoft. recoveryservices szolgáltatónál erőforrástípusok](/azure/templates/microsoft.recoveryservices/allversions)című témakörben olvashat bővebben.

|   |   |
|---|---|
|**Helyreállítási tár** | |
| [Helyreállítási tár létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Recovery Services-tároló létrehozása. A tár használható az Azure Backup és az Azure Site Recovery szolgáltatásokkal. |
|**Virtuális gépek biztonsági mentése**| |
| [A Resource Manager által telepített virtuális gépek biztonsági mentése](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Használja a meglévő helyreállítási tárat és biztonsági mentési szabályzatot az ugyanabban az erőforráscsoportban lévő, Resource Manager által telepített virtuális gépek biztonsági mentéséhez.|
| [IaaS virtuális gépek biztonsági mentése helyreállítási tárba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Sablon a klasszikus és a Resource Manager által telepített virtuális gépek biztonsági mentéséhez. |
| [Heti biztonsági mentési szabályzat létrehozása IaaS virtuális gépekhez](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | A sablon létrehoz Recovery Services-tárolót és egy heti biztonsági mentési szabályzatot, amely a klasszikus és a Resource Manager virtuális gépek biztonsági mentéséhez használható.|
| [Napi biztonsági mentési szabályzat létrehozása IaaS virtuális gépekhez](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | A sablon létrehozza a Recovery Services-tárolót és a napi biztonsági mentési szabályzatot, amely a klasszikus és a Resource Manager-alapú virtuális gépek biztonsági mentésére szolgál.|
| [Windows Server rendszerű virtuális gép telepítése engedélyezett biztonsági mentéssel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | A sablon létrehoz egy Windows Server rendszerű virtuális gépet és helyreállítási tárat az alapértelmezett biztonsági mentési szabályzattal.|
|**Biztonsági mentési feladatok monitorozása** |  |
| [Azure Monitor naplók használata a Azure Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | A sablon Azure Monitor naplókat helyez üzembe Azure Backupokkal, amelyekkel figyelheti a biztonsági mentési és visszaállítási feladatokat, a biztonsági mentési riasztásokat és a Recovery Services-tárolókban használt Felhőbeli tárhelyet.|  
|**SQL Server biztonsági mentése az Azure-beli virtuális gépen** |  |
| [SQL Server biztonsági mentése az Azure-beli virtuális gépen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | A sablon létrehoz egy Recovery Services-tárolót és a munkaterhelés-specifikus biztonsági mentési szabályzatot. Regisztrálja a virtuális gépet Azure Backup szolgáltatással, és konfigurálja a védelmet a virtuális gépen. Jelenleg csak az SQL Gallery-lemezképek esetében működik. |
|   |   |

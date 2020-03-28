---
title: Azure Resource Manager-sablonok
description: Azure Resource Manager-sablonok a Recovery Services-tárolókkal és az Azure Backup funkcióival való használatra
ms.topic: sample
ms.date: 01/31/2019
ms.custom: mvc
ms.openlocfilehash: 3c05f345c6c5df5d0350c75e52381c24b0609501
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74172167"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Resource Manager-sablonok az Azure Backuphoz

A következő táblázat a helyreállítási tárakhoz és az Azure Backup funkcióihoz használható Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz. A JSON szintaxisáról és tulajdonságairól a [Microsoft.RecoveryServices erőforrástípusok](/azure/templates/microsoft.recoveryservices/allversions)című témakörben olvashat.

|   |   |
|---|---|
|**Recovery Services-tároló** | |
| [Helyreállítási szolgáltatások tárolójának létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Recovery Services-tároló létrehozása. A tár használható az Azure Backup és az Azure Site Recovery szolgáltatásokkal. |
|**Virtuális gépek biztonsági mentése**| |
| [A Resource Manager által telepített virtuális gépek biztonsági mentése](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Használja a meglévő helyreállítási tárat és biztonsági mentési szabályzatot az ugyanabban az erőforráscsoportban lévő, Resource Manager által telepített virtuális gépek biztonsági mentéséhez.|
| [IaaS virtuális gépek biztonsági mentése helyreállítási tárba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Sablon a klasszikus és a Resource Manager által telepített virtuális gépek biztonsági mentéséhez. |
| [Heti biztonsági mentési szabályzat létrehozása IaaS virtuális gépekhez](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | Sablon létrehoz recovery szolgáltatások tároló és a heti biztonsági mentési szabályzat, amely a klasszikus és a Resource Manager-virtuális gépek biztonsági mentésére szolgál.|
| [Napi biztonsági mentési szabályzat létrehozása IaaS virtuális gépekhez](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | Sablon létrehoz recovery szolgáltatások tároló és a napi biztonsági mentési szabályzat, amely a klasszikus és a Resource Manager-virtuális gépek biztonsági mentésére szolgál.|
| [Windows Server rendszerű virtuális gép telepítése engedélyezett biztonsági mentéssel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | A sablon létrehoz egy Windows Server rendszerű virtuális gépet és helyreállítási tárat az alapértelmezett biztonsági mentési szabályzattal.|
|**Biztonsági mentési feladatok figyelése** |  |
| [Az Azure Monitor-naplók használata az Azure Backup segítségével](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | A sablon az Azure Backup segítségével telepíti az Azure Monitor-naplókat, amely lehetővé teszi a biztonsági mentési feladatok, a biztonsági mentési riasztások és a Recovery Services-tárolókban használt felhőalapú tárhely figyelését.|  
|**Az SQL Server biztonsági és biztonsági és biztonsági őrei az Azure Virtuális gépben** |  |
| [Az SQL Server biztonsági és biztonsági és biztonsági őrei az Azure Virtuális gépben](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | A sablon létrehoz egy Helyreállítási szolgáltatások tárolóját és a munkaterhelés-specifikus biztonsági mentési szabályzatot. Regisztrálja a virtuális gépet az Azure Backup szolgáltatással, és konfigurálja a védelmet az adott virtuális gépen. Jelenleg csak az SQL Gallery-képek esetén működik. |
|   |   |

---
title: Azure Resource Manager-sablonok az Azure Backuphoz
description: Azure Backup PowerShell-minták
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 01/31/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: bed2c002cacdac1e47852e6fa3181885af6733d2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236800"
---
# <a name="azure-resource-manager-templates-for-azure-backup"></a>Azure Resource Manager-sablonok az Azure Backuphoz

A következő táblázat a helyreállítási tárakhoz és az Azure Backup funkcióihoz használható Azure Resource Manager-sablonokra mutató hivatkozásokat tartalmaz. A JSON-szintaxist és a Tulajdonságok kapcsolatos további információkért lásd: [Microsoft.RecoveryServices erőforrástípusok](/azure/templates/microsoft.recoveryservices/allversions).

|   |   |
|---|---|
|**Helyreállítási tár** | |
| [Helyreállítási tár létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vault-create)| Recovery Services-tároló létrehozása. A tár használható az Azure Backup és az Azure Site Recovery szolgáltatásokkal. |
|**Virtuális gépek biztonsági mentése**| |
| [A Resource Manager által telepített virtuális gépek biztonsági mentése](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-backup-vms) | Használja a meglévő helyreállítási tárat és biztonsági mentési szabályzatot az ugyanabban az erőforráscsoportban lévő, Resource Manager által telepített virtuális gépek biztonsági mentéséhez.|
| [IaaS virtuális gépek biztonsági mentése helyreállítási tárba](https://github.com/Azure/azure-quickstart-templates/tree/master/201-recovery-services-backup-classic-resource-manager-vms) | Sablon a klasszikus és a Resource Manager által telepített virtuális gépek biztonsági mentéséhez. |
| [Heti biztonsági mentési szabályzat létrehozása IaaS virtuális gépekhez](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-weekly-backup-policy-create) | A sablon létrehoz a Recovery Services-tároló és a egy heti biztonsági mentési szabályzat, amely a klasszikus és Resource Manager által telepített virtuális gépek biztonsági mentéséhez használatos.|
| [Napi biztonsági mentési szabályzat létrehozása IaaS virtuális gépekhez](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-daily-backup-policy-create) | A sablon létrehoz a Recovery Services-tároló és a egy napi biztonsági mentési szabályzatot, amely a klasszikus és Resource Manager által telepített virtuális gépek biztonsági mentéséhez használatos.|
| [Windows Server rendszerű virtuális gép telepítése engedélyezett biztonsági mentéssel](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-create-vm-and-configure-backup) | A sablon létrehoz egy Windows Server rendszerű virtuális gépet és helyreállítási tárat az alapértelmezett biztonsági mentési szabályzattal.|
|**Biztonsági mentési feladatok monitorozása** |  |
| [Az Azure Monitor naplóira használata az Azure Backup szolgáltatással](https://github.com/Azure/azure-quickstart-templates/tree/master/101-backup-oms-monitoring) | Sablon üzembe helyez az Azure Monitor-naplók az Azure Backup, amely lehetővé teszi, hogy a biztonsági mentés monitorozása és visszaállítási feladatok, biztonsági mentési riasztások és a Recovery Services-tárolók a használt Felhőbeli tárhelyhez.|  
|**SQL Server Azure virtuális gép biztonsági mentése** |  |
| [SQL Server Azure virtuális gép biztonsági mentése](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) | A sablon létrehoz egy Recovery Services-tároló és a számítási feladatok adott biztonsági mentési szabályzatot. A virtuális gép Azure Backup szolgáltatással, és konfigurálja a védelmet a virtuális gép regisztrálja azt. Jelenleg csak működik az SQL-katalógus rendszerképeinek letöltéséhez. |
|   |   |

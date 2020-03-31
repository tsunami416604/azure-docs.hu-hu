---
title: Azure Backup az Azure VM-ben futó SQL Server-kiszolgálóhoz
description: Ebből a cikkből megtudhatja, hogyan regisztrálhatja az Azure Backup-ot az Azure-alapú virtuális gépen futó SQL Serverben.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: b17e4031edaedc6b0a63d305d20a77e5b58f91ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247384"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup az Azure VM-ben futó SQL Server-kiszolgálóhoz

Az Azure Backup többek között támogatja a számítási feladatok, például az Azure-virtuális gépeken futó SQL Server biztonsági mentését. Mivel az SQL-alkalmazás egy Azure virtuális gépen belül fut, a biztonsági mentési szolgáltatásnak engedélyre van szüksége az alkalmazás eléréséhez és a szükséges adatok lekéréséhez.
Ehhez az Azure Backup telepíti az **AzureBackupWindowsWorkload** bővítményt a virtuális gépre, amelyben az SQL Server fut, a felhasználó által aktivált regisztrációs folyamat során.

## <a name="prerequisites"></a>Előfeltételek

A támogatott forgatókönyvek listáját tekintse meg az Azure Backup által támogatott [támogatási mátrix.](../../backup/sql-support-matrix.md#scenario-support)

## <a name="network-connectivity"></a>Hálózati kapcsolat

Az Azure Backup támogatja az NSG-címkéket, proxykiszolgáló vagy felsorolt IP-tartományok telepítését; az egyes módszerekkel kapcsolatos részletekért olvassa el ezt a [cikket](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Bővítményséma

A bővítményséma és a tulajdonságértékek azok a konfigurációs értékek (futásidejű beállítások), amelyeket a szolgáltatás a CRP API-nak ad át. Ezeket a konfigurációs értékeket a regisztráció és a frissítés során használjuk. **Az AzureBackupWindowsWorkload** bővítmény is ezt a sémát használja. A séma előre be van állítva; új paraméter adható hozzá az objectStr mezőhöz

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

A következő JSON a WorkloadBackup bővítmény sémáját mutatja.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Tulajdonság értékek

Név | Érték/példa | Adattípus
 --- | --- | ---
locale | hu-hu  |  sztring
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | sztring
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzJMjQqxYTItOGRjNy00ZGE5LWI4NTMtMjjJTJNDZlM2ZkIiwiSWRNZ210Q 29udGFpbmVySWQiOjM0NTY3ODg5LCSzXNvdXJjZUlkIjoiMDUIMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMYMGVjNjVhihiiwiU3Vic2NyaXAW9uSWQiOi JjkNGEzOTliNy1iYjAyjAyLTQ2MWMWMTODddmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZDjDDDDDDNTQ4OS00NMNhLWEyZTctMZ NhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIndf1Q ==" | sztring
commandStartTimeUTCTicks | "636967192566036845"  | sztring
vmType  | "microsoft.compute/virtualmachines"  | sztring
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzJMjQqxYTItOGRjNy00ZGE5LWI4NTMtMjjJTJNDZlM2ZkIiwiSWRNZ210Q 29udGFpbmVySWQiOjM0NTY3ODg5LCSzXNvdXJjZUlkIjoiMDUIMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMYMGVjNjVhihiiwiU3Vic2NyaXAW9uSWQiOi JjkNGEzOTliNy1iYjAyjAyLTQ2MWMWMTODddmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZDjDDDDDDNTQ4OS00NMNhLWEyZTctMZ NhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIndf1Q ==" | sztring
logsBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | sztring
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | sztring

## <a name="template-deployment"></a>Sablonalapú telepítés

Azt javasoljuk, hogy az AzureBackupWindowsWorkload bővítmény hozzáadása egy virtuális géphez az SQL Server biztonsági mentésének engedélyezésével történik a virtuális gépen. Ez az SQL Server virtuális gép biztonsági mentésének automatizálására tervezett [Erőforrás-kezelő sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) érhető el.

## <a name="powershell-deployment"></a>PowerShell-telepítés

Az SQL-alkalmazást egy Recovery services-tárolóval tartalmazó Azure virtuális gépet "regisztrálnia" kell. A regisztráció során az AzureBackupWindowsWorkload bővítmény települ a virtuális gépre. A VM regisztrálásához használja a [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) parancsmamot.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

A parancs visszaadja az erőforrás **biztonsági mentési tárolóját,** és az állapot **regisztrálva**lesz.

## <a name="next-steps"></a>További lépések

- [További információ](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) az Azure SQL Server virtuális gép biztonsági mentésével kapcsolatos hibaelhárítási irányelvekről
- [Gyakori kérdések](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) az Azure virtuális gépeken (VM-eken) futó ÉS az Azure Backup szolgáltatást használó SQL Server-adatbázisok biztonsági mentésével kapcsolatban.

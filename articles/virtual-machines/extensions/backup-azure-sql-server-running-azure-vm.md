---
title: Az Azure virtuális Gépen futó SQL Server az Azure Backup
description: Az Azure Backup az SQL Server Azure virtuális Gépen futó regisztrálása
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: swatisachdeva
ms.openlocfilehash: 8710242e04156c8af6e5882a3cb4d42cc31e3677
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607610"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Az Azure virtuális Gépen futó SQL Server az Azure Backup

Egyéb megoldások többek között az Azure Backup támogatja az például az SQL Server számítási feladatok biztonsági másolatának az Azure-beli virtuális gépeken futó. SQL-alkalmazás egy Azure virtuális Gépen belül fut, mivel a biztonsági mentési szolgáltatás kér engedélyt az alkalmazás eléréséhez, és beolvassa a szükséges adatokat.
Ehhez az Azure Backup telepíti a **AzureBackupWindowsWorkload** bővítményt a virtuális gépen, amely az SQL Server fut, a regisztráció során a felhasználó által aktivált.

## <a name="prerequisites"></a>Előfeltételek

Támogatott helyzetek listájáért tekintse meg a [támogatási mátrix](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) az Azure Backup által támogatott.

## <a name="network-connectivity"></a>Hálózati kapcsolat

Az Azure Backup támogatja az NSG-címkék és proxykiszolgáló üzembe helyezéséhez, vagy a felsorolt IP-címtartományok; az egyes módszerek részletes ismertetését a [cikk](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Bővítményséma

A bővítmény séma- és tulajdonság értékei a konfigurációs értékeket (futásidő beállításait), amely a szolgáltatás, CRP API továbbítja. Ezeket a konfigurációs értékeket regisztráció és a frissítés során használt. **AzureBackupWindowsWorkload** bővítmény is használhatja a sémát. A séma előre beállított; új paraméter is hozzáadhatók a objectStr mező

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

A következő JSON a WorkloadBackup bővítmény sémáját jeleníti meg.  

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

### <a name="property-values"></a>Tulajdonságok értékei

Name (Név) | Érték és példa | Adattípus
 --- | --- | ---
Területi beállítás | en-us  |  Karakterlánc
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | Karakterlánc
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ == " | sztring
commandStartTimeUTCTicks | "636967192566036845"  | Karakterlánc
vmType  | "microsoft.compute/virtualmachines"  | Karakterlánc
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ == " | Karakterlánc
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sztring
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sztring


## <a name="template-deployment"></a>Sablonalapú telepítés

Azt javasoljuk, hogy az SQL Server biztonsági másolat, a virtuális gépen engedélyezésével AzureBackupWindowsWorkload bővítményt a virtuális gép hozzáadása. Ez a keresztül érhető el a [Resource Manager-sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) automatizálja a virtuális gép az SQL Server biztonsági másolat készült.


## <a name="powershell-deployment"></a>PowerShell környezetben végzett telepítés

"Regisztráció" az Azure virtuális Gépen, amely tartalmazza az SQL-alkalmazást a Recovery services-tároló kell. Regisztráció során AzureBackupWindowsWorkload bővítmény telepíti a virtuális gépen. Használat [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) parancsmagot, hogy regisztrálja a virtuális Gépet.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
A parancs visszaadja a **biztonsági mentési tároló** ezen erőforrás és az állapot lesz **regisztrált**.


## <a name="next-steps"></a>További lépések

- [További](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) Azure SQL Server virtuális gép biztonsági mentésével kapcsolatos hibaelhárítási irányelvek
- [Gyakori kérdések](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) futtató Azure virtuális gépeken (VM), és az Azure Backup szolgáltatást használó SQL Server-adatbázisok biztonsági mentésével kapcsolatos.

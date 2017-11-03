---
title: "Azure Backup Server v2 csendes telepítése |} Microsoft Docs"
description: "PowerShell parancsfájl segítségével csendes telepítéséhez az Azure Backup Server v2. Az ilyen típusú telepítés felügyelet nélküli telepítés néven is ismert."
services: backup
documentationcenter: " "
author: markgalioto
manager: carmonm
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/30/2017
ms.author: markgal;masaran
ms.openlocfilehash: 91778a67f9ef523aa87b7918197e0d0ded0f5702
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="run-an-unattended-installation-of-azure-backup-server-v2"></a>Az Azure Backup Server v2 felügyelet nélküli telepítés futtatása

Útmutató az Azure Backup Server v2 felügyelet nélküli telepítés futtatása. 

Ezeket a lépéseket ne alkalmazza az Azure Backup Server v1 telepítésekor.

## <a name="install-backup-server-v2"></a>Telepítse a helykiszolgáló biztonsági mentése v2

1. A kiszolgálón, amelyen az Azure Backup Server v2 hozzon létre egy szövegfájlt. (A fájlt létrehozhatja a Jegyzettömbben vagy más szövegszerkesztőben.) Mentse a fájlt MABSSetup.ini. 

2. Illessze be a következő kódot a MABSSetup.ini fájlban. Cserélje le a szöveget a zárójelben (\< \>) a környezet értékeivel. A következő egy példa áll:

  ```
  [OPTIONS]
  UserName=administrator
  CompanyName=<Microsoft Corporation>
  SQLMachineName=localhost
  SQLInstanceName=<SQL instance name>
  SQLMachineUserName=administrator
  SQLMachinePassword=<admin password>
  SQLMachineDomainName=<machine domain>
  ReportingMachineName=localhost
  ReportingInstanceName=<reporting instance name>
  SqlAccountPassword=<admin password>
  ReportingMachineUserName=<username>
  ReportingMachinePassword=<reporting admin password>
  ReportingMachineDomainName=<domain>
  VaultCredentialFilePath=<vault credential full path and complete name>
  SecurityPassphrase=<passphrase>
  PassphraseSaveLocation=<passphrase save location>
  UseExistingSQL=<1/0 use or do not use existing SQL>
  ```

3. Mentse a fájlt. Ezután, egy rendszergazda jogú parancssort a telepítési kiszolgálón, írja be ezt a parancsot:

  ```
  start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
  ```

Ezeknek a jelzőknek a telepítéshez használható:</br>
**/f**: .ini-fájl elérési útja</br>
**/ l**: napló elérési útja</br>
**/i**: telepítési útvonala</br>
**/x**: távolítsa el az elérési út</br>

## <a name="next-steps"></a>Következő lépések
Biztonsági kiszolgáló telepítése után megtudhatja, hogyan készíti elő a kiszolgálót, vagy indítsa el a védelmet a munkaterhelés.

- [A kiszolgálói biztonsági mentési feladatok előkészítése](backup-azure-microsoft-azure-backup.md)
- [Készítsen biztonsági másolatot a VMware server Backup Server használatával](backup-azure-backup-server-vmware.md)
- [Készítsen biztonsági másolatot az SQL Server biztonsági másolat-kiszolgáló használatával](backup-azure-sql-mabs.md)
- [Modern biztonsági másolatokat tároló hozzáadása a helykiszolgáló biztonsági mentése](backup-mabs-add-storage.md)

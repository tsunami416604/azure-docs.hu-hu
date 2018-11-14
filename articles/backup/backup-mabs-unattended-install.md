---
title: Az Azure Backup Server V2 beavatkozás nélküli telepítés
description: Egy PowerShell-parancsprogram használatával beavatkozás nélkül telepítse az Azure Backup Server V2. Ez a fajta telepítés felügyelet nélküli telepítés néven is ismert.
services: backup
author: markgalioto
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: markgal
ms.openlocfilehash: 8f7bda070e613e5495d3733dd1610cb291e12c36
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2018
ms.locfileid: "51612695"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Az Azure Backup Server felügyelet nélküli telepítés futtatása

Ismerje meg, hogyan futtathat az Azure Backup Server felügyelet nélküli telepítéséhez.

Ha az Azure Backup Server V1 telepíti ezeket a lépéseket nem érvényesek.

## <a name="install-backup-server"></a>Backup Server telepítése

1. A kiszolgáló üzemelteti az Azure Backup Server V2 vagy újabb, hozzon létre egy szövegfájlt. (Létrehozhat a fájlt a Jegyzettömbben vagy más szövegszerkesztőben.) Mentse a fájlt MABSSetup.ini.

2. Illessze be a következő kódot a MABSSetup.ini fájlban. Cserélje le a zárójelben a szöveget (\< \>) környezete értékeivel. A következő egy példa a következő szöveget:

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

Ezek a jelölők a telepítéshez használható:</br>
**/f**: .ini-fájl elérési útja</br>
**/ l**: napló elérési útja</br>
**/i**: telepítési útvonal</br>
**/x**: elérési út eltávolítása</br>

## <a name="next-steps"></a>További lépések
Miután telepítette a Backup Server, megtudhatja, hogyan készíti elő a kiszolgálót, vagy a munkaterhelések védelmének megkezdése.

- [Backup Server számítási feladatainak előkészítése](backup-azure-microsoft-azure-backup.md)
- [VMware-kiszolgáló biztonsági mentése Backup Server használatával](backup-azure-backup-server-vmware.md)
- [SQL Server biztonsági mentése Backup Server használatával](backup-azure-sql-mabs.md)
- [Biztonsági mentési kiszolgálóra a Modern Backup Storage hozzáadása](backup-mabs-add-storage.md)

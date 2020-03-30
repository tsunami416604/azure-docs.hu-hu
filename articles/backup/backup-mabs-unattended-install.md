---
title: Az Azure Backup Server V2 csendes telepítése
description: PowerShell-parancsfájl használatával csendben telepítheti az Azure Backup Server V2-t. Ezt a fajta telepítést felügyelet nélküli telepítésnek is nevezik.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 1539089e713bcf8e959707c6ff4a608f062a7c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74172246"
---
# <a name="run-an-unattended-installation-of-azure-backup-server"></a>Az Azure Backup Server felügyelet nélküli telepítésének futtatása

Ismerje meg, hogyan futtatható az Azure Backup Server felügyelet nélküli telepítése.

Ezek a lépések nem vonatkoznak, ha az Azure Backup Server V1 telepítése.

## <a name="install-backup-server"></a>A biztonsági másolat kiszolgálójának telepítése

1. Az Azure Backup Server V2 vagy újabb rendszert tartalmazó kiszolgálón hozzon létre egy szövegfájlt. (A fájlt a Jegyzettömbben vagy egy másik szövegszerkesztőben is létrehozhatja.) Mentse a fájlt MABSSetup.ini fájlként.

2. Illessze be a következő kódot a MABSSetup.ini fájlba. Cserélje le a szögletes zárójelben (\< \>) lévő szöveget a környezetből származó értékekre. A következő szöveg egy példa:

   ```text
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

3. Mentse a fájlt. Ezután a telepítési kiszolgáló nt.rendszergazdai parancssorába írja be ezt a parancsot:

   ```cmd
   start /wait <cdlayout path>/Setup.exe /i  /f <.ini file path>/setup.ini /L <log path>/setup.log
   ```

A telepítéshez használhatja a következő jelzőket:</br>
**/f**: .ini fájl elérési útja</br>
**/l**: Napló elérési útja</br>
**/i**: Telepítési útvonal</br>
**/x**: Elérési út eltávolítása</br>

## <a name="next-steps"></a>További lépések

A Biztonsági másolat kiszolgáló telepítése után ismerje meg, hogyan készítheti elő a kiszolgálót, vagy hogyan védheti meg a munkaterhelést.

- [Biztonságimásolat-kiszolgáló munkaterhelésének előkészítése](backup-azure-microsoft-azure-backup.md)
- [VMware-kiszolgáló biztonsági mentése a Biztonsági másolat kiszolgálójával](backup-azure-backup-server-vmware.md)
- [Az SQL Server biztonsági mentése a Biztonsági másolat kiszolgálójával](backup-azure-sql-mabs.md)
- [Modern biztonságimentési tároló hozzáadása a biztonsági másolat kiszolgálóhoz](backup-mabs-add-storage.md)

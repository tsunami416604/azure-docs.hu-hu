---
title: Az SQL Azure veremben erőforrás-szolgáltató karbantartása |} Microsoft Docs
description: Ismerje meg, hogyan kezelheti az SQL erőforrás-szolgáltató szolgáltatás Azure veremben.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: ad899739dab1dc51d64368d2136ab87f73f6f3a0
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36300910"
---
# <a name="sql-resource-provider-maintenance-operations"></a>SQL erőforrás-szolgáltató karbantartási műveletek

Az SQL erőforrás-szolgáltató egy zárolt virtuális gépen fut. Ahhoz, hogy a karbantartási műveleteket, szeretné frissíteni a virtuális gép biztonsági. Ehhez használja a legalacsonyabb jogosultsági szint elvét, segítségével [PowerShell csak elég adminisztrációs (JEA)](https://docs.microsoft.com/en-us/powershell/jea/overview) végpont *DBAdapterMaintenance*. Az erőforrás-szolgáltató telepítési csomag tartalmaz egy parancsfájlt, ehhez a művelethez.

## <a name="patching-and-updating"></a>Javítás és frissítése

Az erőforrás-szolgáltató SQL Azure verem részeként nem javítás, mert egy bővítmény összetevő. Microsoft SQL erőforrás-szolgáltató szükséges frissítéseit tartalmazza. Egy frissített SQL adapter megjelenésekor, a parancsfájl a frissítés alkalmazásához valósul meg. Ezt a parancsfájlt hoz létre egy új erőforrás-szolgáltató VM, a régi szolgáltató VM állapotának áttelepítése az új virtuális géphez. További információkért lásd: [frissítse az SQL erőforrás-szolgáltató](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Virtuális gép szolgáltató

Mivel az erőforrás-szolgáltató fut egy *felhasználói* virtuális gép, alkalmaznia kell a szükséges javítások és frissítések most kiadásakor. A Windows update csomagok által biztosított javítási és frissítési ciklusában részeként frissítések alkalmazása a virtuális gép használja.

## <a name="backuprestoredisaster-recovery"></a>Backup/Restore/katasztrófa utáni helyreállítás

 Mivel bővítménye, az SQL erőforrás-szolgáltató nem készül biztonsági mentés egy Azure verem üzleti folytonossági vész helyreállítási (BCDR) folyamat részeként. Parancsfájlok fognak adni a következő műveletek:

- Biztonsági mentése állapotadatokat (az Azure-verem tárfiókot tárolni.)
- Az erőforrás-szolgáltató visszaállítása, ha a teljes verem helyreállítására olyankor szükség.

>[!NOTE]
>Ha a helyreállítás elvégzéséhez, adatbázis-kiszolgálók helyre kell állítani az erőforrás-szolgáltató visszaállítására.

## <a name="updating-sql-credentials"></a>SQL-hitelesítő adatainak frissítése

Ön a felelős létrehozására és karbantartására rendszergazdai fiókot az SQL-kiszolgálón. Az erőforrás-szolgáltató ezen adatbázisok felhasználók kezelése engedéllyel rendelkező fiók szükséges, de nem kell azt a felhasználói adatok eléréséhez. Frissítse az SQL Server-kiszolgálókon a sysadmin (rendszergazda) jelszavakat kell, ha az erőforrás-szolgáltató rendszergazdai felület segítségével jelszó módosítása. Ezek a jelszavak a kulcstároló, az Azure-verem példányon tárolják.

A beállítások módosításához válassza **Tallózás** &gt; **felügyeleti erőforrások** &gt; **SQL üzemeltető kiszolgálók** &gt; **SQL-Bejelentkezésekben** , és válassza ki a felhasználót. A módosítani kell az SQL-példányon először (és a replikákat, ha szükséges.) A **beállítások**, jelölje be **jelszó**.

![Frissítés a rendszergazdai jelszó](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Titkos kulcsok Elforgatás

*Ezek az utasítások csak Azure verem integrált rendszerek verzió 1804 és később érvényesek. Ne kísérelje meg elforgatása előtti-1804 Azure verem verzióin titkos kulcsok.*

Az SQL és a MySQL-szolgáltatók használata Azure vermet be integrálva rendszerek, a következő infrastruktúra (telepítés) titkok forgathatók:

- Külső SSL-tanúsítvány [központi telepítés során megadott](azure-stack-pki-certs.md).
- Az erőforrás szolgáltató virtuális gép helyi rendszergazdai fiók jelszava központi telepítés során megadott.
- Erőforrás-szolgáltató diagnosztikai felhasználói (dbadapterdiag) jelszavát.

### <a name="powershell-examples-for-rotating-secrets"></a>PowerShell-példák a titkos kulcsok elforgatása

**Módosítsa a titkos kulcsok egy időben.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**A diagnosztikai felhasználói jelszó módosításához.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd
```

**Módosítsa a virtuális gép helyi rendszergazdai fiók jelszavát.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Módosítsa az SSL-tanúsítvány jelszavát.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>SecretRotationSQLProvider.ps1 paraméterek

|Paraméter|Leírás|
|-----|-----|
|AzCredential|Az Azure verem Szolgáltatásadminisztrátori fiók hitelesítő adatait.|
|CloudAdminCredential|Az Azure verem felhőalapú rendszergazdai tartományi fiók hitelesítő adatait.|
|PrivilegedEndpoint|Kiemelt végponton keresztül biztosít hozzáférést a Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnosztika felhasználói fiók jelszavát.|
|VMLocalCredential|Helyi rendszergazdai fiók a MySQLAdapter virtuális Gépre.|
|DefaultSSLCertificatePassword|Alapértelmezett SSL-tanúsítvány (* pfx) jelszavát.|
|DependencyFilesLocalPath|A függőségi fájlok helyi elérési útja.|
|     |     |

### <a name="known-issues"></a>Ismert problémák

**A probléma**: titkok Elforgatás naplókat.<br>
A titkos kulcsok Elforgatás vonatkozó naplókat, automatikusan nem gyűjt, ha a titkos Elforgatás egyéni parancsfájl sikertelen futtatásakor.

**Megkerülő megoldás**:<br>
Minden erőforrás a szolgáltató naplóiban találhatók, beleértve a AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log C:\Logs mentett összegyűjtéséhez használja a Get-AzsDBAdapterLogs parancsmag.

## <a name="update-the-virtual-machine-operating-system"></a>A virtuális gép operációs rendszerének frissítése

Használja a virtuális gép operációs rendszerének frissítése a következő módszerek egyikét.

- Telepítse a legújabb szolgáltató erőforráscsomag jelenleg javított Windows Server 2016 Core lemezképpel.
- A Windows Update csomag telepítéséhez telepítése során, vagy frissítse, az erőforrás-szolgáltató.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>A virtuális gép Windows Defender-definíciók frissítése

A Windows Defender-definíciók frissítése:

1. A Windows Defender-definíciók frissítéséhez letöltési [Windows Defender definíciós](https://www.microsoft.com/en-us/wdsi/definitions).

   A definíciók frissítése a lap, görgessen le a "Manuálisan töltse le és telepítse a definíciók". Töltse le a "Windows Defender a víruskereső Windows 10 és Windows 8.1" 64 bites fájlt.

   Másik megoldásként használhatja [a közvetlen hivatkozás](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) letöltési/elindul a fpam-fe.exe fájlt.

2. Hozzon létre egy PowerShell-munkamenetet a SQL erőforrás szolgáltató adapter virtuális gép karbantartási végponthoz.

3. A definíciók frissítési fájlok másolása a virtuális gépet a karbantartási végpont munkamenet használatával.

4. A karbantartási PowerShell-munkamenetben futtassa a *frissítés-DBAdapterWindowsDefenderDefinitions* parancsot.

5. Miután telepítette a definíciókat, azt javasoljuk, hogy törölje a definíciók frissítési fájlok használatával a *Remove-ItemOnUserDrive* parancsot.

**PowerShell-mintaparancsfájl definíciók frissítéséhez.**

Szerkesztheti, és futtassa a következő parancsfájlt, a Defender definícióinak frissítéséhez. A parancsfájlban szereplő értékek cserélje le a környezetből értékeket.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="collect-diagnostic-logs"></a>Diagnosztikai naplók gyűjtésére

Zárolt le a virtuális gép naplóinak gyűjtése, használhatja a PowerShell csak elég adminisztrációs (JEA) végpont *DBAdapterDiagnostics*. Ezt a végpontot biztosít a következő parancsokat:

- **Get-AzsDBAdapterLog**. Ez a parancs létrehoz egy zip-csomagját a erőforrás-szolgáltató diagnosztikai naplók, és menti a fájlt a munkamenet felhasználói meghajtón. Ez a parancs, paraméterek nélkül is futtathatja, és az utolsó négy órányi gyűjtött.
- **Remove-AzsDBAdapterLog**. Ez a parancs eltávolítja a meglévő napló-csomagokat a VM erőforrás-szolgáltató.

### <a name="endpoint-requirements-and-process"></a>Végpont követelményeiről és folyamatáról

Ha egy erőforrás-szolgáltató van telepítve, vagy frissíteni, a **dbadapterdiag** felhasználói fiók jön létre. Diagnosztikai naplók gyűjtésére ezt a fiókot fogja használni.

>[!NOTE]
>A dbadapterdiag jelszava megegyezik a helyi rendszergazda a virtuális gépen a szolgáltató központi telepítés vagy frissítés során létrehozott használt jelszót.

Használatához a *DBAdapterDiagnostics* parancsok, az erőforrás-szolgáltató virtuális géphez a távoli PowerShell-munkamenetet létrehozni, és futtassa a **Get-AzsDBAdapterLog** parancsot.

Az időtartam, amely segítségével beállíthatja a **FromDate** és **ToDate** paraméterek. Ha nem adja meg a következő paraméterek egyikét, az alábbi alapértelmezett használhatók:

- FromDate négyórás az aktuális időpont előtt.
- ToDate az eltelt idő.

**PowerShell-mintaparancsfájl naplók gyűjtésére szolgáló.**

Az alábbi parancsfájl bemutatja, hogyan diagnosztikai naplók gyűjtésére VM erőforrás-szolgáltató.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session.
$session | Remove-PSSession
```

## <a name="next-steps"></a>További lépések

[SQL Server kiszolgálót a kiszolgálók hozzáadása](azure-stack-sql-resource-provider-hosting-servers.md)

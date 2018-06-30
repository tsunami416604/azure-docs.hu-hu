---
title: A MySQL erőforrás-szolgáltató Azure veremben karbantartása |} Microsoft Docs
description: Ismerje meg, hogyan kezelheti a MySQL erőforrás-szolgáltató szolgáltatás Azure veremben.
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
ms.date: 06/29/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: bc1c96d2f027d459ca20fccb70cd94ac9e5cae94
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130138"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>MySQL erőforrás-szolgáltató karbantartási műveletek

A MySQL erőforrás-szolgáltató egy zárolt virtuális gépen fut. Ahhoz, hogy a karbantartási műveleteket, szeretné frissíteni a virtuális gép biztonsági. Ehhez használja a legalacsonyabb jogosultsági szint elvét, PowerShell csak elég adminisztrációs (JEA) végpont DBAdapterMaintenance is használhat. Az erőforrás-szolgáltató telepítési csomag tartalmaz egy parancsfájlt, ehhez a művelethez.

## <a name="update-the-virtual-machine-operating-system"></a>A virtuális gép operációs rendszerének frissítése

Mivel az erőforrás-szolgáltató fut egy *felhasználói* virtuális gép, alkalmaznia kell a szükséges javítások és frissítések most kiadásakor. A Windows update csomagok által biztosított javítási és frissítési ciklusában részeként frissítések alkalmazása a virtuális gép használja.

Frissítés a szolgáltató virtuális gépet a következő módszerek valamelyikével:

- Telepítse a legújabb szolgáltató erőforráscsomag jelenleg javított Windows Server 2016 Core lemezképpel.
- A Windows Update csomag telepítéséhez telepítése során, vagy frissítse az erőforrás-szolgáltató.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>A virtuális gép Windows Defender-definíciók frissítése

A Defender definíciók frissítéséhez kövesse az alábbi lépéseket:

1. A Windows Defender-definíciók frissítéséhez letöltési [Windows Defender definíciós](https://www.microsoft.com/en-us/wdsi/definitions).

    A definíciók lapon görgessen le a "Manuálisan töltse le és telepítse a definíciók". Töltse le a "Windows Defender a víruskereső Windows 10 és Windows 8.1" 64 bites fájlt.

    Másik megoldásként használhatja [a közvetlen hivatkozás](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) letöltési/elindul a fpam-fe.exe fájlt.

2. Nyisson meg egy PowerShell-munkamenetet a MySQL erőforrás szolgáltató adapter virtuális gép karbantartási végponthoz.

3. Másolja a definíciók frissítési fájlt az erőforrás-szolgáltató adapter a karbantartási végpont munkamenet használó virtuális gépek.

4. A karbantartási PowerShell-munkamenetben futtassa a _frissítés-DBAdapterWindowsDefenderDefinitions_ parancsot.

5. Miután telepítette a definíciókat, azt javasoljuk, hogy törölje a definíciók frissítési fájlok használatával a _Remove-ItemOnUserDrive)_ parancsot.

**PowerShell-mintaparancsfájl definíciók frissítéséhez.**

Szerkesztheti, és futtassa a következő parancsfájlt, a Defender definícióinak frissítéséhez. A parancsfájlban szereplő értékek cserélje le a környezetből értékeket.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
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

## <a name="secrets-rotation"></a>Titkos kulcsok Elforgatás

*Ezek az utasítások csak Azure verem integrált rendszerek verzió 1804 és később érvényesek. Ne kísérelje meg forgassa el a titkos kulcsok Azure verem előtti-1804 verzióiban.*

Az SQL és a MySQL-szolgáltatók használata Azure vermet be integrálva rendszerek, a következő infrastruktúra (telepítés) titkok forgathatók:

- Külső SSL-tanúsítvány [központi telepítés során megadott](azure-stack-pki-certs.md).
- Az erőforrás szolgáltató virtuális gép helyi rendszergazdai fiók jelszava központi telepítés során megadott.
- Erőforrás-szolgáltató diagnosztikai felhasználói (dbadapterdiag) jelszavát.

### <a name="powershell-examples-for-rotating-secrets"></a>PowerShell-példák a titkos kulcsok elforgatása

**Módosítsa a titkos kulcsok egy időben.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**A diagnosztikai felhasználói jelszó módosításához.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd

```

**Módosítsa a virtuális gép helyi rendszergazdai fiók jelszavát.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Módosítsa az SSL-tanúsítvány jelszavát.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>SecretRotationMySQLProvider.ps1 paraméterek

|Paraméter|Leírás|
|-----|-----|
|AzCredential|Az Azure verem Szolgáltatásadminisztrátori fiók hitelesítő adatait.|
|CloudAdminCredential|Az Azure verem felhőalapú rendszergazdai tartományi fiók hitelesítő adatait.|
|PrivilegedEndpoint|Kiemelt végponton keresztül biztosít hozzáférést a Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnosztika felhasználói fiók jelszavát.|
|VMLocalCredential|A helyi rendszergazdai fiók a MySQLAdapter virtuális Gépre.|
|DefaultSSLCertificatePassword|Alapértelmezett SSL-tanúsítvány (* pfx) jelszavát.|
|DependencyFilesLocalPath|A függőségi fájlok helyi elérési útja.|
|     |     |

### <a name="known-issues"></a>Ismert problémák

**Probléma:**<br>
A titkos kulcsok Elforgatás vonatkozó naplókat, automatikusan nem gyűjt, ha a titkos Elforgatás parancsfájl sikertelen futtatásakor.

**Megkerülő megoldás:**<br>
A Get-AzsDBAdapterLogs parancsmag segítségével a erőforrás szolgáltató naplófájlokat, beleértve a AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log C:\Logs mentett gyűjtése.

## <a name="collect-diagnostic-logs"></a>Diagnosztikai naplók gyűjtésére

A zárolt virtuális gép naplóinak gyűjtése, a PowerShell csak elég adminisztrációs (JEA) végpont DBAdapterDiagnostics is használhat. Ezt a végpontot biztosít a következő parancsokat:

- **Get-AzsDBAdapterLog**. Ez a parancs létrehoz egy zip-csomagját a erőforrás-szolgáltató diagnosztikai naplók, és menti a fájlt a munkamenet felhasználói meghajtón. Ez a parancs, paraméterek nélkül is futtathatja, és az utolsó négy órányi gyűjtött.

- **Remove-AzsDBAdapterLog**. Ez a parancs eltávolítja a meglévő napló-csomagokat a VM erőforrás-szolgáltató.

### <a name="endpoint-requirements-and-process"></a>Végpont követelményeiről és folyamatáról

Ha egy erőforrás-szolgáltató van telepítve, vagy frissített, a dbadapterdiag felhasználói fiók jön létre. Diagnosztikai naplók gyűjtésére ezt a fiókot fogja használni.

>[!NOTE]
>A dbadapterdiag jelszava megegyezik a helyi rendszergazda a virtuális gépen a szolgáltató központi telepítés vagy frissítés során létrehozott használt jelszót.

Használatához a _DBAdapterDiagnostics_ parancsok, az erőforrás-szolgáltató virtuális géphez a távoli PowerShell-munkamenetet létrehozni, és futtassa a **Get-AzsDBAdapterLog** parancsot.

Az időtartam, amely segítségével beállíthatja a **FromDate** és **ToDate** paraméterek. Ha nem adja meg a következő paraméterek egyikét, az alábbi alapértelmezett használhatók:

* FromDate négyórás az aktuális időpont előtt.
* ToDate az eltelt idő.

**PowerShell-mintaparancsfájl naplók gyűjtésére szolgáló.**

Az alábbi parancsfájl bemutatja, hogyan diagnosztikai naplók gyűjtésére VM erőforrás-szolgáltató.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
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
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>További lépések

[A MySQL erőforrás-szolgáltató eltávolítása](azure-stack-mysql-resource-provider-remove.md)

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
ms.date: 06/11/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e7ddbe1235b3957a1e0cb7693ee728bfdbf9db6b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295659"
---
# <a name="maintenance-operations"></a>A karbantartási műveleteket 
Az SQL erőforrás-szolgáltató nem egy zárolt a virtuális gép működik. Az erőforrás szolgáltató virtuális gép biztonsági frissítése végezhető el a PowerShell csak elég adminisztrációs (JEA) végpont _DBAdapterMaintenance_. A parancsfájl az e műveletek megkönnyítése érdekében a RP-telepítési csomaggal valósul meg.

## <a name="patching-and-updating"></a>Javítás és frissítése
Az SQL erőforrás-szolgáltató nem kiszolgált Azure verem részeként, mert az bővítménye. Microsoft fogják biztosítani a frissítések az SQL-erőforrás szolgáltatóhoz szükség szerint. Az erőforrás-szolgáltató SQL példányosítani egy _felhasználói_ virtuális gép az alapértelmezett szolgáltató előfizetésben. Ezért fontos adja meg a Windows javítások, vírusvédelmi aláírások, stb. A Windows frissítése a csomagok által biztosított javítási és frissítési ciklusában részét is lehet használni a frissítések alkalmazása a Windows virtuális gép. Egy frissített adapter megjelenésekor, a parancsfájl a frissítés alkalmazásához valósul meg. Ezt a parancsfájlt hoz létre egy új RP virtuális Gépet, és telepítse át a már állapotokat.

 ## <a name="backuprestoredisaster-recovery"></a>Backup/Restore/katasztrófa utáni helyreállítás
 Az SQL erőforrás-szolgáltató nincs biztonsági másolata Azure verem BC-vész-Helyreállítási folyamat részeként, mert az bővítménye. Parancsfájlok megkönnyítésére biztosítja:
- A szükséges állapotadatokat (egy verem Azure storage-fiókban tárolt) biztonsági mentése
- A függő Entitás visszaállítása, abban az esetben, ha a teljes verem helyreállítás akkor van szükség.
Adatbázis-kiszolgálók helyre kell állítani az első (ha szükséges), az erőforrás szolgáltató helyreáll.

## <a name="updating-sql-credentials"></a>SQL-hitelesítő adatainak frissítése
Ön felelősséggel tartozik létrehozására és karbantartására rendszer rendszergazdai fiókokhoz az SQL-kiszolgálón. Az erőforrás-szolgáltató ezen felhasználók nevében adatbázisok kezelése engedéllyel rendelkező fiók szükséges, mert nem kell ezeket az adatbázisokat az adatokhoz való hozzáférés. Ha az SQL-kiszolgálón a rendszergazdai (SA) jelszó frissíteni kell, a frissítési funkció az erőforrás-szolgáltató rendszergazdai felület segítségével az erőforrás-szolgáltató által használt tárolt jelszó megváltoztatásához. Ezek a jelszavak a kulcstároló, az Azure-verem példányon tárolják.

A beállítások módosításához kattintson **Tallózás** &gt; **felügyeleti erőforrások** &gt; **SQL üzemeltető kiszolgálók** &gt; **SQL-Bejelentkezésekben** válassza ki a bejelentkezési nevet. A módosítani kell az SQL-példányon először (és bármilyen replikákat, ha szükséges). Az a **beállítások** panelen, kattintson a **jelszó**.

![Frissítés a rendszergazdai jelszó](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Titkos kulcsok Elforgatás 
*Ezek az utasítások csak Azure verem integrált rendszerek verzió 1804 és később vonatkoznak. Ne kísérelje meg titkos elforgatás előtti-1804 Azure verem verzióin.*

Az SQL és a MySQL-szolgáltatók használata Azure vermet be integrálva rendszerek, a következő infrastruktúra (telepítés) titkok forgathatók:
- Külső SSL-tanúsítvány [központi telepítés során megadott](azure-stack-pki-certs.md).
- Az erőforrás szolgáltató virtuális gép helyi rendszergazdai fiók jelszava központi telepítés során megadott.
- Erőforrás-szolgáltató diagnosztikai felhasználói (dbadapterdiag) jelszavát.

### <a name="powershell-examples-for-rotating-secrets"></a>PowerShell-példák a titkos kulcsok elforgatása

**Módosítsa az összes titkos kulcsot egyszerre**
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

**Felhasználói jelszó módosítása csak**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    –DiagnosticsUserPassword  $passwd 
```

**Virtuális gép helyi rendszergazdai fiók jelszavának módosítása**
```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**SSL-tanúsítvány módosítása**
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
|DiagnosticsUserPassword|Diagnosztika felhasználói jelszavát.|
|VMLocalCredential|A helyi rendszergazdai fiók a MySQLAdapter a virtuális gépet.|
|DefaultSSLCertificatePassword|Alapértelmezett SSL-tanúsítvány (* pfx) jelszavát.|
|DependencyFilesLocalPath|A függőségi fájlok helyi elérési útja.|
|     |     |

### <a name="known-issues"></a>Ismert problémák
**A probléma**: A titkos kulcsok Elforgatás naplókat gyűjtött nem automatikusan, ha a titkos Elforgatás egyéni parancsfájl sikertelen futtatásakor.

**Megkerülő megoldás**: használja a Get-AzsDBAdapterLogs parancsmagot gyűjtött összes erőforrás szolgáltató naplók, többek között a AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log C:\Logs alatt.

## <a name="update-the-virtual-machine-operating-system"></a>A virtuális gép operációs rendszerének frissítése
Többféleképpen is lehet frissíteni a Windows Server virtuális Gépet:
- Telepítse a legújabb szolgáltató erőforráscsomag jelenleg javított Windows Server 2016 Core lemezkép használatával
- A telepítés vagy a függő Entitás frissítése során a Windows Update csomag telepítése

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>A virtuális gép Windows Defender-definíciók frissítése
Kövesse az alábbi lépéseket a Defender definícióinak frissítéséhez:

1. A Windows Defender-definíciók frissítéséhez letöltési [Windows Defender definíciós](https://www.microsoft.com/en-us/wdsi/definitions).

    Ez az oldal "Manuálisan töltse le és telepítse a definíciók" le "Windows Defender víruskereső Windows 10 és Windows 8.1" 64 bites fájlrendszer. 
    
    A közvetlen hivatkozás: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64.

2. Hozzon létre egy PowerShell-munkamenetben virtuális gép SQL RP adapter karbantartási végponthoz
3. A definíciók frissítési fájlok másolása a DB adapter a gépet, a karbantartási végpont munkamenet használatával
4. A karbantartási PowerShell a munkamenet meghívása a _frissítés-DBAdapterWindowsDefenderDefinitions_ parancs
5. Telepítése után ajánlott eltávolítani a használt definíciókat fájl. A karbantartási munkamenet használatával el kell távolítani a _Remove-ItemOnUserDrive)_ parancsot.


Íme egy minta parancsfájlt Defender definíciófrissítését a (helyettesítse be a címet, vagy a tényleges értékkel a virtuális gép neve):

```powershell
# Set credentials for the RP VM local admin user
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession 
```


## <a name="collect-diagnostic-logs"></a>Diagnosztikai naplók gyűjtésére
Az SQL erőforrás-szolgáltató nem egy zárolt a virtuális gép működik. Ha szükségessé válik a virtuális gép, egy PowerShell csak elég adminisztrációs (JEA) végpont gyűjteni _DBAdapterDiagnostics_ valósul meg erre a célra. Nincsenek elérhető ezen a végponton keresztül két parancsot:

- **Get-AzsDBAdapterLog**. Előkészíti a függő Entitás diagnosztikai naplók tartalmazó zip-csomagját, és hozzáadja a munkamenet felhasználói meghajtón. A parancs paraméter nélküli hívható, és összegyűjti a naplók utolsó négy óra.
- **Remove-AzsDBAdapterLog**. A szükségtelenné vált meglévő napló-csomagokat a VM erőforrás-szolgáltató

Egy felhasználói fiókot **dbadapterdiag** RP központi telepítés vagy frissítés a diagnosztika végpont RP naplók kibontott való csatlakozás során jön létre. A fiókhoz tartozó jelszó megegyezik a telepítés/frissítés közben a helyi rendszergazdai fiókhoz megadott jelszóval.

Ezek a parancsok használatához szüksége lesz az erőforrás-szolgáltató virtuális géphez a távoli PowerShell-munkamenetet létrehozni, és meghívja a parancsot. FromDate és ToDate paraméterekkel is megadhat. Egyik vagy mindkét esetben nem adja meg, ha a FromDate lesz az aktuális időpont előtt négy óra, és a ToDate lesz az aktuális idővel.

Ez a parancsfájlpélda bemutatja, hogy ezek a parancsok:

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
        -ConfigurationName DBAdapterDiagnostics

# Sample captures logs from the previous one hour
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup logs
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session
$session | Remove-PSSession
```

## <a name="next-steps"></a>További lépések
[SQL Server kiszolgálót a kiszolgálók hozzáadása](azure-stack-sql-resource-provider-hosting-servers.md)

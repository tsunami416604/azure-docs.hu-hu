---
title: "SQL-adatbázisok használata Azure veremben |} Microsoft Docs"
description: "Ismerje meg, hogyan telepítheti az SQL-adatbázisok Azure verem és a Gyorsműveletek központi telepítése az SQL Server erőforrás-szolgáltató adapter szolgáltatásként."
services: azure-stack
documentationCenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: 805e39dfdee3a23d4ddc196085be59788cee912a
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>SQL-adatbázis használata a Microsoft Azure veremben

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az SQL Server erőforrás-szolgáltató adapter segítségével teszi közzé az SQL-adatbázisok a szolgáltatásként [Azure verem](azure-stack-poc.md). Az erőforrás-szolgáltató telepítése, és csatlakoztassa egy vagy több SQL Server-példányokat, után és a felhasználók hozhat létre:
- Adatbázisok natív felhőalapú alkalmazásokhoz.
- SQL alapuló webhelyeket.
- SQL alapuló munkaterhelések.
Rendszerű virtuális gép (VM), amely futtatja az SQL Server, minden alkalommal, amikor nincs.

Az erőforrás-szolgáltató nem támogatja az összes adatbázis felügyeleti funkcióit biztosítja [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Például a rugalmas adatbáziskészletek és tárcsázza adatbázis teljesítményének felfelé és lefelé automatikusan képes nem érhetők el. Azonban az erőforrás biztosítja által támogatott szolgáltatók hasonló létrehozása, olvasása, frissítése és Törlés (CRUD) típusú műveletek. Az API nem található kompatibilis SQL-adatbázis.

## <a name="sql-resource-provider-adapter-architecture"></a>SQL erőforrás-szolgáltató adapter architektúrája
Az erőforrás-szolgáltató három összetevőből áll:

- **Az SQL erőforrás szolgáltató adapter VM**, vagyis a Windows rendszert futtató virtuális gépet a szolgáltató szolgáltatásoktól.
- **Az erőforrás-szolgáltató maga**, amely feldolgozza a kiépítési kérelmekre, és tesz elérhetővé adatbázis-erőforrások.
- **SQL Server kiszolgálók**, adatbázisok kapacitást nyújt üzemeltetési kiszolgáló neve.

Meg kell hozzon létre egy (vagy több) az SQL Server példányai és/vagy a külső SQL Server-példányokat hozzáférést biztosítanak.

> [!NOTE]
> Üzemeltető kiszolgálók Azure verem telepített integrált rendszerek bérlői előfizetéssel kell létrehozni. Az alapértelmezett szolgáltató előfizetésből nem hozhatók létre. A bérlői portálon vagy egy PowerShell munkamenetből rendelkező megfelelő bejelentkezési objektumokat létre kell hozni. Minden üzemeltetési kiszolgáló terhelhető virtuális gép, és megfelelő licenccel kell rendelkeznie. A szolgáltatás-rendszergazdát a bérlő előfizetés tulajdonosának lehet.

## <a name="deploy-the-resource-provider"></a>Az erőforrás-szolgáltató telepítése

1. Ha még nem tette meg, regisztrálja a szoftverfejlesztői készlet, és töltse le a Windows Server 2016 Datacenter Core kép letölthető piactér felügyelet használatával. A Windows Server 2016 Core lemezképet kell használnia. Parancsfájl használata létrehozásához egy [Windows Server 2016 kép](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Ügyeljen arra, hogy válassza ki a Core.)

2. Jelentkezzen be egy olyan gazdagépre, férhetnek hozzá a kiemelt végpont virtuális gép.

    - A Azure verem szoftverfejlesztői készlet telepítésekre jelentkezzen be a fizikai állomáson.

    - Több csomópontos rendszerek esetében a gazdagép egy rendszer, amely hozzáférhet a kiemelt végpont kell lennie.
    
    >[!NOTE]
    > A rendszer, ha a parancsfájl futtatása *kell* egy Windows 10 vagy Windows Server 2016 rendszert a legújabb verzióra a .NET-futtatókörnyezet telepítve lesz. Ellenkező esetben nem telepíthető. Az Azure verem SDK állomás megfelel-e ezt a feltételt.


3. Töltse le az SQL erőforrás-szolgáltató bináris. Futtassa a önkibontó kicsomagolása egy ideiglenes könyvtárhoz.

    >[!NOTE] 
    > Az erőforrás-szolgáltató build minimális megfelelő Azure verem rendelkezik. Győződjön meg arról, a megfelelő bináris futtató Azure verem verziójának letöltéséhez.

    | Az Azure verem build | Erőforrás-szolgáltató telepítő SQL |
    | --- | --- |
    | 1802: 1.0.180302.1 | [SQL RP 1.1.18.0 verziója](https://aka.ms/azurestacksqlrp1802) |
    | 1712: 1.0.180102.3, 1.0.180103.2 vagy 1.0.180106.1 (több csomópontos) | [SQL RP 1.1.14.0 verziója](https://aka.ms/azurestacksqlrp1712) |
    | 1711: 1.0.171122.1 | [SQL RP 1.1.12.0 verziója](https://aka.ms/azurestacksqlrp1711) |
    | 1710: 1.0.171028.1 | [SQL RP 1.1.8.0 verziója](https://aka.ms/azurestacksqlrp1710) |
  

4. Az Azure-verem legfelső szintű tanúsítvány veszi át a kiemelt végpont. Az Azure verem SDK-ban önaláírt tanúsítvány jön létre a folyamat során. Integrált rendszerek esetén meg kell adnia a megfelelő tanúsítványt.

   Adja meg a tanúsítvány, tegyen egy .pfx-fájlra a **DependencyFilesLocalPath** az alábbiak szerint:

    - Vagy egy helyettesítő tanúsítvány \*.dbadapter.\< a régióban\>.\< külső fqdn\> vagy egyhelyes tanúsítvány köznapi neve a sqladapter.dbadapter.\< a régióban\>.\< külső fqdn\>.

    - Ezt a tanúsítványt megbízhatónak kell lennie. Ez azt jelenti, hogy a megbízhatósági lánc léteznie kell anélkül, hogy a köztes tanúsítványok.

    - Csak egyetlen fájl a könyvtárban, a DependencyFilesLocalPath paraméter által hivatkozott létezhet.

    - A fájl neve nem tartalmazhat speciális karaktereket.


5. Nyissa meg a **új** emelt szintű (felügyeleti) PowerShell-konzolt, és módosítsa a könyvtárra, amelybe kibontotta a fájlokat. Egy új ablak segítségével a rendszer helytelen PowerShell-modul, amely már be van töltve az esetlegesen felmerülő problémák elkerülése érdekében.

6. [Telepítse az Azure PowerShell verziója 1.2.11](azure-stack-powershell-install.md).

7. Futtassa a DeploySqlProvider.ps1 parancsfájlt, amely végrehajtja ezeket a lépéseket:

    - A tanúsítványok és egyéb összetevők feltölt egy tárfiókot, Azure veremben.
    - Gyűjteményelem csomagok tesz közzé, úgy, hogy az SQL-adatbázisok a gyűjtemény telepítése.
    - Közzétesz egy gyűjteménycsomag üzemeltetési kiszolgáló telepítéséhez.
    - A virtuális gépek telepíti a Windows Server 2016 lemezkép, az 1. lépésben létrejött, és telepíti az erőforrás-szolgáltató használatával.
    - Regisztrálja a helyi DNS-rekordot, amely a virtuális gép erőforrás-szolgáltató van leképezve.
    - Az erőforrás-szolgáltató regisztrálása az a helyi Azure Resource Manager (felhasználó és rendszergazda).
    - Opcionálisan telepíti egy Windows-frissítés RP telepítése során

8. Azt javasoljuk, hogy töltse le a legújabb Windows Server 2016 Core kép a piactér felügyelet alól. Ha egy frissítés telepítésére van szüksége, elhelyezhet egy. A helyi függőségi elérési MSU-csomagot. Ha egynél több. MSU fájl található, a parancsfájl futtatása sikertelen lesz.


Íme egy példa a PowerShell-parancssorból futtatható. (Ne feledje módosítani a fiók- és igény szerint.)

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 parameters
Ezeket a paramétereket is megadhat a parancssorban. Ha nem, vagy bármely paraméter érvényesítése sikertelen, a szükséges paraméterek megadását kéri.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait. | _Szükséges_ |
| **AzCredential** | Az Azure-verem szolgáltatás rendszergazdai fiók hitelesítő adatait. Azure verem telepítéséhez használt használja ugyanazokat a hitelesítő adatokat. | _Szükséges_ |
| **VMLocalCredential** | Az SQL erőforrás-szolgáltató VM a helyi rendszergazdai fiók hitelesítő adatait. | _Szükséges_ |
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végpont DNS-nevét. |  _Szükséges_ |
| **DependencyFilesLocalPath** | A .pfx fájl a könyvtárban kell elhelyezni. | _Nem kötelező_ (_kötelező_ több csomópont) |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _Szükséges_ |
| **MaxRetryCount** | Ennyiszer azt szeretné, majd ismételje meg minden egyes művelet, ha hiba történik.| 2 |
| **RetryDuration** | Az időkorlát másodpercben az újrapróbálkozások között. | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltató és minden kapcsolódó erőforrások (lásd az alábbi megjegyzések). | Nem |
| **DebugMode** | Megakadályozza az automatikus tisztítás hiba esetén. | Nem |


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>A verem Azure portál használatával a telepítés ellenőrzése

> [!NOTE]
>  A telepítési parancsfájl végeztével akkor kell frissítenie a portálhoz, és a felügyeleti panelt.


1. Jelentkezzen be a felügyeleti portál a szolgáltatás-rendszergazdaként.

2. Győződjön meg arról, hogy a telepítés sikeres volt. Ugrás a **erőforráscsoportok**. Válassza ki a **system.\< hely\>.sqladapter** erőforráscsoportot. Győződjön meg arról, hogy sikerült-e négy követelményszabályait.

      ![Az SQL erőforrás-szolgáltató telepítésének ellenőrzése](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="update-the-sql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>Az SQL erőforrás-szolgáltató adapter (több csomópontos csak, buildek 1710 és újabb verziók) frissítése
Új SQL-erőforrás szolgáltató adapter szabaddá tehető Azure verem buildek frissítésekor. A meglévő adapter továbbra is működni fog, de javasolt frissítésére a legújabb buildjével a lehető leghamarabb. Ahhoz, telepíteni kell a frissítéseket: verziók nem hagyható ki (lásd a fenti táblázatban).

A frissítési folyamat hasonlít a korábban ismertetett telepítési folyamat. A legújabb erőforrás-szolgáltató kódot hoz létre egy új virtuális Gépet. Ezenkívül telepít át beállítások ezen új példányának, beleértve az adatbázis és a helyet adó kiszolgáló adatait. A szükséges DNS-rekordot is telepíti át.

A UpdateSQLProvider.ps1 parancsfájl használata, amely azt a korábban ismertetett ugyanazokkal az argumentumokkal. A tanúsítvány itt is meg kell adnia.

Azt javasoljuk, hogy töltse le a legújabb Windows Server 2016 Core kép a piactér felügyelet alól. Ha egy frissítés telepítésére van szüksége, elhelyezhet egy. A helyi függőségi elérési MSU-csomagot. Ha egynél több. MSU fájl található, a parancsfájl futtatása sikertelen lesz.


> [!NOTE]
> A frissítési folyamat csak az integrált rendszerekre vonatkozik.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="updatesqlproviderps1-parameters"></a>UpdateSQLProvider.ps1 parameters
Ezeket a paramétereket is megadhat a parancssorban. Ha nem, vagy bármely paraméter érvényesítése sikertelen, a szükséges paraméterek megadását kéri.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait. | _Szükséges_ |
| **AzCredential** | Az Azure-verem szolgáltatás rendszergazdai fiók hitelesítő adatait. Az Azure-verem telepítéséhez használt hitelesítő használja. | _Szükséges_ |
| **VMLocalCredential** | Az SQL erőforrás-szolgáltató VM a helyi rendszergazdai fiók hitelesítő adatait. | _Szükséges_ |
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végpont DNS-nevét. |  _Szükséges_ |
| **DependencyFilesLocalPath** | A .pfx fájl a könyvtárban kell elhelyezni. | _Nem kötelező_ (_kötelező_ több csomópont) |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _required_ |
| **MaxRetryCount** | Ennyiszer azt szeretné, majd ismételje meg minden egyes művelet, ha hiba történik.| 2 |
| **RetryDuration** |Az időkorlát másodpercben az újrapróbálkozások között. | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltató és minden kapcsolódó erőforrások (lásd az alábbi megjegyzések). | Nem |
| **DebugMode** | Megakadályozza az automatikus tisztítás hiba esetén. | Nem |


## <a name="collect-diagnostic-logs"></a>Diagnosztikai naplók gyűjtésére
Az SQL erőforrás-szolgáltató nem egy zárolt a virtuális gép működik. Ha szükségessé válik a virtuális gép, egy PowerShell csak elég adminisztrációs (JEA) végpont gyűjteni _DBAdapterDiagnostics_ valósul meg erre a célra. Nincsenek elérhető ezen a végponton keresztül két parancsot:

* Get-AzsDBAdapterLog - előkészíti a függő Entitás diagnosztikai naplók tartalmazó zip-csomagját, és hozzáadja a munkamenet felhasználói meghajtón. A parancs paraméter nélküli hívható, és összegyűjti a naplók utolsó négy óra.
* Remove-AzsDBAdapterLog - megtisztítja a meglévő napló-csomagokat a VM erőforrás-szolgáltató

Egy felhasználói fiókot _dbadapterdiag_ RP központi telepítés vagy frissítés a diagnosztika végpont RP naplók kibontott való csatlakozás során jön létre. A fiókhoz tartozó jelszó megegyezik a telepítés/frissítés közben a helyi rendszergazdai fiókhoz megadott jelszóval.

Ezek a parancsok használatához szüksége lesz az erőforrás-szolgáltató virtuális géphez a távoli PowerShell-munkamenetet létrehozni, és meghívja a parancsot. FromDate és ToDate paraméterekkel is megadhat. Egyik vagy mindkét esetben nem adja meg, ha a FromDate lesz az aktuális időpont előtt négy óra, és a ToDate lesz az aktuális idővel.

Ez a parancsfájlpélda bemutatja, hogy ezek a parancsok:

```
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<see above>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, $diagnosticsUserPassword)
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

## <a name="maintenance-operations-integrated-systems"></a>A karbantartási műveleteket (integrált rendszerek)
Az SQL erőforrás-szolgáltató nem egy zárolt a virtuális gép működik. Az erőforrás szolgáltató virtuális gép biztonsági frissítése végezhető el a PowerShell csak elég adminisztrációs (JEA) végpont _DBAdapterMaintenance_.

A parancsfájl az e műveletek megkönnyítése érdekében a RP-telepítési csomaggal valósul meg.

### <a name="update-the-virtual-machine-operating-system"></a>A virtuális gép operációs rendszerének frissítése
Többféleképpen is lehet frissíteni a Windows Server virtuális Gépet:
* Telepítse a legújabb szolgáltató erőforráscsomag jelenleg javított Windows Server 2016 Core lemezkép használatával
* A telepítés vagy a függő Entitás frissítése során a Windows Update csomag telepítése


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>A virtuális gép Windows Defender-definíciók frissítése

Kövesse az alábbi lépéseket a Defender definícióinak frissítéséhez:

1. A Windows Defender-definíciók frissítéséhez letöltési [Windows Defender definíciós](https://www.microsoft.com/en-us/wdsi/definitions)

    Ez az oldal "Manuálisan töltse le és telepítse a definíciók" le "Windows Defender víruskereső Windows 10 és Windows 8.1" 64 bites fájlrendszer. 
    
    A közvetlen hivatkozás: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Hozzon létre egy PowerShell-munkamenetben virtuális gép SQL RP adapter karbantartási végponthoz
3. A definíciók frissítési fájlok másolása a DB adapter a gépet, a karbantartási végpont munkamenet használatával
4. A karbantartási PowerShell a munkamenet meghívása a _frissítés-DBAdapterWindowsDefenderDefinitions_ parancs
5. Telepítése után ajánlott eltávolítani a használt definíciókat fájl. A karbantartási munkamenet használatával el kell távolítani a _Remove-ItemOnUserDrive)_ parancsot.


Íme egy minta parancsfájlt Defender definíciófrissítését a (helyettesítse be a címet, vagy a tényleges értékkel a virtuális gép neve):

```
# Set credentials for the diagnostic user
$diagPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$diagCreds = New-Object System.Management.Automation.PSCredential `
    ("dbadapterdiag", $vmLocalAdminPass)$diagCreds = Get-Credential

# Public IP Address of the DB adapter machine
$databaseRPMachine  = "XX.XX.XX.XX"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"
 
# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions. 
Invoke-WebRequest -Uri https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64 `
    -Outfile $localPathToDefenderUpdate 

# Create session to the maintenance endpoint
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $diagCreds -ConfigurationName DBAdapterMaintenance
# Copy defender update file to the db adapter machine
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\mpam-fe.exe"
# Install the update file
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinitions -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}
# Cleanup the definitions package file and session
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession
```

## <a name="remove-the-sql-resource-provider-adapter"></a>Az SQL erőforrás-szolgáltató adapter eltávolítása

Távolítsa el az erőforrás-szolgáltató, fontos először távolítsa el a függőségeket.

1. Győződjön meg arról, hogy rendelkezik-e az eredeti központi telepítési csomag ezen verzióját az SQL erőforrás-szolgáltató adapter letöltött.

2. Összes felhasználói adatbázis törölni kell az erőforrás-szolgáltató. (A felhasználói adatbázis törlése nem törli az adatokat.) Ez a feladat a felhasználók maguk kell elvégezni.

3. A rendszergazda az üzemeltetési kiszolgáló törölni kell az SQL erőforrás-szolgáltató adapter.

4. A rendszergazda törölnie kell az SQL erőforrás-szolgáltató adapter hivatkozó bármely tervek.

5. A rendszergazda minden termékváltozatok és az SQL erőforrás-szolgáltató adapter társított kvóták kell törölnie.

6. Futtassa újra a telepítési parancsprogram a következő elemeket:
    - A - paraméter eltávolítása
    - Az Azure Resource Manager-végpontok
    - A DirectoryTenantID
    - A szolgáltatás-rendszergazdai fiók hitelesítő adatait


## <a name="next-steps"></a>További lépések

[Adja hozzá az üzemeltetési kiszolgáló](azure-stack-sql-resource-provider-hosting-servers.md) és [adatbázisok létrehozására](azure-stack-sql-resource-provider-databases.md).

Próbálja más [PaaS szolgáltatások](azure-stack-tools-paas-services.md) hasonlóan a [MySQL Server erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md) és a [alkalmazásszolgáltatások erőforrás-szolgáltató](azure-stack-app-service-overview.md).

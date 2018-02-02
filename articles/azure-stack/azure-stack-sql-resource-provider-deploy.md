---
title: "SQL-adatbázisok használata Azure veremben |} Microsoft Docs"
description: "Ismerje meg, hogyan telepítheti az SQL-adatbázisok Azure verem és a Gyorsműveletek központi telepítése az SQL Server erőforrás-szolgáltató adapter szolgáltatásként."
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: JeffGo
ms.openlocfilehash: bf52ed4986b4e0930b57721c0e38bbf748045a36
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
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

Meg kell létrehozni az SQL Server egy (vagy több) intances és/vagy külső SQL Server-példányokat hozzáférést biztosítanak.

## <a name="deploy-the-resource-provider"></a>Az erőforrás-szolgáltató telepítése

1. Ha még nem tette meg, regisztrálja a szoftverfejlesztői készlet, és töltse le a Windows Server 2016 Datacenter Core kép letölthető piactér felügyelet használatával. A Windows Server 2016 Core lemezképet kell használnia. Parancsfájl használata létrehozásához egy [Windows Server 2016 kép](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Ügyeljen arra, hogy válassza ki a Core). A .NET 3.5 futásidejű már nincs szükség.

2. Jelentkezzen be egy olyan gazdagépre, férhetnek hozzá a kiemelt végpont virtuális gép.

    - A Azure verem szoftverfejlesztői készlet telepítésekre jelentkezzen be a fizikai állomáson.

    - Több csomópontos rendszerek esetében a gazdagép egy rendszer, amely hozzáférhet a kiemelt végpont kell lennie.
    
    >[!NOTE]
    > A rendszer, ha a parancsfájl futtatása *kell* egy Windows 10 vagy Windows Server 2016 rendszert a legújabb verzióra a .NET-futtatókörnyezet telepítve lesz. Ellenkező esetben nem telepíthető. Az Azure verem SDK állomás megfelel-e ezt a feltételt.


3. Töltse le az SQL erőforrás-szolgáltató bináris. Futtassa a önkibontó kicsomagolása egy ideiglenes könyvtárhoz.

    >[!NOTE] 
    > Az erőforrás-szolgáltató build Azure verem buildek felel meg. Győződjön meg arról, a megfelelő bináris futtató Azure verem verziójának letöltéséhez.

    | Az Azure verem build | Erőforrás-szolgáltató telepítő SQL |
    | --- | --- |
    |1.0.180102.3, 1.0.180103.2 vagy 1.0.180106.1 (több csomópontos) | [SQL RP 1.1.14.0 verziója](https://aka.ms/azurestacksqlrp1712) |
    | 1.0.171122.1 | [SQL RP 1.1.12.0 verziója](https://aka.ms/azurestacksqlrp1711) |
    | 1.0.171028.1 | [SQL RP 1.1.8.0 verziója](https://aka.ms/azurestacksqlrp1710) |
  

4. Az Azure-verem legfelső szintű tanúsítvány veszi át a kiemelt végpont. Az Azure verem SDK-ban önaláírt tanúsítvány jön létre a folyamat során. Több csomópontos meg kell adnia egy megfelelő tanúsítványt.

   Adja meg a tanúsítvány, tegyen egy .pfx-fájlra a **DependencyFilesLocalPath** az alábbiak szerint:

    - Vagy egy helyettesítő tanúsítvány \*.dbadapter.\< a régióban\>.\< külső fqdn\> vagy egyhelyes tanúsítvány köznapi neve a sqladapter.dbadapter.\< a régióban\>.\< külső fqdn\>.

    - Ezt a tanúsítványt megbízhatónak kell lennie. Ez azt jelenti, hogy a megbízhatósági lánc léteznie kell anélkül, hogy a köztes tanúsítványok.

    - Csak egyetlen tanúsítványfájlt a DependencyFilesLocalPath szerepel.

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

> [!NOTE]
> Ha a telepítés több mint 90 percig tart, előfordulhat, hogy. Ha nem sikerül, a képernyőn, majd a naplófájlban megjelenik egy hibaüzenet, de a központi telepítés a hibás lépés a rendszer ismét megkísérli. Előfordulhat, hogy a memória és vCPU ajánlott paramétereknek meg nem felelő rendszereket nem az SQL-resoure szolgáltató telepítéséhez.
>

Íme egy példa a PowerShell-parancssorból futtatható. (Ne feledje módosítani a fiók- és igény szerint.)

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM.
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
Az Azure-verem build frissül, amikor egy új SQL-erőforrás szolgáltató adapter megjelenik. A meglévő adapter esetleg tovább használhatók. Azt javasoljuk azonban frissítése a legújabb buildjével amint lehetséges az Azure-verem frissítése után. 

A frissítési folyamat hasonlít a korábban ismertetett telepítési folyamat. A legújabb erőforrás-szolgáltató kódot hoz létre egy új virtuális Gépet. Ezenkívül telepít át beállítások ezen új példányának, beleértve az adatbázis és a helyet adó kiszolgáló adatait. A szükséges DNS-rekordot is telepíti át.

A UpdateSQLProvider.ps1 parancsfájl használata, amely azt a korábban ismertetett ugyanazokkal az argumentumokkal. A tanúsítvány itt is meg kell adnia.

> [!NOTE]
> A frissítési folyamat csak többcsomópontos rendszereken támogatott.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack and the default prefix is AzS.
# For integrated systems, the domain and the prefix are the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

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

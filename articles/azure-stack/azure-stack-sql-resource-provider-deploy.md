---
title: SQL-adatbázisok használata Azure veremben |} Microsoft Docs
description: Ismerje meg, hogyan telepítheti az SQL-adatbázisok Azure verem és a Gyorsműveletek központi telepítése az SQL Server erőforrás-szolgáltató adapter szolgáltatásként.
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
ms.date: 05/01/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 20b289c16a73bd20ed020987116975c8abe893f0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>SQL-adatbázis használata a Microsoft Azure veremben
Az Azure verem SQL Server erőforrás-szolgáltató használatával teszi közzé az SQL-adatbázisok Azure verem szolgáltatásként. Az SQL-erőforrás-szolgáltató szolgáltatás fut az SQL-erőforrás-szolgáltató egy Windows Server core virtuális gép virtuális gép.

## <a name="prerequisites"></a>Előfeltételek
Nincsenek több előfeltételnek kell, mielőtt telepíthetné a verem SQL Azure erőforrás-szolgáltató szükséges. Egy olyan számítógépen, férhetnek hozzá a kiemelt végpont VM, hajtsa végre az alábbi lépéseket:

- Ön még nem tette meg, ha [regisztrálni Azure verem](.\azure-stack-registration.md) az Azure-ral, hogy az Azure piactéren elemek is letöltheti.
- A verem Azure piactér úgy, hogy letölti a szükséges Windows Server core virtuális gép hozzáadása a **Windows Server 2016 Server core** kép. Ha egy frissítés telepítésére van szüksége, elhelyezhet egy. A helyi függőségi elérési MSU-csomagot. Ha egynél több. MSU fájl található, SQL erőforrás-szolgáltató telepítése sikertelen lesz.
- Töltse le a bináris SQL erőforrás-szolgáltató, és futtassa az ideiglenes könyvtár tartalmának önkibontó. Az erőforrás-szolgáltató build minimális megfelelő Azure verem rendelkezik. Győződjön meg arról, a megfelelő bináris Azure verem, Ön által futtatott verziójának letöltéséhez:
    - Az Azure verem 1802 (1.0.180302.1) verziója: [SQL RP verzió 1.1.18.0](https://aka.ms/azurestacksqlrp1802).
    - Az Azure verem verzió 1712 (1.0.180102.3, 1.0.180103.2 vagy 1.0.180106.1 (integrált rendszert)): [SQL RP verzió 1.1.14.0](https://aka.ms/azurestacksqlrp1712).
- Csak integrált rendszerek telepítések esetén meg kell adnia az SQL PaaS PKI-tanúsítványt a választható PaaS tanúsítványok szakaszában leírtak szerint [Azure verem PKI követelményektől](.\azure-stack-pki-certs.md#optional-paas-certificates), úgy, hogy a .pfx fájlt a helyen által a **DependencyFilesLocalPath** paraméter.
- Győződjön meg arról, hogy a [Azure verem PowerShell legújabb verziójának](.\azure-stack-powershell-install.md) (v1.2.11) telepítve. 

## <a name="deploy-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató telepítése
Miután sikeresen előkészítette az SQL erőforrás-szolgáltató telepítése teljesíti az összes előfeltétel, most futtathatja a **DeploySqlProvider.ps1** parancsfájl központi telepítése az SQL erőforrás-szolgáltató. A DeploySqlProvider.ps1 parancsfájl ki kell olvasni az SQL erőforrás-szolgáltató bináris részeként, hogy letöltötte az Azure-verem verziója megfelelő. 

> [!IMPORTANT]
> A rendszer, ahol a parancsfájlt egy Windows 10 vagy Windows Server 2016 rendszert a legújabb verzióra a .NET-futtatókörnyezet telepítve kell lennie.


Az SQL erőforrás-szolgáltató telepítéséhez nyisson meg egy új emelt szintű (felügyeleti) PowerShell-konzolt, és nyissa meg azt a könyvtárat, ahová kicsomagolta az SQL erőforrás szolgáltató bináris fájlok.

> [!NOTE]
> Új PowerShell-konzolablakot használja a rendszer helytelen PowerShell-modul, amely már be van töltve az esetlegesen felmerülő problémák elkerülése érdekében.

Futtassa a DeploySqlProvider.ps1 parancsfájlt, amely a következő lépéseket végzi el:
- A tanúsítványok és egyéb összetevők feltölt egy tárfiókot, Azure veremben.
- Gyűjteményelem csomagok tesz közzé, úgy, hogy az SQL-adatbázisok a gyűjtemény telepítése.
- Közzétesz egy gyűjteménycsomag üzemeltetési kiszolgáló telepítéséhez.
- A virtuális gépek telepíti a Windows Server 2016 lemezkép, az 1. lépésben létrejött, és telepíti az erőforrás-szolgáltató használatával.
- Regisztrálja a helyi DNS-rekordot, amely a virtuális gép erőforrás-szolgáltató van leképezve.
- Az erőforrás-szolgáltató regisztrálása az a helyi Azure Resource Manager (felhasználó és rendszergazda).
- Opcionálisan telepítése egyetlen Windows-frissítési RP telepítése során.

Erőforrás-szolgáltató telepítési SQL kezdődik, és a system.local.sqladapter erőforrás csoportot hoz létre. Ez az erőforráscsoport a négy kötelező központi telepítések elvégzéséhez legfeljebb 75 percig is eltarthat.

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 paraméterek
Ezeket a paramétereket is megadhat a parancssorban. Ha nem, vagy bármely paraméter érvényesítése sikertelen, a szükséges paraméterek megadását kéri.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait. | _Szükséges_ |
| **AzCredential** | Az Azure-verem szolgáltatás rendszergazdai fiók hitelesítő adatait. Azure verem telepítéséhez használt használja ugyanazokat a hitelesítő adatokat. | _Szükséges_ |
| **VMLocalCredential** | Az SQL erőforrás-szolgáltató VM a helyi rendszergazdai fiók hitelesítő adatait. | _Szükséges_ |
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végpont DNS-nevét. |  _Szükséges_ |
| **DependencyFilesLocalPath** | A .pfx fájl a könyvtárban kell elhelyezni. | _Nem kötelező_ (_kötelező_ integrált rendszerekhez) |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _Szükséges_ |
| **MaxRetryCount** | Ennyiszer azt szeretné, majd ismételje meg minden egyes művelet, ha hiba történik.| 2 |
| **RetryDuration** | Az időkorlát másodpercben az újrapróbálkozások között. | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltató és minden kapcsolódó erőforrások (lásd az alábbi megjegyzések). | Nem |
| **DebugMode** | Megakadályozza az automatikus tisztítás hiba esetén. | Nem |

>[!NOTE]
> SKU órát is igénybe vehet egy megjeleníteni a portálon. Egy adatbázis nem hozható létre, amíg létrejön a Termékváltozat.


## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Az SQL erőforrás-szolgáltató használatával egyéni parancsfájl központi telepítése
Szükséges információk manuális bevitelét, ha fut a DeploySqlProvider.ps1 parancsfájl elkerüléséhez testre az alapértelmezett fiók- és igény szerint módosítsa úgy az alábbi mintaparancsfájl:

```powershell
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

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>A verem Azure portál használatával a telepítés ellenőrzése
Ebben a szakaszban a lépések segítségével győződjön meg arról, hogy az SQL erőforrás-szolgáltató sikeresen települt.

> [!NOTE]
>  A telepítési parancsfájl végeztével frissítse a portált, az admin panel megnyitásához, és a gyűjtemény elemeket kell.

1. Jelentkezzen be a felügyeleti portál a szolgáltatás-rendszergazdaként.

2. Győződjön meg arról, hogy a telepítés sikeres volt. Ugrás a **erőforráscsoportok**. Válassza ki a **system.\< hely\>.sqladapter** erőforráscsoportot. Győződjön meg arról, hogy sikerült-e négy követelményszabályait.

      ![Az SQL erőforrás-szolgáltató telepítésének ellenőrzése](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)


## <a name="next-steps"></a>További lépések

[Üzemeltetési kiszolgáló hozzáadása](azure-stack-sql-resource-provider-hosting-servers.md)

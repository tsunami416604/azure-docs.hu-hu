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
ms.date: 06/25/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: e1505761a0bd1ea9dabdd0b2cbab7af902198311
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938332"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Az SQL Server erőforrás-szolgáltató Azure veremben telepítése

Az Azure verem SQL Server erőforrás-szolgáltató használatával teszi közzé az SQL-adatbázisok Azure verem szolgáltatásként. Az erőforrás-szolgáltató SQL szolgáltatásként fut, a Windows Server 2016 Server Core virtuális gépen (VM).

## <a name="prerequisites"></a>Előfeltételek

Nincsenek több előfeltételnek kell, mielőtt telepíthetné a verem SQL Azure erőforrás-szolgáltató szükséges. Ezek a követelmények teljesítéséhez, az alábbi lépésekkel egy olyan számítógépen, férhetnek hozzá a kiemelt végpont VM:

- Ha még nem tette meg, [regisztrálni Azure verem](.\azure-stack-registration.md) az Azure-ral, letöltheti az Azure piactéren elemek.
- A verem Azure piactér úgy, hogy letölti a szükséges Windows Server core virtuális gép hozzáadása a **Windows Server 2016 Datacenter - Server Core** kép. Parancsfájl használata létrehozásához egy [Windows Server 2016 kép](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Győződjön meg arról, hogy a core beállítást választja, a parancsfájl futtatásakor.

  >[!NOTE]
  >Ha egy frissítés telepítésére van szüksége, egyetlen MSU csomag elhelyezheti a helyi függőségi elérési útja. Ha egynél több MSU fájl található, SQL erőforrás-szolgáltató telepítése sikertelen lesz.

- Töltse le az SQL erőforrás-szolgáltató bináris, és futtassa az ideiglenes könyvtár tartalmának önkibontó. Az erőforrás-szolgáltató build minimális megfelelő Azure verem rendelkezik. Győződjön meg arról, hogy a megfelelő bináris Azure verem, Ön által futtatott verziójának letöltése.

    |Verem az Azure-verzió|SQL RP verziója|
    |-----|-----|
    |Verzió 1804 (1.0.180513.1)|[SQL RP 1.1.24.0 verziója](https://aka.ms/azurestacksqlrp1804)
    |Verzió 1802 (1.0.180302.1)|[SQL RP 1.1.18.0 verziója](https://aka.ms/azurestacksqlrp1802)|
    |Verzió 1712 (1.0.180102.3, 1.0.180103.2 vagy 1.0.180106.1 (integrált rendszert))|[SQL RP 1.1.14.0 verziója](https://aka.ms/azurestacksqlrp1712)|
    |     |     |

### <a name="certificates"></a>Tanúsítványok

Csak integrált rendszerek telepítés esetén. Meg kell adnia az SQL PaaS PKI-tanúsítványt a választható PaaS tanúsítványok szakaszában leírt [Azure verem PKI követelményektől](.\azure-stack-pki-certs.md#optional-paas-certificates). A .pfx fájl a megadott helyen helyezze el a **DependencyFilesLocalPath** paraméter.

## <a name="deploy-the-sql-resource-provider"></a>Az SQL erőforrás-szolgáltató telepítése

Miután minden előfeltétel telepítve van, futtassa a **DeploySqlProvider.ps1** parancsfájl központi telepítése az SQL erőforrás-szolgáltató. A DeploySqlProvider.ps1 parancsfájl a SQL erőforrás-szolgáltató bináris Azure verem verziójának letöltött részeként ki kell olvasni.

> [!IMPORTANT]
> A rendszer, amely a parancsfájlt futtatja egy Windows 10 vagy Windows Server 2016 rendszert a legújabb verzióra a .NET-futtatókörnyezet telepítve kell lennie.

Az SQL erőforrás-szolgáltató telepítéséhez nyisson meg egy **új** emelt szintű PowerShell konzolablakában, és nyissa meg azt a könyvtárat, ahová kicsomagolta az SQL erőforrás szolgáltató bináris fájlok. Azt javasoljuk, egy új PowerShell-ablakot, amely már be van töltve PowerShell-modulok által okozott problémák elkerülése érdekében.

Futtassa a DeploySqlProvider.ps1 parancsfájlt, amely a következő feladatokat hajtja végre:

- A tanúsítványok és egyéb összetevők feltölt egy tárfiókot, Azure veremben.
- Gyűjteményelem csomagok tesz közzé, hogy SQL Database-adatbázisok a gyűjteményben.
- Közzétesz egy gyűjteménycsomag üzemeltetési kiszolgáló telepítéséhez.
- A Windows Server 2016 core lemezkép letöltése, és telepíti az erőforrás-szolgáltató SQL virtuális gép telepítését.
- Regisztrálja a helyi DNS-rekordot, amely a virtuális gép erőforrás-szolgáltató van leképezve.
- Az erőforrás-szolgáltató az a helyi Azure Resource Manager az operátor és a felhasználói fiókok regisztrálása.
- Szükség esetén telepíti egy frissítés a Windows Server erőforrás-szolgáltató telepítése során.

> [!NOTE]
> Az SQL-erőforrások szolgáltató telepítése indításakor, a **system.local.sqladapter** erőforráscsoportban jön létre. Ez az erőforráscsoport négy kötelező központi telepítést befejezéséhez legfeljebb 75 percig is eltarthat.

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 paraméterek

A parancssorból a következő paramétereket is megadhat. Ha ezt elmulasztja, vagy bármely paraméter-ellenőrzés sikertelen, a program kéri, a szükséges paraméterek megadásához.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait. | _Szükséges_ |
| **AzCredential** | Az Azure-verem szolgáltatás rendszergazdai fiók hitelesítő adatait. Az Azure-verem telepítéséhez használt hitelesítő használja. | _Szükséges_ |
| **VMLocalCredential** | Az SQL erőforrás-szolgáltató VM a helyi rendszergazdai fiók hitelesítő adatait. | _Szükséges_ |
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végpont DNS-nevét. |  _Szükséges_ |
| **DependencyFilesLocalPath** | A .pfx fájl a könyvtárban kell elhelyezni. | _Nem kötelező_ (_kötelező_ integrált rendszerekhez) |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _Szükséges_ |
| **MaxRetryCount** | Ennyiszer azt szeretné, majd ismételje meg minden egyes művelet, ha hiba történik.| 2 |
| **RetryDuration** | Az időkorlát másodpercben az újrapróbálkozások között. | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltató és minden kapcsolódó erőforrások (lásd az alábbi megjegyzések). | Nem |
| **DebugMode** | Megakadályozza az automatikus tisztítás hiba esetén. | Nem |

>[!NOTE]
> SKU órát is igénybe vehet egy megjeleníteni a portálon. Egy adatbázis nem hozható létre, amíg a Termékváltozat nem telepített és futó.

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Az SQL erőforrás-szolgáltató használatával egyéni parancsfájl központi telepítése

Elkerülése érdekében minden manuális konfigurációs, az erőforrás-szolgáltató telepítése során, testre szabhatja a következő parancsfájlt. Módosítsa az alapértelmezett fiók- és az Azure-verem üzembe helyezéshez szükséges.

```powershell
# Install the AzureRM.Bootstrapper module and set the profile.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory If folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Az erőforrás-szolgáltató telepítési parancsfájl befejezése után, frissítse a böngészőt, hogy ellenőrizze, hogy a legújabb frissítések látható.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>A verem Azure portál használatával a telepítés ellenőrzése

Az alábbi lépéseket győződjön meg arról, hogy az SQL erőforrás-szolgáltató telepítése sikeres volt.

1. Jelentkezzen be a felügyeleti portál a szolgáltatás-rendszergazdaként.
2. Válassza ki **erőforráscsoportok**.
3. Válassza ki a **system.\< hely\>.sqladapter** erőforráscsoportot.
4. Az üzenet **központi telepítések**, a következő képernyőfelvételen látható, meg kell **4 sikeres**.

      ![Az SQL erőforrás-szolgáltató telepítésének ellenőrzése](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. Az erőforrás szolgáltató üzemelő példányt részletesebb információkat kaphat **beállítások**. Válassza ki **központi telepítések** például segítségével információkat olvashat: állapot, TIMESTAMP és az egyes központi telepítések IDŐTARTAMÁT.

## <a name="next-steps"></a>További lépések

[Üzemeltetési kiszolgáló hozzáadása](azure-stack-sql-resource-provider-hosting-servers.md)

---
title: MySQL-adatbázisok használata Azure veremben |} Microsoft Docs
description: Ismerje meg, hogyan telepíthet MySQL-adatbázisok Azure verem és a Gyorsműveletek a MySQL kiszolgálói erőforrás-szolgáltató adapter telepítéséhez szolgáltatásként.
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
ms.openlocfilehash: e4c3eb1d7dfd4894576d5fbed52cf4de5fed9e44
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938113"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>A MySQL erőforrás-szolgáltató Azure veremben telepítése

A MySQL Server erőforrás-szolgáltató használatával teszi közzé a MySQL-adatbázisok Azure verem szolgáltatásként. A MySQL erőforrás-szolgáltató szolgáltatásként fut, a Windows Server 2016 Server Core virtuális gépen (VM).

## <a name="prerequisites"></a>Előfeltételek

Nincsenek több előfeltételnek kell, mielőtt telepíthetné az Azure verem MySQL erőforrás-szolgáltató szükséges. Ezek a követelmények teljesítéséhez, hajtsa végre a cikkben egy olyan számítógépen, férhetnek hozzá a kiemelt végpont virtuális gép.

* Ha még nem tette meg, [regisztrálni Azure verem](.\azure-stack-registration.md) az Azure-ral, letöltheti az Azure piactéren elemek.
* A verem Azure piactér úgy, hogy letölti a szükséges Windows Server core virtuális gép hozzáadása a **Windows Server 2016 Datacenter - Server Core** kép. Parancsfájl használata létrehozásához egy [Windows Server 2016 kép](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). Győződjön meg arról, hogy a core beállítást választja, a parancsfájl futtatásakor.

  >[!NOTE]
  >Ha egy frissítés telepítésére van szüksége, elhelyezhet egy. A helyi függőségi elérési MSU-csomagot. Ha egynél több. MSU fájl található, a MySQL erőforrás-szolgáltató telepítése sikertelen lesz.

* Töltse le a MySQL erőforrás-szolgáltató bináris, és futtassa az ideiglenes könyvtár tartalmának önkibontó.

  >[!NOTE]
  >A rendszer nem rendelkezik Internet-hozzáféréssel a MySQL-szolgáltató telepítéséhez másolja a [mysql-összekötő-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) fájlt egy helyi megosztással. Adja meg a megosztás neve, amikor a rendszer kéri az. Telepítenie kell az Azure és az Azure verem PowerShell modulok.

* Az erőforrás-szolgáltató build minimális megfelelő Azure verem rendelkezik. Győződjön meg arról, hogy a megfelelő bináris Azure verem, Ön által futtatott verziójának letöltése.

    | Verem az Azure-verzió | MySQL RP verziója|
    | --- | --- |
    | Verzió 1804 (1.0.180513.1)|[MySQL RP 1.1.24.0 verziója](https://aka.ms/azurestackmysqlrp1804) |
    | Verzió 1802 (1.0.180302.1) | [MySQL RP 1.1.18.0 verziója](https://aka.ms/azurestackmysqlrp1802) |
    | Verzió 1712 (1.0.180102.3 vagy 1.0.180106.1 (integrált rendszert)) | [MySQL RP 1.1.14.0 verziója](https://aka.ms/azurestackmysqlrp1712) |

### <a name="certificates"></a>Tanúsítványok

A telepítési folyamat során a ASDK az önaláírt tanúsítvány jön létre. Az integrált Azure verem rendszer meg kell adnia egy megfelelő tanúsítványt. Ha saját van szüksége, helyezze el egy .pfx-fájlra a **DependencyFilesLocalPath** , amely megfelel a következő:

* Vagy egy helyettesítő tanúsítvány \*.dbadapter.\< a régióban\>.\< külső fqdn\> vagy egyhelyes tanúsítvány köznapi neve a mysqladapter.dbadapter.\< a régióban\>.\< külső fqdn\>.
* A tanúsítványnak megbízhatónak kell lennie. A megbízhatósági lánc anélkül, hogy a köztes tanúsítványok léteznie kell.
* Csak egyetlen tanúsítványfájlt a DependencyFilesLocalPath szerepel.
* A fájl neve nem lehet semmilyen különleges karaktereket vagy a szóközt.

## <a name="deploy-the-resource-provider"></a>Az erőforrás-szolgáltató telepítése

Miután minden előfeltétel telepítve van, futtassa a **DeployMySqlProvider.ps1** parancsfájl központi telepítése a MYSQL erőforrás-szolgáltató. A DeployMySqlProvider.ps1 parancsfájl a MySQL erőforrás-szolgáltató bináris Azure verem verziójának letöltött részeként ki kell olvasni.

> [!IMPORTANT]
> A rendszer, amely a parancsfájlt futtatja egy Windows 10 vagy Windows Server 2016 rendszert a legújabb verzióra a .NET-futtatókörnyezet telepítve kell lennie.

A MySQL erőforrás-szolgáltató telepítéséhez nyisson meg egy új emelt szintű PowerShell-konzolablakot, és váltson arra a könyvtárra, amelybe kibontotta a MySQL erőforrás szolgáltató bináris fájlokat. Azt javasoljuk, egy új PowerShell-ablakot, amely már be van töltve PowerShell-modulok által okozott problémák elkerülése érdekében.

Futtassa a **DeployMySqlProvider.ps1** parancsfájl, amely a következő feladatokat hajtja végre:

* A tanúsítványok és egyéb összetevők feltölt egy tárfiókot, Azure veremben.
* Gyűjteményelem csomagok tesz közzé, úgy, hogy a gyűjteményelem MySQL adatbázis telepítése.
* Közzétesz egy gyűjteménycsomag üzemeltetési kiszolgáló telepítéséhez.
* Egy virtuális Gépet, a Windows Server 2016 core lemezkép letöltése, és a MySQL erőforrás-szolgáltató, majd telepíti központilag telepíti.
* Regisztrálja a helyi DNS-rekordot, amely a virtuális gép erőforrás-szolgáltató van leképezve.
* Az erőforrás-szolgáltató az a helyi Azure Resource Manager az operátor és a felhasználói fiókok regisztrálása.
* Szükség esetén telepíti egy frissítés a Windows Server erőforrás-szolgáltató telepítése során.

> [!NOTE]
> Ha a MySQL-erőforrások szolgáltató telepítése elindul, a **system.local.mysqladapter** erőforráscsoportban jön létre. Ez az erőforráscsoport szükséges négy központi telepítések befejezéséhez legfeljebb 75 percig is eltarthat.

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 paraméterek

Ezek a paraméterek a parancssorból is megadhat. Ha ezt elmulasztja, vagy bármely paraméter-ellenőrzés sikertelen, a program kéri, a szükséges paraméterek megadásához.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait. | _Szükséges_ |
| **AzCredential** | Az Azure-verem szolgáltatás rendszergazdai fiók hitelesítő adatait. Az Azure-verem telepítéséhez használt hitelesítő használja. | _Szükséges_ |
| **VMLocalCredential** | A MySQL erőforrás-szolgáltató VM a helyi rendszergazdai fiók hitelesítő adatait. | _Szükséges_ |
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végpont DNS-nevét. |  _Szükséges_ |
| **DependencyFilesLocalPath** | Egy helyi megosztás tartalmazó elérési útját [mysql-összekötő-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Az elérési utak megadásakor a tanúsítványfájlt a könyvtárban kell elhelyezni. | _Nem kötelező_ egyetlen csomópont _kötelező_ több csomópont. |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _Szükséges_ |
| **MaxRetryCount** | Ennyiszer azt szeretné, majd ismételje meg minden egyes művelet, ha hiba történik.| 2 |
| **RetryDuration** | Az időkorlát másodpercben az újrapróbálkozások között. | 120 |
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltató és minden kapcsolódó erőforrások (lásd az alábbi megjegyzések). | Nem |
| **DebugMode** | Megakadályozza az automatikus tisztítás hiba esetén. | Nem |
| **AcceptLicense** | Fogadja el a GPL licenc adatait kérő felület kihagyja.  <http://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

> [!NOTE]
> SKU órát is igénybe vehet egy megjeleníteni a portálon. Egy adatbázis nem hozható létre, amíg a Termékváltozat nem telepített és futó.

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>A MySQL erőforrás-szolgáltató használatával egyéni parancsfájl központi telepítése

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
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files.
# Find the ERCS01 IP address first, and make sure the certificate file is in the specified directory.
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Az erőforrás-szolgáltató telepítési parancsfájl befejezése után, frissítse a böngészőt, hogy ellenőrizze, hogy a legújabb frissítések látható.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>A telepítés ellenőrzése a verem Azure portál használatával

1. Jelentkezzen be a felügyeleti portál a szolgáltatás-rendszergazdaként.
2. Válassza ki **erőforráscsoportok**
3. Válassza ki a **system.\< hely\>.mysqladapter** erőforráscsoportot.
4. Az összefoglalás lapon erőforrás csoport-áttekintés, az üzenet **központi telepítések** kell **3 sikeres**.
5. Az erőforrás szolgáltató üzemelő példányt részletesebb információkat kaphat **beállítások**. Válassza ki **központi telepítések** például segítségével információkat olvashat: állapot, TIMESTAMP és az egyes központi telepítések IDŐTARTAMÁT.

## <a name="next-steps"></a>További lépések

[Üzemeltetési kiszolgáló hozzáadása](azure-stack-mysql-resource-provider-hosting-servers.md)

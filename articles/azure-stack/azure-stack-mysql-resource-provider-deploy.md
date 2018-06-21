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
ms.date: 06/20/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: 061d9431890622dafafa8c180027b8df735c100e
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295755"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>A Microsoft Azure verem használható MySQL-adatbázisok
Az Azure verem MySQL Server erőforrás-szolgáltató használatával teszi közzé a MySQL-adatbázisok Azure verem szolgáltatásként. A MySQL erőforrás-szolgáltató egy Windows Server core virtuális gép virtuális Gépet a MySQL-erőforrás-szolgáltató szolgáltatás fut.

## <a name="deploy-the-resource-provider"></a>Az erőforrás-szolgáltató telepítése 
1. Ha még nem tette meg, regisztrálja a szoftverfejlesztői készlet, és töltse le a Windows Server 2016 Datacenter Core kép letölthető piactér felügyelet használatával. A Windows Server 2016 Core lemezképet kell használnia. Parancsfájl használata létrehozásához egy [Windows Server 2016 kép](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Ügyeljen arra, hogy válassza ki a Core.) 

2. Jelentkezzen be egy olyan gazdagépre, férhetnek hozzá a kiemelt végpont VM:
    - Az Azure SDK telepítése jelentkezzen be a fizikai állomáson.  
    - Integrált rendszerek esetében a gazdagép egy rendszer, amely hozzáférhet a kiemelt végpont kell lennie. 
    
    >[!NOTE] 
    > A rendszer, amelyre a parancsfájl futtatása *kell* egy Windows 10 vagy Windows Server 2016 rendszert a legújabb verzióra a .NET-futtatókörnyezet telepítve lesz. Ellenkező esetben nem telepíthető. Az Azure verem ASDK állomás megfelel-e ezt a feltételt. 
    
3. Töltse le a MySQL erőforrás-szolgáltató bináris. Futtassa a önkibontó kicsomagolása egy ideiglenes könyvtárhoz. 
    >[!NOTE]  
    > Az erőforrás-szolgáltató build minimális megfelelő Azure verem rendelkezik. Győződjön meg arról, a megfelelő bináris futtató Azure verem verziójának letöltéséhez.

    | Verem az Azure-verzió | MySQL RP verziója| 
    | --- | --- | 
    | Verzió 1804 (1.0.180513.1)|[MySQL RP 1.1.24.0 verziója](https://aka.ms/azurestackmysqlrp1804) | 
    | Verzió 1802 (1.0.180302.1) | [MySQL RP 1.1.18.0 verziója](https://aka.ms/azurestackmysqlrp1802) | 
    | Verzió 1712 (1.0.180102.3 vagy 1.0.180106.1 (integrált rendszert)) | [MySQL RP 1.1.14.0 verziója](https://aka.ms/azurestackmysqlrp1712) | 

4.  A ASDK az önaláírt tanúsítvány jön létre a folyamat során. Integrált rendszerek esetén meg kell adnia a megfelelő tanúsítványt. Ha saját van szüksége, helyezze el egy .pfx-fájlra a **DependencyFilesLocalPath** , amely megfelel a következő: 
    - Vagy egy helyettesítő tanúsítvány \*.dbadapter.\< a régióban\>.\< külső fqdn\> vagy egyhelyes tanúsítvány köznapi neve a mysqladapter.dbadapter.\< a régióban\>.\< külső fqdn\>. 
    - Ezt a tanúsítványt megbízhatónak kell lennie. Ez azt jelenti, hogy a megbízhatósági lánc léteznie kell anélkül, hogy a köztes tanúsítványok. 
    - Csak egyetlen tanúsítványfájlt a DependencyFilesLocalPath szerepel. 
    - A fájl neve nem tartalmazhat különleges karaktereket vagy szóköz. 

5. Nyissa meg a **új** emelt szintű (felügyeleti) PowerShell-konzolban. Majd váltson arra a könyvtárra, amelybe kibontotta a fájlokat. Egy új ablak segítségével a rendszer helytelen PowerShell-modul, amely már be van töltve az esetlegesen felmerülő problémák elkerülése érdekében. 

6. Futtassa a **DeployMySqlProvider.ps1** parancsfájl. A parancsfájl ezeket a lépéseket hajtja végre: 
    - Letölti a MySQL összekötő bináris (Ezzel biztosítható, hogy kapcsolat nélküli). 
    - A tanúsítványok és egyéb összetevők feltölt egy tárfiókot, Azure veremben. 
    - Gyűjteményelem csomagok tesz közzé, úgy, hogy az SQL-adatbázisok a gyűjtemény telepítése. 
    - Közzétesz egy gyűjteménycsomag üzemeltetési kiszolgáló telepítéséhez. 
    - A Windows Server 2016 Azure verem Piactéri lemezképről virtuális gépet, és telepíti az erőforrás-szolgáltató. 
    - Regisztrálja a helyi DNS-rekordot, amely a virtuális gép erőforrás-szolgáltató van leképezve. 
    - Az erőforrás-szolgáltató regisztrálása az a helyi Azure Resource Manager (bérlői és felügyelet). 

    Tudja meg a kötelező paraméter a parancssorban vagy futtassa a parancsfájlt, paraméterek nélkül, és adja meg őket, amikor a rendszer kéri. 

    Íme egy példa a PowerShell-parancssorból futtatható. Győződjön meg arról, a fiók- és igény szerint módosíthatja: 

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
    # Find the ERCS01 IP address first, and make sure the certificate 
    # file is in the specified directory. 
    $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds ` 
        -VMLocalCredential $vmLocalAdminCreds ` 
        -CloudAdminCredential $cloudAdminCreds ` 
        -PrivilegedEndpoint $privilegedEndpoint ` 
        -DefaultSSLCertificatePassword $PfxPass ` 
        -DependencyFilesLocalPath $tempDir\cert ` 
        -AcceptLicense 
    ``` 

### <a name="deploymysqlproviderps1-parameters"></a>DeployMySqlProvider.ps1 paraméterek 
Ezeket a paramétereket is megadhat a parancssorban. Ha nem, vagy bármely paraméter érvényesítése sikertelen, a szükséges paraméterek megadását kéri. 

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték | 
| --- | --- | --- | 
| **CloudAdminCredential** | A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait. | _Szükséges_ | 
| **AzCredential** | Az Azure-verem szolgáltatás rendszergazdai fiók hitelesítő adatait. Az Azure-verem telepítéséhez használt hitelesítő használja. | _Szükséges_ | 
| **VMLocalCredential** | A MySQL erőforrás-szolgáltató VM a helyi rendszergazdai fiók hitelesítő adatait. | _Szükséges_ | 
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végpont DNS-nevét. |  _Szükséges_ | 
| **DependencyFilesLocalPath** | Egy helyi megosztás tartalmazó elérési útját [mysql-összekötő-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi). Az elérési utak megadásakor a tanúsítványfájlt a könyvtárban kell elhelyezni. | _Nem kötelező_ (_kötelező_ több csomópont) | 
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava. | _Szükséges_ | 
| **MaxRetryCount** | Ennyiszer azt szeretné, majd ismételje meg minden egyes művelet, ha hiba történik.| 2 | 
| **RetryDuration** | Az időkorlát másodpercben az újrapróbálkozások között. | 120 | 
| **Eltávolítás** | Eltávolítja az erőforrás-szolgáltató és minden kapcsolódó erőforrások (lásd az alábbi megjegyzések). | Nem | 
| **DebugMode** | Megakadályozza az automatikus tisztítás hiba esetén. | Nem | 
| **AcceptLicense** | Fogadja el a GPL licenc adatait kérő felület kihagyja.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

> [!NOTE] 
> SKU órát is igénybe vehet egy megjeleníteni a portálon. Egy adatbázis nem hozható létre, amíg létrejön a Termékváltozat. 

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>A telepítés ellenőrzése a verem Azure portál használatával 
> [!NOTE] 
>  A telepítési parancsfájl végeztével akkor kell frissítenie a portálhoz, és a felügyeleti panelt. 

1. Jelentkezzen be a felügyeleti portál a szolgáltatás-rendszergazdaként. 
2. Győződjön meg arról, hogy a telepítés sikeres volt. Ugrás a **erőforráscsoportok**, majd válassza ki a **rendszer.\< hely\>.mysqladapter** erőforráscsoportot. Győződjön meg arról, hogy sikerült-e minden négy központi telepítések:

      ![A MySQL RP a telepítés ellenőrzése](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png) 


## <a name="next-steps"></a>További lépések
[Üzemeltetési kiszolgáló hozzáadása](azure-stack-mysql-resource-provider-hosting-servers.md)

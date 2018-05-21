---
title: MySQL-adatbázisok használata Azure veremben PaaS |} Microsoft Docs
description: Ismerje meg, hogyan telepítheti a MySQL erőforrás-szolgáltató, és adja meg a MySQL-adatbázisok Azure veremben szolgáltatásként.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: mabrigg
ms.reviewer: jeffgo
ms.openlocfilehash: bc88140bf1adea49ff4bc76667d30a379f829bbc
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>A Microsoft Azure verem használható MySQL-adatbázisok

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Telepíthet egy MySQL erőforrás-szolgáltató Azure veremben. Az erőforrás-szolgáltató telepítése után MySQL-kiszolgálók és adatbázisok Azure Resource Manager központi telepítési sablonok segítségével hozhat létre. MySQL-adatbázisok szolgáltatásként is megadhatja. 

MySQL-adatbázisok, amelyek közös webhelyek, támogatja a webhely számos platformon. Például az erőforrás-szolgáltató telepítése után létrehozhat WordPress-webhelyek a Web Apps platform (PaaS) szolgáltatás-bővítmény, Azure verem.

Másolja a fájlt a rendszer nem rendelkezik Internet-hozzáférés a MySQL-szolgáltató telepítéséhez [mysql-összekötő-net-6.10.5.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.10.5.msi) helyi megosztáshoz. Majd adja meg, hogy a megosztás neve, az azt kérő. Telepítenie kell az Azure és az Azure verem PowerShell modulok.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL-kiszolgáló erőforrás szolgáltató adapter architektúrája

Az erőforrás-szolgáltató három összetevőből épül fel:

- **A MySQL erőforrás szolgáltató adapter VM**, amely a szolgáltató-szolgáltatásokat futtató Windows virtuális gépként.

- **Az erőforrás-szolgáltató maga**, amely feldolgozza a kiépítési kérelmekre, és tesz elérhetővé adatbázis-erőforrások.

- **MySQL-kiszolgálót üzemeltető kiszolgáló**, adatbázisok, nevezzük üzemeltető kiszolgálók kapacitásának nyújt.

Ebben a kiadásban már nem készít a MySQL-példányokat. Ez azt jelenti, hogy szeretné-e létre saját maga és/vagy a külső SQL Server-példányok hozzáférést biztosítanak. Látogasson el a [Azure verem gyorsindítási galéria](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) egy példa-sablon által telepíthető:
- Hozza létre a MySQL-kiszolgáló.
- Töltse le és telepít egy MySQL-kiszolgálót az Azure piactérről.

> [!NOTE]
> Üzemeltető kiszolgálók Azure verem telepített integrált rendszerek bérlői előfizetéssel kell létrehozni. Az alapértelmezett szolgáltató előfizetésből nem hozhatók létre. A bérlői portálon vagy egy PowerShell munkamenetből rendelkező megfelelő bejelentkezési objektumokat létre kell hozni. Minden üzemeltetési kiszolgáló terhelhető virtuális gép, és megfelelő licenccel kell rendelkeznie. A szolgáltatás-rendszergazdát a bérlő előfizetés tulajdonosának lehet.

### <a name="required-privileges"></a>Szükséges jogosultságok
A system fiók következő jogosultságokkal kell rendelkeznie:

1.  Adatbázis: Létrehozás, dobja el
2.  Bejelentkezési identitás: Hozzon létre, beállítása, dobja el, adja meg, visszavonása

## <a name="deploy-the-resource-provider"></a>Az erőforrás-szolgáltató telepítése

1. Ha még nem tette meg, regisztrálja a szoftverfejlesztői készlet, és töltse le a Windows Server 2016 Datacenter Core kép letölthető piactér felügyelet használatával. A Windows Server 2016 Core lemezképet kell használnia. Parancsfájl használata létrehozásához egy [Windows Server 2016 kép](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image). (Ügyeljen arra, hogy válassza ki a Core.)


2. Jelentkezzen be egy olyan gazdagépre, férhetnek hozzá a kiemelt végpont virtuális gép.

    - Az Azure SDK telepítése jelentkezzen be a fizikai állomáson. 
    - Több csomópontos rendszerek esetében a gazdagép egy rendszer, amely hozzáférhet a kiemelt végpont kell lennie.
    
    >[!NOTE]
    > A rendszer, amelyre a parancsfájl futtatása *kell* egy Windows 10 vagy Windows Server 2016 rendszert a legújabb verzióra a .NET-futtatókörnyezet telepítve lesz. Ellenkező esetben nem telepíthető. Az Azure verem SDK állomás megfelel-e ezt a feltételt.
    

3. Töltse le a MySQL erőforrás-szolgáltató bináris. Futtassa a önkibontó kicsomagolása egy ideiglenes könyvtárhoz.

    >[!NOTE] 
    > Az erőforrás-szolgáltató build minimális megfelelő Azure verem rendelkezik. Győződjön meg arról, a megfelelő bináris futtató Azure verem verziójának letöltéséhez.

    | Az Azure verem build | MySQL RP-telepítő |
    | --- | --- |
    | 1802: 1.0.180302.1 | [MySQL RP 1.1.18.0 verziója](https://aka.ms/azurestackmysqlrp1802) |
    | 1712: 1.0.180102.3 vagy 1.0.180106.1 (több csomópontos) | [MySQL RP 1.1.14.0 verziója](https://aka.ms/azurestackmysqlrp1712) |
    | 1711: 1.0.171122.1 | [MySQL RP 1.1.12.0 verziója](https://aka.ms/azurestackmysqlrp1711) |
    | 1710: 1.0.171028.1 | [MySQL RP 1.1.8.0 verziója](https://aka.ms/azurestackmysqlrp1710) |

4.  Az Azure SDK-ban önaláírt tanúsítvány jön létre a folyamat során. Több csomópontos meg kell adnia egy megfelelő tanúsítványt.

    Ha saját van szüksége, helyezze el egy .pfx-fájlra a **DependencyFilesLocalPath** , amely megfelel a következő:

    - Vagy egy helyettesítő tanúsítvány \*.dbadapter.\< a régióban\>.\< külső fqdn\> vagy egyhelyes tanúsítvány köznapi neve a mysqladapter.dbadapter.\< a régióban\>.\< külső fqdn\>.

    - Ezt a tanúsítványt megbízhatónak kell lennie. Ez azt jelenti, hogy a megbízhatósági lánc léteznie kell anélkül, hogy a köztes tanúsítványok.

    - Csak egyetlen tanúsítványfájlt a DependencyFilesLocalPath szerepel.
    
    - A fájl neve nem tartalmazhat különleges karaktereket vagy szóköz.


5. Nyissa meg a **új** emelt szintű (felügyeleti) PowerShell-konzolban. Majd váltson arra a könyvtárra, amelybe kibontotta a fájlokat. Egy új ablak segítségével a rendszer helytelen PowerShell-modul, amely már be van töltve az esetlegesen felmerülő problémák elkerülése érdekében.

6. [Telepítse az Azure PowerShell verziója 1.2.11](azure-stack-powershell-install.md).

7. Futtassa az `DeployMySqlProvider.ps1` szkriptet.

A parancsfájl ezeket a lépéseket hajtja végre:

* Letölti a MySQL összekötő bináris (Ezzel biztosítható, hogy kapcsolat nélküli).
* A tanúsítványok és egyéb összetevők feltölt egy tárfiókot, Azure veremben.
* Gyűjteményelem csomagok tesz közzé, úgy, hogy az SQL-adatbázisok a gyűjtemény telepítése.
* Közzétesz egy gyűjteménycsomag üzemeltetési kiszolgáló telepítéséhez.
* A virtuális gépek telepíti a Windows Server 2016-lemezképről, az 1. lépésben létrehozott. Az erőforrás-szolgáltató is telepíti.
* Regisztrálja a helyi DNS-rekordot, amely a virtuális gép erőforrás-szolgáltató van leképezve.
* Az erőforrás-szolgáltató regisztrálása az a helyi Azure Resource Manager (bérlői és felügyelet).


A következőket teheti:
- Adja meg a kötelező paraméter a parancssorban.
- Paraméterek nélkül futtatni, és adja őket, amikor a rendszer kéri.

Íme egy példa a PowerShell-parancssorból futtatható. Győződjön meg arról, a fiók- és igény szerint módosíthatja:


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
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
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

>[!NOTE]
> SKU órát is igénybe vehet egy megjeleníteni a portálon. Egy adatbázis nem hozható létre, amíg létrejön a Termékváltozat.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>A telepítés ellenőrzése a verem Azure portál használatával

> [!NOTE]
>  A telepítési parancsfájl végeztével akkor kell frissítenie a portálhoz, és a felügyeleti panelt.


1. Jelentkezzen be a felügyeleti portál a szolgáltatás-rendszergazdaként.

2. Győződjön meg arról, hogy a telepítés sikeres volt. Ugrás a **erőforráscsoportok**, majd válassza ki a **rendszer.\< hely\>.mysqladapter** erőforráscsoportot. Győződjön meg arról, hogy sikerült-e négy követelményszabályait.

      ![A MySQL RP a telepítés ellenőrzése](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Adja meg a kapacitás MySQL üzemeltetési kiszolgálójához

1. A szolgáltatás-rendszergazdaként jelentkezzen be a verem Azure portálra

2. Válassza ki **felügyeleti erőforrások** > **üzemeltető kiszolgálók MySQL** > **+ Hozzáadás**.

    Az a **MySQL üzemeltető kiszolgálók** panelen csatlakozhat a MySQL Server erőforrás-szolgáltató MySQL kiszolgáló tényleges példányai az erőforrás-szolgáltató háttér szolgál.

    ![Üzemeltetési kiszolgáló](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Adja meg a MySQL-kiszolgálópéldány kapcsolat adatait. Ne adja meg a teljesen minősített tartománynevét (FQDN) vagy egy érvényes IPv4-címet, és nem a rövid virtuális gép nevét. A telepítés nem egy alapértelmezett MySQL példányt biztosít. A megadott méret segít az erőforrás-szolgáltató az adatbázis-kapacitás kezelése. A fizikai kapacitás az adatbázis-kiszolgáló közel kell lennie.

    > [!NOTE]
    >A MySQL-példány hozzáférhet a bérlői és az Azure Resource Manager rendszergazda, ha az erőforrás-szolgáltató ellenőrzése alatt lehet tenni. A MySQL-példány *kell* kizárólag az erőforrás-szolgáltató kiosztani.

4. Kiszolgálók hozzáadása során, akkor hozzá kell rendelnie azokat egy új vagy meglévő SKU szolgáltatásajánlatok megkülönböztetési engedélyezéséhez.
  Például lehet egy vállalati példány megadása:
    - adatbázis-kapacitás
    - Automatikus biztonsági mentés
    - az egyes részlegek nagy teljesítményű kiszolgálók


  > [!IMPORTANT]
  > Önálló kiszolgálók nem keverhetők az Always On osztályt a azonos Termékváltozat. Kísérlet történt kombinálhatók típusok hozzáadása az első üzemeltetési kiszolgáló eredmények a hiba után.
 

A termékváltozat tükröznie kell tulajdonságait, hogy a bérlők megfelelően elhelyezheti az adatbázisokat. A termékváltozatban minden üzemeltetési kiszolgáló ugyanazokat a képességeket kell rendelkeznie.

![Hozzon létre egy MySQL Termékváltozat](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)





## <a name="test-your-deployment-by-creating-your-first-mysql-database"></a>Az első MySQL-adatbázis létrehozása a központi telepítés tesztelése


1. A szolgáltatás-rendszergazdaként jelentkezzen be a verem Azure portálra

2. Válassza ki **+ új** > **adatok + tárolás** > **MySQL-adatbázis**.

3. Adja meg az adatbázis adatait.

    ![Egy teszt MySQL-adatbázis létrehozása](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Válassza ki a Termékváltozat.

    ![Válassza ki a Termékváltozat](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Hozzon létre bejelentkezési beállítást. Használja fel meglévő bejelentkezési beállításokat, vagy hozzon létre egy újat. Ez a beállítás a felhasználói nevet és jelszót az adatbázis tartalmaz.

    ![Hozzon létre egy új adatbázis-bejelentkezési adatokat](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    A kapcsolati karakterlánc a valódi adatbázis-kiszolgáló nevét tartalmazza. Másolja a portálról.

    ![A MySQL-adatbázis a kapcsolati karakterlánc beolvasása](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> A felhasználónevek a hossza nem haladhatja meg a MySQL 5.7 32 karakter hosszú lehet. A korábbi kiadásokban nem haladhatja meg 16 karakter hosszúságú lehet.


## <a name="add-capacity"></a>Adja hozzá a kapacitás

Adja hozzá a kapacitása további MySQL-kiszolgálók hozzáadásával a verem Azure-portálon. További kiszolgálókra lehet hozzáadni egy új vagy meglévő Termékváltozat. Ellenőrizze, hogy a kiszolgáló, azonosítandó paraméterek azonosak.


## <a name="make-mysql-databases-available-to-tenants"></a>MySQL-adatbázisok elérhetővé tétele a bérlők
Csomagok és a MySQL-adatbázisok a bérlők számára elérhetővé ajánlatok létrehozása. Például vegye fel a Microsoft.MySqlAdapter szolgáltatást, vegye fel a kvótát, és így tovább.

![Tervek és adatbázisokat tartalmazza ajánlatok létrehozása](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Frissítés a rendszergazdai jelszó
A jelszó módosításához első módosítás MySQL server-példányon. Válassza ki **felügyeleti erőforrások** > **üzemeltető kiszolgálók MySQL**. Ezután válassza ki az üzemeltetési kiszolgálóhoz. Az a **beállítások** panelen, jelölje be **jelszó**.

![Frissítés a rendszergazdai jelszó](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>A MySQL erőforrás-szolgáltató adapter (több csomópontos csak, buildek 1710 és újabb verziók) frissítése
Új SQL-erőforrás szolgáltató adapter előfordulhat, hogy mikorra várható, Azure verem buildek frissítésekor. A meglévő adapter továbbra is működik, de javasolt frissítésére a legújabb buildjével a lehető leghamarabb. 

Az erőforrás-szolgáltató használata frissíteni a *UpdateMySQLProvider.ps1* parancsfájl. A folyamat hasonlít a folyamat egy erőforrás-szolgáltató telepítéséhez használt leírtak szerint a [telepíteni az erőforrás-szolgáltató](#deploy-the-resource-provider) című szakaszát. A parancsfájl az erőforrás-szolgáltató a letöltés részét képezi.

A *UpdateMySQLProvider.ps1* parancsfájlt hoz létre egy új virtuális Gépet a legújabb erőforrás-szolgáltató kódot, és a beállítások áttelepítése a régi virtuális gépről az új virtuális Gépet. Telepítse át a beállításokat adatbázis és a helyet adó kiszolgáló adatait, és a szükséges DNS-rekordja.

A parancsfájl a DeployMySqlProvider.ps1 parancsfájl ugyanazokkal az argumentumokkal ismertetett használatát igényli. Adja meg itt tanúsítványt is. 

Az alábbiakban látható egy példa a *UpdateMySQLProvider.ps1* parancsfájlt, amely a PowerShell-parancssorból futtatható. Győződjön meg arról, a fiók- és igény szerint módosíthatja: 

> [!NOTE]
> A frissítési folyamat csak az integrált rendszerekre vonatkozik.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

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

# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 paraméterek
Ezeket a paramétereket is megadhat a parancssorban. Ha ezt elmulasztja, vagy bármely paraméter-ellenőrzés sikertelen, a szükséges paraméterek megadását kéri.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait. | _Szükséges_ |
| **AzCredential** | Az Azure-verem szolgáltatás rendszergazdai fiók hitelesítő adatait. Azure verem telepítéséhez használt használja ugyanazokat a hitelesítő adatokat. | _Szükséges_ |
| **VMLocalCredential** |Az SQL erőforrás-szolgáltató VM a helyi rendszergazdai fiók hitelesítő adatait. | _Szükséges_ |
| **PrivilegedEndpoint** | Az IP-cím vagy a kiemelt végpont DNS-nevét. |  _Szükséges_ |
| **DependencyFilesLocalPath** | A .pfx fájl a könyvtárban kell elhelyezni. | _Nem kötelező_ (_kötelező_ több csomópont) |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava | _Szükséges_ |
| **MaxRetryCount** | Ennyiszer azt szeretné, majd ismételje meg minden egyes művelet, ha hiba történik.| 2 |
| **RetryDuration** | Az időkorlát másodpercben az újrapróbálkozások között. | 120 |
| **Eltávolítás** | Távolítsa el az erőforrás-szolgáltatót és minden kapcsolódó erőforrások (lásd az alábbi megjegyzések). | Nem |
| **DebugMode** | Megakadályozza az automatikus tisztítás hiba esetén. | Nem |
| **AcceptLicense** | Fogadja el a GPL licenc adatait kérő felület kihagyja.  (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | |


## <a name="collect-diagnostic-logs"></a>Diagnosztikai naplók gyűjtésére
A MySQL erőforrás-szolgáltató nem egy zárolt a virtuális gép működik. Ha szükségessé válik a virtuális gép, egy PowerShell csak elég adminisztrációs (JEA) végpont gyűjteni _DBAdapterDiagnostics_ valósul meg erre a célra. Nincsenek elérhető ezen a végponton keresztül két parancsot:

* Get-AzsDBAdapterLog - előkészíti a függő Entitás diagnosztikai naplók tartalmazó zip-csomagját, és hozzáadja a munkamenet felhasználói meghajtón. A parancs paraméter nélküli hívható, és összegyűjti a naplók utolsó négy óra.
* Remove-AzsDBAdapterLog - megtisztítja a meglévő napló-csomagokat a VM erőforrás-szolgáltató

Egy felhasználói fiókot _dbadapterdiag_ RP központi telepítés vagy frissítés a diagnosztika végpont RP naplók kibontott való csatlakozás során jön létre. A fiókhoz tartozó jelszó megegyezik a telepítés/frissítés közben a helyi rendszergazdai fiókhoz megadott jelszóval.

Ezek a parancsok használatához szüksége az erőforrás-szolgáltató virtuális géphez a távoli PowerShell-munkamenetet létrehozni, és meghívja a parancsot. FromDate és ToDate paraméterekkel is megadhat. Egyik vagy mindkét esetben nem adja meg, ha a FromDate lesz az aktuális időpont előtt négy óra, és a ToDate lesz az aktuális idővel.

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
A MySQL erőforrás-szolgáltató nem egy zárolt a virtuális gép működik. Az erőforrás szolgáltató virtuális gép biztonsági frissítése végezhető el a PowerShell csak elég adminisztrációs (JEA) végpont _DBAdapterMaintenance_.

A parancsfájl az e műveletek megkönnyítése érdekében a RP-telepítési csomaggal valósul meg.


### <a name="update-the-virtual-machine-operating-system"></a>A virtuális gép operációs rendszerének frissítése
Többféleképpen is lehet frissíteni a Windows Server virtuális Gépet:
* Telepítse a legújabb szolgáltató erőforráscsomag jelenleg javított Windows Server 2016 Core lemezkép használatával
* A telepítés vagy a függő Entitás frissítése során a Windows Update csomag telepítése


### <a name="update-the-virtual-machine-windows-defender-definitions"></a>A virtuális gép Windows Defender-definíciók frissítése

Kövesse az alábbi lépéseket a Defender definícióinak frissítéséhez:

1. A Windows Defender-definíciók frissítéséhez letöltési [Windows Defender definíciós](https://www.microsoft.com/en-us/wdsi/definitions)

    Ez az oldal "Manuálisan töltse le és telepítse a definíciók" le "Windows Defender víruskereső Windows 10 és Windows 8.1" 64 bites fájlrendszer. 
    
    Közvetlen kapcsolat: https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64

2. Hozzon létre egy PowerShell-munkamenetet a MySQL RP adapter virtuális gép karbantartási végponthoz
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


## <a name="remove-the-mysql-resource-provider-adapter"></a>A MySQL erőforrás-szolgáltató adapter eltávolítása

Távolítsa el az erőforrás-szolgáltató, fontos először távolítsa el a függőségeket.

1. Győződjön meg arról, hogy rendelkezik-e az eredeti központi telepítési csomag, az erőforrás-szolgáltató ezen verzióját letöltötte-e.

2. Az összes bérlői adatbázisok törölni kell az erőforrás-szolgáltató. (A bérlő adatbázisok törlése nem törli az adatokat.) Ez a feladat a bérlők maguknak kell elvégezni.

3. Bérlők kell megszüntetni a névteret.

4. A rendszergazda az üzemeltetési kiszolgáló törölni kell a MySQL-adaptert.

5. A rendszergazda kell törölni a MySQL Adapter hivatkozó bármely tervek.

6. A rendszergazda törölnie kell a kvóták, amelyek a MySQL-Adapter társítva.

7. Futtassa újra a telepítési parancsprogram a következő elemeket:
    - A - paraméter eltávolítása
    - Az Azure Resource Manager-végpontok
    - A DirectoryTenantID
    - A szolgáltatás-rendszergazdai fiók hitelesítő adatait

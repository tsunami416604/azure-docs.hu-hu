---
title: "MySQL-adatbázisok használata Azure veremben PaaS |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítheti a MySQL erőforrás-szolgáltató, és adja meg a MySQL-adatbázisok Azure veremben szolgáltatásként"
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
ms.date: 12/15/2017
ms.author: JeffGo
ms.openlocfilehash: 71abceb1afe315a09ea88b593f9806e9e8b31f16
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>A Microsoft Azure verem használható MySQL-adatbázisok

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Telepíthet egy MySQL erőforrás-szolgáltató Azure veremben. Miután telepítette az erőforrás-szolgáltató, MySQL-kiszolgálók és adatbázisok Azure Resource Manager központi telepítési sablonok létrehozása, és adja meg a MySQL-adatbázisok szolgáltatásként. MySQL-adatbázisok, amelyek közös webhelyek, támogatja a webhely számos platformon. Tegyük fel az erőforrás-szolgáltató telepítése után létrehozhat WordPress-webhelyek a webalkalmazások Azure platformon, a szolgáltatás (PaaS) bővítmény Azure verem.

A rendszer nem rendelkezik internet-hozzáférés a MySQL-szolgáltató telepítéséhez másolhatja a fájlt [mysql-összekötő-net-6.9.9.msi](https://dev.mysql.com/get/Download/sConnector-Net/mysql-connector-net-6.9.9.msi) helyi megosztáshoz. Ezt követően adja meg, hogy megosztásnevet, amikor a rendszer kéri. Az Azure és az Azure verem PowerShell modulok is telepíteni kell.


## <a name="mysql-server-resource-provider-adapter-architecture"></a>MySQL kiszolgálóadapter erőforrás szolgáltató architektúrája

Az erőforrás-szolgáltató három összetevőből épül fel:

- **A MySQL erőforrás szolgáltató adapter VM**, mely a szolgáltató services szolgáltatást futtató Windows virtuális gép.
- **Az erőforrás-szolgáltató maga**, amely feldolgozza a kiépítési kérelmekre, és tesz elérhetővé adatbázis-erőforrások.
- **MySQL-kiszolgálót üzemeltető kiszolgáló**, adatbázisok, kapacitás nyújt nevű futtató kiszolgálók.

Ebben a kiadásban már nem készít egy MySQL-példányt. Meg kell hozza létre a címzetteket és/vagy a külső SQL Server-példányok hozzáférést biztosítanak. Látogasson el a [Azure verem gyorsindítási galéria](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) egy példa-sablon által telepíthető:
- hozza létre a MySQL-kiszolgáló
- Töltse le és telepít egy MySQL-kiszolgálót a piactéren.

> [!NOTE]
> Egy több csomópontos Azure verem telepített kiszolgálók üzemeltető bérlői előfizetéssel kell létrehozni. Az alapértelmezett szolgáltató előfizetésből nem hozhatók létre. Ez azt jelenti akkor létre kell hozni a bérlői portál vagy egy megfelelő bejelentkezési azonosító egy PowerShell-munkamenetet. Minden üzemeltetési kiszolgáló terhelhető virtuális gép, és megfelelő licenccel kell rendelkeznie. A szolgáltatás-rendszergazdát, hogy az előfizetés tulajdonosa lehet.

### <a name="required-privileges"></a>Szükséges jogosultságok
A system fiók következő jogosultságokkal kell rendelkeznie:

1.  Adatbázis: Létrehozás, dobja el
2.  Bejelentkezési identitás: Hozzon létre, beállítása, dobja el, adja meg, visszavonása

## <a name="deploy-the-resource-provider"></a>Az erőforrás-szolgáltató telepítése

1. Ha még nem tette meg, regisztrálja a szoftverfejlesztői készlet, és töltse le a Windows Server 2016 Datacenter Core kép letölthető piactér felügyelet használatával. A Windows Server 2016 Core lemezképet kell használnia. Parancsfájl használata létrehozásához egy [Windows Server 2016 kép](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) -ügyeljen arra, hogy válassza ki a Core. A .NET 3.5 futásidejű már nincs szükség.


2. Jelentkezzen be egy olyan gazdagépre, férhetnek hozzá a kiemelt végpont virtuális Gépet.

    a. A Azure verem Development Kit (ASDK) telepítésekre jelentkezzen be a fizikai állomáson.

    b. Több csomópontos rendszerek esetében a gazdagép egy rendszer, amely hozzáférhet a kiemelt végpont kell lennie.
    
    >[!NOTE]
    > A rendszer, ha a parancsfájl futtatása *kell* egy Windows 10 vagy Windows Server 2016 rendszert a legújabb verzióra a .NET-futtatókörnyezet telepítve lesz. Ellenkező esetben telepítése sikertelen. A ASDK gazdagép megfelel a feltételeknek.
    

3. Töltse le a MySQL erőforrás-szolgáltató bináris, és egy ideiglenes könyvtárhoz tartalmának önkibontó hajtható végre.

    >[!NOTE] 
    > Az erőforrás-szolgáltató build Azure verem buildek felel meg. Le kell töltenie a megfelelő bináris futtató Azure verem verziójának.

    | Az Azure verem Build | MySQL RP-telepítő |
    | --- | --- |
    | 1.0.171122.1 | [MySQL RP 1.1.12.0 verziója](https://aka.ms/azurestackmysqlrp) |
    | 1.0.171028.1 | [MySQL RP 1.1.8.0 verziója](https://aka.ms/azurestackmysqlrp1710) |
    | 1.0.170928.3 | [MySQL RP 1.1.3.0 verziója](https://aka.ms/azurestackmysqlrp1709) |

4.  Az Azure-verem legfelső szintű tanúsítvány veszi át a kiemelt végpont. A ASDK önaláírt tanúsítvány jön létre a folyamat során. Több csomópontos meg kell adnia egy megfelelő tanúsítványt.

    Ha saját van szüksége, szüksége lesz a PFX-fájlba helyezi a **DependencyFilesLocalPath** (lásd alább) az alábbiak szerint:

    - Vagy egy helyettesítő tanúsítvány \*.dbadapter.\< a régióban\>.\< külső fqdn\> vagy egyhelyes tanúsítvány köznapi neve a mysqladapter.dbadapter.\< a régióban\>.\< külső fqdn\>
    - Ezt a tanúsítványt megbízhatónak kell lennie, például a állít ki egy hitelesítésszolgáltatónak. Ez azt jelenti, hogy a megbízhatósági lánc léteznie kell anélkül, hogy a köztes tanúsítványok.
    - Csak egyetlen tanúsítványfájlt a DependencyFilesLocalPath szerepel.
    - A fájl neve nem tartalmazhat speciális karaktereket.



5. Nyissa meg a **új** emelt szintű (felügyeleti) PowerShell-konzolt, és módosítsa a könyvtárra, amelybe kibontotta a fájlokat. Egy új ablak segítségével a rendszer helytelen PowerShell-modul már be van töltve az esetleg felmerülő problémák elkerülése érdekében.

6. [Telepítse az Azure PowerShell verziója 1.2.11](azure-stack-powershell-install.md).

7. Futtassa a DeploySqlProvider.ps1 parancsfájlt.

A parancsfájl ezeket a lépéseket hajtja végre:

* Töltse le a MySQL összekötő bináris (Ezzel biztosítható, hogy kapcsolat nélküli).
* A tanúsítványok és egyéb összetevők feltölteni a az Azure storage-fiók.
* Tegye közzé a gyűjtemény csomagok, hogy az SQL-adatbázisok a gyűjtemény telepítése.
* Az üzemeltető kiszolgálók telepítése egy gyűjteménycsomag közzététele
* A Windows Server 2016-lemezkép az 1. lépésben létrehozott virtuális gép telepítése, és az erőforrás-szolgáltató telepítése.
* Regisztrálja a helyi DNS-rekordot, amely a virtuális gép erőforrás-szolgáltató van leképezve.
* Az erőforrás-szolgáltató regisztrálása az a helyi Azure Resource Manager (bérlői és felügyelet).


A következőket teheti:
- Adjon meg legalább az kötelező paraméter a parancssorban
- vagy ha paraméter nélkül futtatja, adja meg őket, amikor a rendszer kéri.

Például futtathatja a PowerShell kérni (de a fiók- és szükség szerint módosítsa):


```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privleged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Run the installation script from the folder where you extracted the installation files
# Find the ERCS01 IP address first and make sure the certificate
# file is in the specified directory
. $tempDir\DeployMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense

 ```


### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 paraméterek
Ezeket a paramétereket is megadhat a parancssorban. Ha nem, vagy bármely paraméter-ellenőrzés sikertelen, a rendszer kéri a adja meg a szükséges néhányat a meglévők közül.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájával, a Privleged végpont eléréséhez szükséges hitelesítő adatait. | _szükséges_ |
| **AzCredential** | Adja meg a Azure verem szolgáltatás-rendszergazdai fiók hitelesítő adatait. Használja ugyanazokat a hitelesítő adatokat telepítése Azure verem használható). | _szükséges_ |
| **VMLocalCredential** | Adja meg a MySQL erőforrás-szolgáltató VM a helyi rendszergazdai fiók hitelesítő adatait. | _szükséges_ |
| **PrivilegedEndpoint** | Adja meg az IP-cím vagy a DNS-neve, a kiemelt végpont. |  _szükséges_ |
| **DependencyFilesLocalPath** | Egy helyi megosztással tartalmazó elérési [mysql-összekötő-net-6.9.9.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.9.9.msi). Ha megad egy, a tanúsítványfájlt a könyvtárban kell elhelyezni. | _nem kötelező_ (_kötelező_ több csomópont) |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava | _szükséges_ |
| **MaxRetryCount** | Adja meg, majd ismételje meg minden egyes művelet, ha azt szeretné, hogy hány alkalommal hibát.| 2 |
| **RetryDuration** | Adja meg az időtúllépés másodpercben az újrapróbálkozások között. | 120 |
| **Eltávolítás** | Távolítsa el az erőforrás-szolgáltató és minden kapcsolódó erőforrások (lásd az alábbi megjegyzéseket:) | Nem |
| **DebugMode** | Megakadályozza az automatikus tisztítás hiba esetén | Nem |
| **AcceptLicense** | Fogadja el a GPL licenc (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) adatait kérő felület kihagyja | |


Attól függően, hogy a rendszer teljesítményét és a letöltési sebessége, telepítési akár 20 percig vagy hosszú szerint több óráig is eltarthat. Ha nem érhető el a MySQLAdapter panelt, frissítse a felügyeleti portálon.

> [!NOTE]
> Ha a telepítés több mint 90 percig tart, előfordulhat, hogy, és a hibaüzenet akkor jelenik meg, a képernyőn, majd a naplófájlban. A rendszer a központi telepítés a hibás lépés ismét megkísérli. Rendszerek, amelyek nem felelnek meg a memória és az alapvető ajánlott paramétereknek nem lehet a MySQL RP telepíthet.



## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>A verem Azure portál használatával a telepítés ellenőrzése

> [!NOTE]
>  Miután befejezte a telepítési parancsfájlt, szüksége lesz a portálhoz, és a felügyeleti panel frissítéséhez.


1. Jelentkezzen be a felügyeleti portál a szolgáltatás-rendszergazdaként.

2. Győződjön meg arról, hogy a telepítés sikeres volt. Tallózással keresse meg **erőforráscsoportok** &gt;, kattintson a **system.\< hely\>.mysqladapter** erőforrás csoportból, és győződjön meg arról, hogy sikerült-e négy követelményszabályait.

      ![A MySQL RP a telepítés ellenőrzése](./media/azure-stack-mysql-rp-deploy/mysqlrp-verify.png)

## <a name="provide-capacity-by-connecting-to-a-mysql-hosting-server"></a>Adja meg a kapacitás MySQL üzemeltetési kiszolgálójához

1. A szolgáltatás-rendszergazdaként jelentkezzen be a verem Azure portálra

2. Keresse meg a **felügyeleti erőforrások** &gt; **üzemeltető kiszolgálók MySQL** &gt; **+ Hozzáadás**.

    A **MySQL üzemeltető kiszolgálók** panel, ahol csatlakozhat a MySQL Server erőforrás-szolgáltató MySQL kiszolgáló tényleges példányai az erőforrás-szolgáltató háttér szolgál.

    ![Üzemeltetési kiszolgáló](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

3. Az űrlap kitöltése a MySQL Server-példány a kapcsolat adatai. Adja meg a teljesen minősített tartománynevét (FQDN) vagy egy érvényes IPv4-címet, és nem a rövid virtuális gép nevét. A telepítés nem egy alapértelmezett MySQL példányt biztosít. A megadott méret segít az erőforrás-szolgáltató az adatbázis-kapacitás kezelése. A fizikai kapacitás az adatbázis-kiszolgáló közel kell lennie.

    > [!NOTE]
    > Mindaddig, amíg a MySQL-példány hozzáférhet a bérlői és az Azure Resource Manager rendszergazda, az erőforrás-szolgáltató ellenőrzése alatt lehet tenni. A MySQL-példány __kell__ lehet kizárólag a függő Entitás számára.

4. Kiszolgálók hozzáadása során, akkor hozzá kell rendelnie azokat egy új vagy meglévő SKU szolgáltatásajánlatok megkülönböztetési engedélyezéséhez.
  Például lehet egy vállalati példány megadása:
    - adatbázis-kapacitás
    - Automatikus biztonsági mentés
    - az egyes részlegek nagy teljesítményű kiszolgálók
 

A termékváltozat tükröznie kell tulajdonságait, hogy a bérlők megfelelően elhelyezheti az adatbázisokat. A termékváltozatban minden üzemeltetési kiszolgáló ugyanazokat a képességeket kell rendelkeznie.

![Hozzon létre egy MySQL Termékváltozat](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)


>[!NOTE]
> SKU órát is igénybe vehet egy megjeleníteni a portálon. Egy adatbázis nem hozható létre, amíg létrejön a Termékváltozat.


## <a name="to-test-your-deployment-create-your-first-mysql-database"></a>A telepítés tesztelésére, az első MySQL-adatbázis létrehozása


1. Szolgáltatás-rendszergazdaként jelentkezzen be a verem Azure portálra

2. Kattintson a **+ új** gomb &gt; **adatok + tárolás** &gt; **MySQL-adatbázis**.

3. Töltse ki az űrlapot az adatbázis adatokkal.

    ![Egy teszt MySQL-adatbázis létrehozása](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. Válassza ki a Termékváltozat.

    ![Válassza ki a Termékváltozat](./media/azure-stack-mysql-rp-deploy/mysql-select-a-sku.png)

5. Hozzon létre bejelentkezési beállítást. A bejelentkezési beállítás használható fel újra, vagy újat létrehozni. Ez a beállítás a felhasználói nevet és jelszót az adatbázis tartalmaz.

    ![Hozzon létre egy új adatbázis-bejelentkezési adatokat](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    A kapcsolati karakterlánc a valódi adatbázis-kiszolgáló nevét tartalmazza. Másolja a portálról.

    ![A MySQL-adatbázis a kapcsolati karakterlánc beolvasása](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

> [!NOTE]
> A felhasználónevek nem hosszabb 32 karakter hosszú lehet a MySQL 5.7 vagy korábbi kiadások 16 karakter.


## <a name="add-capacity"></a>Adja hozzá a kapacitás

Adja hozzá a kapacitása további MySQL-kiszolgálók hozzáadásával a verem Azure-portálon. További kiszolgálókra lehet hozzáadni egy új vagy meglévő Termékváltozat. Ellenőrizze, hogy a kiszolgáló, azonosítandó paraméterek azonosak.


## <a name="make-mysql-databases-available-to-tenants"></a>MySQL-adatbázisok elérhetővé tétele a bérlők
Csomagok és a MySQL-adatbázisok a bérlők számára elérhetővé ajánlatok létrehozása. Vegye fel a Microsoft.MySqlAdapter szolgáltatást, vegye fel a kvótát, stb.

![Tervek és adatbázisokat tartalmazza ajánlatok létrehozása](./media/azure-stack-mysql-rp-deploy/mysql-new-plan.png)

## <a name="update-the-administrative-password"></a>Frissítés a rendszergazdai jelszó
A jelszó módosításához első módosítás MySQL server-példányon. Keresse meg a **felügyeleti erőforrások** &gt; **MySQL üzemeltető kiszolgálók** &gt; , majd kattintson a az üzemeltető kiszolgálót. A beállítások panelen kattintson a jelszót.

![Frissítés a rendszergazdai jelszó](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="update-the-mysql-resource-provider-adapter-multi-node-only-builds-1710-and-later"></a>A MySQL erőforrás-szolgáltató Adapter (több csomópontos csak, buildek 1710 és újabb verziók) frissítése
Az Azure-verem build frissül, amikor új MySQL erőforrás-szolgáltató Adapter kiadjuk az. A meglévő adapter esetleg tovább használhatók, amíg tanácsos frissítse a legújabb buildjével amint lehetséges az Azure-verem frissítése után. A frissítési folyamat nagyon hasonlít a fent ismertetett telepítési folyamat. Egy új virtuális Gépet a legújabb RP kód jön létre, és ezen új példányának, beleértve az adatbázis és a helyet adó kiszolgáló adatait, valamint a szükséges DNS-rekord beállításai áttelepíthetők.

A UpdateMySQLProvider.ps1 parancsfájl használata a fenti ugyanazokkal az argumentumokkal. A tanúsítvány itt is meg kell adnia.

> [!NOTE]
> Frissítés csak többcsomópontos rendszereken támogatott.

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack and the default prefix is AzS
# For integrated systems, the domain and the prefix will be the same.
$domain = "AzureStack"
$prefix = "AzS"
$privilegedEndpoint = "$prefix-ERCS01"

# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
. $tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert `
  -AcceptLicense
 ```

### <a name="updatemysqlproviderps1-parameters"></a>UpdateMySQLProvider.ps1 paraméterek
Ezeket a paramétereket is megadhat a parancssorban. Ha nem, vagy bármely paraméter-ellenőrzés sikertelen, a rendszer kéri a adja meg a szükséges néhányat a meglévők közül.

| Paraméter neve | Leírás | Megjegyzés vagy az alapértelmezett érték |
| --- | --- | --- |
| **CloudAdminCredential** | A felhő rendszergazdájával, a kiemelt végpont eléréséhez szükséges hitelesítő adatait. | _szükséges_ |
| **AzCredential** | Adja meg a Azure verem szolgáltatás-rendszergazdai fiók hitelesítő adatait. Használja ugyanazokat a hitelesítő adatokat telepítése Azure verem használható). | _szükséges_ |
| **VMLocalCredential** | Adja meg az SQL erőforrás-szolgáltató VM a helyi rendszergazdai fiók hitelesítő adatait. | _szükséges_ |
| **PrivilegedEndpoint** | Adja meg az IP-cím vagy a Privleged végpont DNS-nevét. |  _szükséges_ |
| **DependencyFilesLocalPath** | A PFX-fájl a könyvtárban kell elhelyezni. | _nem kötelező_ (_kötelező_ több csomópont) |
| **DefaultSSLCertificatePassword** | A .pfx tanúsítvány jelszava | _szükséges_ |
| **MaxRetryCount** | Adja meg, majd ismételje meg minden egyes művelet, ha azt szeretné, hogy hány alkalommal hibát.| 2 |
| **RetryDuration** | Adja meg az időtúllépés másodpercben az újrapróbálkozások között. | 120 |
| **Eltávolítás** | Távolítsa el az erőforrás-szolgáltató és minden kapcsolódó erőforrások (lásd az alábbi megjegyzéseket:) | Nem |
| **DebugMode** | Megakadályozza az automatikus tisztítás hiba esetén | Nem |
| **AcceptLicense** | Fogadja el a GPL licenc (http://www.gnu.org/licenses/old-licenses/gpl-2.0.html) adatait kérő felület kihagyja | |

## <a name="remove-the-mysql-resource-provider-adapter"></a>A MySQL-erőforrás-szolgáltató Adapter eltávolítása

Távolítsa el az erőforrás-szolgáltató, fontos először távolítsa el a függőségeket.

1. Ellenőrizze, hogy az eredeti központi telepítési csomag ezen verzióját az erőforrás-szolgáltató letöltött.

2. Az összes bérlői adatbázisok törölni kell az erőforrás-szolgáltató (Ez az adatok nem törli). Ez a bérlők maguknak kell elvégezni.

3. Bérlők kell megszüntetni a névteret.

4. Rendszergazdai üzemeltetési kiszolgáló kell törölnie a MySQL-Adapter

5. Rendszergazda kell törölni a MySQL Adapter hivatkozó bármely tervek.

6. Rendszergazda bármely a MySQL-adapterhez kapcsolódó kvóták kell törölnie.

7. A telepítési parancsfájlt, és futtassa újra a - távolítsa el a paramétert, Azure Resource Manager végpontok, DirectoryTenantID és a szolgáltatás-rendszergazdai fiók hitelesítő adatait.




## <a name="next-steps"></a>További lépések

Próbálja más [PaaS szolgáltatások](azure-stack-tools-paas-services.md) hasonlóan a [erőforrás-szolgáltató SQL Server](azure-stack-sql-resource-provider-deploy.md) és a [alkalmazásszolgáltatások erőforrás-szolgáltató](azure-stack-app-service-overview.md).

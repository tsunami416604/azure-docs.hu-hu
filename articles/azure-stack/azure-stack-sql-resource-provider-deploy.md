---
title: "SQL-adatbázisok használata Azure veremben |} Microsoft Docs"
description: "Ismerje meg, hogyan telepítheti az SQL-adatbázisok Azure verem és a Gyorsműveletek központi telepítése az SQL Server erőforrás-szolgáltató adapter szolgáltatásként"
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
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 6e65af68dcd2306aabda65efdf8fe056c0d9b4a4
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/11/2017
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>SQL-adatbázis használata a Microsoft Azure veremben

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Az SQL Server erőforrás-szolgáltató adapter segítségével teszi közzé az SQL-adatbázisok a szolgáltatásként [Azure verem](azure-stack-poc.md). Az erőforrás-szolgáltató telepítése, és csatlakoztassa egy vagy több SQL Server-példányokat, után és a felhasználók hozhat létre:
- adatbázisok felhő natív alkalmazások
- SQL alapuló webhelyek
- SQL alapuló munkaterhelések rendszerű virtuális gép (VM), amely futtatja az SQL Server, minden alkalommal, amikor nincs.

Az erőforrás-szolgáltató nem támogatja az összes adatbázis felügyeleti funkcióit biztosítja [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Például a rugalmas adatbáziskészletek, és képes automatikusan tárcsázza a fel-le adatbázis teljesítménye nem érhetők el. Azonban az erőforrás biztosítja által támogatott szolgáltatók hasonló létrehozása, olvasása, frissítése és Törlés (CRUD) típusú műveletek. Az API nem található kompatibilis SQL-adatbázis.

## <a name="sql-server-resource-provider-adapter-architecture"></a>SQL Server erőforrás-szolgáltató Adapter architektúrája
Az erőforrás-szolgáltató három összetevőből épül fel:

- **Az SQL erőforrás szolgáltató adapter VM**, mely a szolgáltató services szolgáltatást futtató Windows virtuális gép.
- **Az erőforrás-szolgáltató maga**, amely feldolgozza a kiépítési kérelmekre, és tesz elérhetővé adatbázis-erőforrások.
- **SQL Server kiszolgálók**, adatbázisok, kapacitás nyújt nevű futtató kiszolgálók.

Meg kell egy (vagy több) SQL-kiszolgálókat hoz létre és/vagy a külső SQL Server-példányok hozzáférést biztosítanak.

## <a name="deploy-the-resource-provider"></a>Az erőforrás-szolgáltató telepítése

1. Ha még nem tette meg, regisztrálja a szoftverfejlesztői készlet, és töltse le a Windows Server 2016 Datacenter Core kép letölthető piactér felügyelet használatával. A Windows Server 2016 Core lemezképet kell használnia. Parancsfájl használata létrehozásához egy [Windows Server 2016 kép](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-default-image) -ügyeljen arra, hogy válassza ki a Core. A .NET 3.5 futásidejű már nincs szükség.

2. Jelentkezzen be egy olyan gazdagépre, férhetnek hozzá a kiemelt végpont virtuális Gépet.

    a. A Azure verem Development Kit (ASDK) telepítésekre jelentkezzen be a fizikai állomáson.

    b. Több csomópontos rendszerek esetében a gazdagép egy rendszer, amely hozzáférhet a kiemelt végpont kell lennie.

3. [Töltse le az SQL-szolgáltató bináris fájlt](https://aka.ms/azurestacksqlrp) és egy ideiglenes könyvtárhoz tartalmának önkibontó végrehajtható.

4. Az Azure-verem legfelső szintű tanúsítvány veszi át a kiemelt végpont. A ASDK önaláírt tanúsítvány jön létre a folyamat során. Több csomópontos meg kell adnia egy megfelelő tanúsítványt.

    Ha saját van szüksége, a következő tanúsítványra van szükség:

    A helyettesítő tanúsítványt \*.dbadapter.\< a régióban\>.\< külső fqdn\>. Ezt a tanúsítványt megbízhatónak kell lennie, például a állít ki egy hitelesítésszolgáltatónak. Ez azt jelenti, hogy a megbízhatósági lánc léteznie kell anélkül, hogy a köztes tanúsítványok. Egy egyetlen hely használhatja a explicit Virtuálisgép-név [sqladapter] telepítés során használt.


5. Nyissa meg a **új** emelt szintű (felügyeleti) PowerShell-konzolt, és módosítsa a könyvtárra, amelybe kibontotta a fájlokat. Egy új ablak segítségével a rendszer helytelen PowerShell-modul már be van töltve az esetleg felmerülő problémák elkerülése érdekében.

6. [Telepítse az Azure PowerShell verziója 1.2.11](azure-stack-powershell-install.md).

7. Futtassa a DeploySqlProvider.ps1 parancsfájlt az alábbi paraméterekkel.

A parancsfájl ezeket a lépéseket hajtja végre:

- A tanúsítványok és egyéb összetevők feltölteni a az Azure storage-fiók.
- Tegye közzé a gyűjtemény csomagok, hogy az SQL-adatbázisok a gyűjtemény telepítése.
- Az üzemeltető kiszolgálók telepítése egy gyűjteménycsomag közzététele
- A Windows Server 2016-lemezkép az 1. lépésben létrehozott virtuális gép telepítése, és az erőforrás-szolgáltató telepítése.
- Regisztrálja a helyi DNS-rekordot, amely a virtuális gép erőforrás-szolgáltató van leképezve.
- Az erőforrás-szolgáltató regisztrálása az a helyi Azure Resource Manager (felhasználó és rendszergazda).

> [!NOTE]
> Ha a telepítés több mint 90 percig tart, előfordulhat, hogy és a képernyőn, majd a naplófájlban megjelenik egy hibaüzenet, de a központi telepítés a hibás lépés a rendszer ismét megkísérli. Rendszerek, amelyek nem felelnek meg az ajánlott memória és vCPU paramétereknek nem lehet tudja telepíteni az SQL erőforrás-Szolgáltatónál.
>

Például futtathatja a PowerShell kérni (de a fiók- és szükség szerint módosítsa):

```
# Install the AzureRM.Bootstrapper module, set the profile, and install AzureRM and AzureStack modules
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2017-03-09-profile
Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force

# Use the NetBIOS name for the Azure Stack domain. On ASDK, the default is AzureStack
$domain = "AzureStack"
# Point to the directory where the RP installation files were extracted
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (can be AAD or ADFS)
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the Resource Provider VM
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# and the cloudadmin credential required for Privileged Endpoint access
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# change the following as appropriate
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change directory to the folder where you extracted the installation files
# and adjust the endpoints
.$tempDir\DeploySQLProvider.ps1 -AzCredential $AdminCreds -VMLocalCredential $vmLocalAdminCreds -CloudAdminCredential $cloudAdminCreds -PrivilegedEndpoint '10.10.10.10' -DefaultSSLCertificatePassword $PfxPass -DependencyFilesLocalPath $tempDir\cert
 ```

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider.ps1 paraméterek
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


## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>A verem Azure portál használatával a telepítés ellenőrzése

> [!NOTE]
>  Miután befejezte a telepítési parancsfájlt, szüksége lesz a portálhoz, és a felügyeleti panel frissítéséhez.


1. Jelentkezzen be a felügyeleti portál a szolgáltatás-rendszergazdaként.

2. Győződjön meg arról, hogy a telepítés sikeres volt. Tallózással keresse meg **erőforráscsoportok** &gt;, kattintson a **system.\< hely\>.sqladapter** erőforrás csoportból, és győződjön meg arról, hogy sikerült-e négy követelményszabályait.

      ![Az SQL RP a telepítés ellenőrzése](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)





## <a name="removing-the-sql-adapter-resource-provider"></a>Az SQL Adapter erőforrás-szolgáltató eltávolítása

Ahhoz, hogy távolítsa el az erőforrás-szolgáltató, létfontosságú, először távolítsa el a függőségeket.

1. Ellenőrizze, hogy az eredeti központi telepítési csomag ezen verzióját az erőforrás-szolgáltató letöltött.

2. Összes felhasználói adatbázis törölni kell az erőforrás-szolgáltató (Ez az adatok nem törli). Ez a felhasználók maguk kell elvégezni.

3. Rendszergazda törölni kell az üzemeltetési kiszolgáló az SQL-Adapter

4. Rendszergazda törölnie kell az SQL-Adapter hivatkozó bármely tervek.

5. Rendszergazda törölnie kell minden olyan termékváltozatok és az SQL-adapterhez kapcsolódó kvóták.

6. A telepítési parancsfájlt, és futtassa újra a - távolítsa el a paramétert, Azure Resource Manager végpontok, DirectoryTenantID és a szolgáltatás-rendszergazdai fiók hitelesítő adatait.


## <a name="next-steps"></a>Következő lépések


Próbálja más [PaaS szolgáltatások](azure-stack-tools-paas-services.md) hasonlóan a [MySQL Server erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md) és a [alkalmazásszolgáltatások erőforrás-szolgáltató](azure-stack-app-service-overview.md).

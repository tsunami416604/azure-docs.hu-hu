---
title: Tranzakciós replikáció konfigurálása két felügyelt példány és az SQL Server között
description: Egy oktatóanyag, amely konfigurálja a replikációt egy Publisher által felügyelt példány, egy Disztribútor által felügyelt példány és egy SQL Server-előfizető között egy Azure virtuális gépen, valamint a szükséges hálózati összetevőket, például a privát DNS-zónát és a VPN-társviszony-létesítést.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 11/21/2019
ms.openlocfilehash: fa6e393500e9deeb91ee84aa5255320003817f08
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76719891"
---
# <a name="tutorial-configure-transactional-replication-between-two-managed-instances-and-sql-server"></a>Oktatóanyag: Tranzakciós replikáció konfigurálása két felügyelt példány és az SQL Server között


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> - Felügyelt példány konfigurálása replikációs közzétevőként. 
> - Felügyelt példány konfigurálása replikációs forgalmazóként. 
> - SQL Server konfigurálása előfizetőként. 

![Replikáció SQL MI-pub, SQL MI Dist és SQL Server sub között](media/sql-database-managed-instance-failover-group-tutorial/sqlmi-to-sql-replication.png)

Ez az oktatóanyag tapasztalt közönség számára készült, és feltételezi, hogy a felhasználó ismeri a felügyelt példányok és az Azure-on belüli SQL Server virtuális gépek üzembe helyezését és csatlakoztatását. Mint ilyen, bizonyos lépéseket a bemutató glossed át. 

További információ: az [Azure SQL Database felügyelt példányáttekintése](sql-database-managed-instance-index.yml), [képességek](sql-database-managed-instance.md)és [SQL transactional replikációs](sql-database-managed-instance-transactional-replication.md) cikkek.

A felügyelt példány közzétevője és a felügyelt példány előfizetője közötti replikáció konfigurálásához olvassa el a [Tranzakciós replikáció konfigurálása két felügyelt példány között című témakört.](replication-with-sql-database-managed-instance.md) 

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez győződjön meg arról, hogy rendelkezik a következő előfeltételekkel:

- [Egy Azure-előfizetés](https://azure.microsoft.com/free/). 
- Két felügyelt példány ugyanazon a virtuális hálózaton belüli üzembe helyezésével kapcsolatos tapasztalat. 
- Egy SQL Server-előfizető, akár a helyszínen, akár egy Azure-beli virtuális gép. Ez az oktatóanyag egy Azure virtuális gép.  
- [SQL Server Management Studio (SSMS) 18.0-s vagy nagyobb](/sql/ssms/download-sql-server-management-studio-ssms).
- Az Azure [Powershell](/powershell/azure/install-az-ps?view=azps-1.7.0)legújabb verziója.
- A 445-ös és az 1433-as port engedélyezi az SQL-forgalmat az Azure tűzfalon és a Windows tűzfalon is. 

## <a name="1---create-the-resource-group"></a>1 - Az erőforráscsoport létrehozása
Új erőforráscsoport létrehozásához használja a következő PowerShell-kódrészletet:

```powershell-interactive
# set variables
$ResourceGroupName = "SQLMI-Repl"
$Location = "East US 2"

# Create a new resource group
New-AzResourceGroup -Name  $ResourceGroupName -Location $Location
```

## <a name="2---create-two-managed-instances"></a>2 - Két felügyelt példány létrehozása
Hozzon létre két felügyelt példányt az új erőforráscsoporton belül az [Azure Portal](https://portal.azure.com)használatával. 

- A közzétevő által felügyelt példány `sql-mi-publisher` neve: (néhány karakterrel együtt a randomizáláshoz), `vnet-sql-mi-publisher`és a virtuális hálózat nevének kell lennie.
- A forgalmazó által felügyelt példány `sql-mi-distributor` neve a következő(néhány karakterrel együtt a randomizációhoz), és ugyanabban a virtuális hálózatban kell lennie, _mint a közzétevő által felügyelt példánynak._

   ![A forgalmazó megjelenítői virtuális hálózatának használata](media/sql-database-managed-instance-configure-replication-tutorial/use-same-vnet-for-distributor.png)

A felügyelt példányok létrehozásáról további információt [a Felügyelt példány létrehozása a portálon című témakörben talál.](sql-database-managed-instance-get-started.md)

  > [!NOTE]
  > Az egyszerűség kedvéért, és mivel ez a leggyakoribb konfiguráció, ez az oktatóanyag azt javasolja, hogy a forgalmazó által felügyelt példányt helyezze ugyanabban a virtuális hálózatban, mint a közzétevő. Azonban lehetőség van a forgalmazó külön virtuális hálózatban való létrehozására. Ehhez be kell állítania a VPN-társviszony-létesítést a közzétevő és a forgalmazó virtuális hálózatai között, majd konfigurálnia kell a VPN-társviszony-létesítést a forgalmazó és az előfizető virtuális hálózatai között. 

## <a name="3---create-a-sql-server-vm"></a>3 - SQL Server virtuális gép létrehozása
Sql Server virtuális gép létrehozása az [Azure Portal használatával.](https://portal.azure.com) Az SQL Server virtuális gépnek a következő jellemzőkkel kell rendelkeznie:

- név:`sql-vm-sub`
- Kép: SQL Server 2016 vagy nagyobb
- Erőforráscsoport: ugyanaz, mint a felügyelt példány
- Virtuális hálózat:`sql-vm-sub-vnet` 

Az SQL Server virtuális gép Azure-ba történő üzembe helyezéséről további információt a [Rövid útmutató: SQL Server virtuális gép létrehozása](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md)című témakörben talál.

## <a name="4---configure-vpn-peering"></a>4 - A VPN-társviszony-létesítés konfigurálása
A VPN-társviszony-létesítés konfigurálásával engedélyezze a kommunikációt a két felügyelt példány virtuális hálózata és az SQL Server virtuális hálózata között. Ehhez használja ezt a PowerShell-kódrészletet:

```powershell-interactive
# Set variables
$SubscriptionId = '<SubscriptionID>'
$resourceGroup = 'SQLMI-Repl'
$pubvNet = 'sql-mi-publisher-vnet'
$subvNet = 'sql-vm-sub-vnet'
$pubsubName = 'Pub-to-Sub-Peer'
$subpubName = 'Sub-to-Pub-Peer'

$virtualNetwork1 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $pubvNet 

 $virtualNetwork2 = Get-AzVirtualNetwork `
  -ResourceGroupName $resourceGroup `
  -Name $subvNet  

# Configure VPN peering from publisher to subscriber
Add-AzVirtualNetworkPeering `
  -Name $pubsubName `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id

# Configure VPN peering from subscriber to publisher
Add-AzVirtualNetworkPeering `
  -Name $subpubName `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id

# Check status of peering on the publisher vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $pubvNet `
 | Select PeeringState

# Check status of peering on the subscriber vNet; should say connected
Get-AzVirtualNetworkPeering `
 -ResourceGroupName $resourceGroup `
 -VirtualNetworkName $subvNet `
 | Select PeeringState

```

A VPN-társviszony-létesítés létrehozása után tesztelje a kapcsolatot az SQL Server Management Studio (SSMS) sql serveren történő elindításával és mindkét felügyelt példányhoz való csatlakozással. Az SSMS használatával felügyelt példányokhoz való csatlakozásról az [SSMS használata a hibajelzőhöz való csatlakozáshoz](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance)című témakörben talál további információt. 

![A felügyelt példányok kapcsolatának tesztelése](media/sql-database-managed-instance-configure-replication-tutorial/test-connectivity-to-mi.png)

## <a name="5---create-private-dns-zone"></a>5 - Saját DNS-zóna létrehozása

A privát DNS-zóna lehetővé teszi a DNS-útválasztást a felügyelt példányok és az SQL Server között. 

### <a name="create-private-dns-zone"></a>Saját DNS-zóna létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza **az Erőforrás létrehozása** lehetőséget egy új Azure-erőforrás létrehozásához. 
1. Keressen `private dns zone` rá az Azure Marketplace-en. 
1. Válassza a Microsoft által közzétett **Magán DNS-zóna** erőforrást, majd a **Létrehozás** gombot a DNS-zóna létrehozásához. 
1. Válassza ki az előfizetést és az erőforráscsoportot a legördülő menüből. 
1. Adjon meg tetszőleges nevet a `repldns.com`DNS-zónának, például . 

   ![Saját DNS-zóna létrehozása](media/sql-database-managed-instance-configure-replication-tutorial/create-private-dns-zone.png)

1. Válassza az **Áttekintés + létrehozás** lehetőséget. Tekintse át a saját DNS-zóna paramétereit, majd válassza a **Létrehozás** gombot az erőforrás létrehozásához. 

### <a name="create-a-record"></a>Rekord létrehozása

1. Nyissa meg az új **privát DNS-zónát,** és válassza **az Áttekintés**lehetőséget. 
1. Új A-rekord létrehozásához válassza a **+ Rekordkészlet** lehetőséget. 
1. Adja meg az SQL Server virtuális gép nevét, valamint a saját belső IP-címet. 

   ![Rekord konfigurálása](media/sql-database-managed-instance-configure-replication-tutorial/configure-a-record.png)

1. Az A rekord létrehozásához válassza az **OK gombot.** 

### <a name="link-the-virtual-network"></a>A virtuális hálózat összekapcsolása

1. Nyissa meg az új **magánDNS-zónát,** és válassza a **Virtuális hálózati kapcsolatok lehetőséget.** 
1. Válassza a **+ Hozzáadás** lehetőséget. 
1. Adja meg a hivatkozás nevét, például `Pub-link`. 
1. Válassza ki az előfizetést a legördülő menüből, majd válassza ki a közzétevő által felügyelt példány virtuális hálózatát. 
1. Jelölje be az **Automatikus regisztráció engedélyezése jelölőnégyzetet.** 

   ![Vnet-kapcsolat létrehozása](media/sql-database-managed-instance-configure-replication-tutorial/configure-vnet-link.png)

1. A virtuális hálózat összekapcsolására **kattintson** az OK gombra. 
1. Ismételje meg ezeket a lépéseket, ha az előfizető `Sub-link`virtuális hálózatához szeretne hivatkozást hozzáadni, például a néven. 


## <a name="6---create-azure-storage-account"></a>6 - Azure storage-fiók létrehozása

[Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) a munkakönyvtárhoz, majd hozzon létre egy [fájlmegosztást](../storage/files/storage-how-to-create-file-share.md) a tárfiókon belül. 

Másolja a fájlmegosztás elérési útját a következő formátumban:`\\storage-account-name.file.core.windows.net\file-share-name`   

Például: `\\replstorage.file.core.windows.net\replshare`

Másolja a tároló-hozzáférési kulcs kapcsolati karakterláncát a következő formátumban:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`   

Például: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`


További információt a [Tárfiók hozzáférési kulcsainak kezelése című témakörben talál.](../storage/common/storage-account-keys-manage.md) 


## <a name="7---create-a-database"></a>7 - Adatbázis létrehozása
Hozzon létre egy új adatbázist a közzétevő hibanevében. Ehhez kövesse az alábbi lépéseket:

1. Indítsa el az SQL Server Management Studio (SSMS) alkalmazást az SQL Server kiszolgálón. 
1. Csatlakozzon `sql-mi-publisher` a felügyelt példányhoz. 
1. Nyisson meg egy **Új lekérdezés** ablakot, és hajtsa végre a következő T-SQL lekérdezést az adatbázis létrehozásához:

```sql
-- Create the databases
USE [master]
GO

-- Drop database if it exists
IF EXISTS (SELECT * FROM sys.sysdatabases WHERE name = 'ReplTutorial')
BEGIN
    DROP DATABASE ReplTutorial
END
GO

-- Create new database
CREATE DATABASE [ReplTutorial]
GO

-- Create table
USE [ReplTutorial]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO

-- Populate table with data
USE [ReplTutorial]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="8---configure-distribution"></a>8 - A disztribúció konfigurálása 
Miután létrejött a kapcsolat, és rendelkezik egy `sql-mi-distributor` mintaadatbázissal, konfigurálhatja a felügyelt példány disztribúcióját. Ehhez kövesse az alábbi lépéseket:

1. Indítsa el az SQL Server Management Studio (SSMS) alkalmazást az SQL Server kiszolgálón. 
1. Csatlakozzon `sql-mi-distributor` a felügyelt példányhoz. 
1. Nyisson meg egy **Új lekérdezés** ablakot, és futtassa a következő Transact-SQL kódot a terjesztési konfigurálásához a forgalmazó által felügyelt példányon: 

   ```sql
   EXEC sp_adddistpublisher @publisher = 'sql-mi-publisher.b6bf57.database.windows.net', -- primary publisher
        @distribution_db = N'distribution',
        @security_mode = 0,
        @login = N'azureuser',
        @password = N'<publisher_password>',
        @working_directory = N'\\replstorage.file.core.windows.net\replshare',
        @storage_connection_string = N'<storage_connection_string>'
        -- example: @storage_connection_string = N'DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net'

   ```

   > [!NOTE]
   > Ügyeljen arra, hogy csak`\`fordított perjeleket ( ) használjon a @working_directory paraméterhez. Perjel (`/`) használata hibát okozhat a fájlmegosztáshoz való csatlakozáskor. 

1. Csatlakozzon `sql-mi-publisher` a felügyelt példányhoz. 
1. Nyisson meg egy **Új lekérdezés** ablakot, és futtassa a következő Transact-SQL kódot a forgalmazó regisztrálásához a kiadónál: 

```sql
Use MASTER
EXEC sys.sp_adddistributor @distributor = 'sql-mi-distributor.b6bf57.database.windows.net', @password = '<distributor_admin_password>' 
```


## <a name="9---create-the-publication"></a>9 - A kiadvány létrehozása
A terjesztés konfigurálása után létrehozhatja a kiadványt. Ehhez kövesse az alábbi lépéseket: 

1. Indítsa el az SQL Server Management Studio (SSMS) alkalmazást az SQL Server kiszolgálón. 
1. Csatlakozzon `sql-mi-publisher` a felügyelt példányhoz. 
1. Az **Objektumkezelőben** **bontsa** ki a Replikáció csomópontot, és kattintson a jobb gombbal a **Helyi kiadvány** mappára. Válassza az **Új kiadvány lehetőséget...**. 
1. Válassza a **Tovább** gombot az üdvözlőlapon való továbblépéshez. 
1. A **Kiadványadatbázis** lapon jelölje `ReplTutorial` ki a korábban létrehozott adatbázist. Válassza a **Tovább lehetőséget.** 
1. A **Kiadvány típusa** lapon válassza a **Tranzakciós kiadvány**lehetőséget. Válassza a **Tovább lehetőséget.** 
1. A **Cikkek** lapon jelölje be a **Táblázatok jelölőnégyzetet.** Válassza a **Tovább lehetőséget.** 
1. A **Táblázatsorok szűrése** lapon válassza a **Tovább** gombot szűrők hozzáadása nélkül. 
1. A **Pillanatképügynök** lapon jelölje be a **Pillanatkép létrehozása című jelölőnégyzetet, és tartsa elérhetővé a pillanatképet az előfizetések inicializálásához.** Válassza a **Tovább lehetőséget.** 
1. Az **Ügynök biztonsága** lapon válassza a **Biztonsági beállítások lehetőséget. . .**. Adja meg az SQL Server bejelentkezési hitelesítő adatait a Snapshot ügynökhöz, és csatlakozzon a Publisherhez. A **Pillanatképügynök biztonsága** lap bezárásához **kattintson** az OK gombra. Válassza a **Tovább lehetőséget.** 

   ![Pillanatképügynök biztonságának konfigurálása](media/sql-database-managed-instance-configure-replication-tutorial/snapshot-agent-security.png)

1. A **Varázsló műveletek lapján** válassza **a Kiadvány létrehozása lehetőséget,** és (tetszés szerint) válassza **a Parancsfájl létrehozása lehetőséget, amely lépésekkel hozza létre a kiadványt,** ha ezt a parancsfájlt későbbre szeretné menteni. 
1. A **Varázsló befejezése** lapon nevezze `ReplTest` el a kiadványt, és a Közzététel létrehozásához válassza a **Tovább** gombot. 
1. A kiadvány létrehozása után frissítse a **Replikáció csomópontot** az **Objektumkezelőben,** és **bontsa** ki a Helyi kiadványok elemet az új kiadvány megtekintéséhez. 


## <a name="10---create-the-subscription"></a>10 - Az előfizetés létrehozása 

A kiadvány létrehozása után létrehozhatja az előfizetést. Ehhez kövesse az alábbi lépéseket: 

1. Indítsa el az SQL Server Management Studio (SSMS) alkalmazást az SQL Server kiszolgálón. 
1. Csatlakozzon `sql-mi-publisher` a felügyelt példányhoz. 
1. Nyisson meg egy **Új lekérdezés** ablakot, és futtassa a következő Transact-SQL kódot az előfizetés és a terjesztési ügynök hozzáadásához. Használja a DNS-t az előfizető nevében. 

```sql
use [ReplTutorial]
exec sp_addsubscription 
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@destination_db = N'ReplSub', 
@subscription_type = N'Push', 
@sync_type = N'automatic', 
@article = N'all', 
@update_mode = N'read only', 
@subscriber_type = 0

exec sp_addpushsubscription_agent
@publication = N'ReplTest',
@subscriber = N'sql-vm-sub.repldns.com', -- include the DNS configured in the private DNS zone
@subscriber_db = N'ReplSub', 
@job_login = N'azureuser', 
@job_password = '<Complex Password>', 
@subscriber_security_mode = 0, 
@subscriber_login = N'azureuser', 
@subscriber_password = '<Complex Password>', 
@dts_package_location = N'Distributor'
GO
```

## <a name="11---test-replication"></a>11 - Teszt replikáció 

A replikáció konfigurálása után tesztelheti azt, ha új elemeket szúr be a közzétevőbe, és figyeli, hogy a módosítások továbbítanak-e az előfizetőnek. 

Futtassa a következő T-SQL kódrészletet az előfizető sorainak megtekintéséhez:

```sql
Use ReplSub
select * from dbo.ReplTest
```

Futtassa a következő T-SQL kódrészletet további sorok beszúrásához a közzétevőbe, majd ellenőrizze újra a sorokat az előfizetőn. 

```sql
Use ReplTutorial
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

1. Nyissa meg az erőforráscsoportot az [Azure Portalon.](https://portal.azure.com) 
1. Jelölje ki a felügyelt példány(oka)t, majd válassza a **Törlés**gombot. Írja `yes` be a szövegmezőbe az erőforrás törlésének megerősítéséhez, majd válassza a **Törlés**lehetőséget. Ez a folyamat eltarthat egy ideig, hogy teljes a háttérben, és amíg ez nem történik meg, akkor nem lesz képes törölni a *virtuális fürt* vagy bármely más függő erőforrásokat. Figyelje a törlést a Tevékenység lapon, és ellenőrizze, hogy a felügyelt példány törölve lett-e. 
1. A felügyelt példány törlése után törölje a *virtuális fürtöt* úgy, hogy kijelöli azt az erőforráscsoportban, majd a Törlés parancsot **választja.** Írja `yes` be a szövegmezőbe az erőforrás törlésének megerősítéséhez, majd válassza a **Törlés**lehetőséget. 
1. Törölje a fennmaradó erőforrásokat. Írja `yes` be a szövegmezőbe az erőforrás törlésének megerősítéséhez, majd válassza a **Törlés**lehetőséget. 
1. Az erőforráscsoport törléséhez válassza az **Erőforráscsoport törlése**lehetőséget, `myResourceGroup`írja be az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget. 

## <a name="known-errors"></a>Ismert hibák

### <a name="windows-logins-are-not-supported"></a>A Windows bejelentkezések nem támogatottak

`Exception Message: Windows logins are not supported in this version of SQL Server.`

Az ügynök Windows-bejelentkezéssel lett konfigurálva, és helyette SQL Server bejelentkezést kell használnia. A **Közzététel tulajdonságainak** **Ügynök-biztonság** lapján a bejelentkezési hitelesítő adatokat SQL Server-bejelentkezésre módosíthatja. 


### <a name="failed-to-connect-to-azure-storage"></a>Nem sikerült csatlakozni az Azure Storage-hoz


`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 53.`

2019-11-19 02:21:05.07 Beszerzett Azure Storage Connection String for replstorage 2019-11-19 02:21:05.07 Csatlakozás Az Azure Files Storage '\\replstorage.file.core.windows.net\replshare' 2019-11-19 02:21:31.21 Nem sikerült csatlakozni az Azure Storage "" operációs rendszer hiba: 53.


Ennek valószínű, hogy a 445-ös port be van zárva az Azure tűzfal, a Windows tűzfal vagy mindkettő. 

`Connecting to Azure Files Storage '\\replstorage.file.core.windows.net\replshare' Failed to connect to Azure Storage '' with OS error: 55.`

A hiba a fájlmegosztás fájlelérési útján fordított perjel helyett perjel használata okozhatja ezt a hibát. Ez rendben `\\replstorage.file.core.windows.net\replshare` van: Ez okozhat OS 55 hiba:`'\\replstorage.file.core.windows.net/replshare'`

### <a name="could-not-connect-to-subscriber"></a>Nem lehet csatlakozni az előfizetőhöz

`The process could not connect to Subscriber 'SQL-VM-SUB`
`Could not open a connection to SQL Server [53].`
`A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections.`

Lehetséges megoldások:
- Ellenőrizze, hogy az 1433-as port nyitva van-e. 
- Győződjön meg arról, hogy a TCP/IP engedélyezve van az előfizetőn. 
- Ellenőrizze, hogy a DNS-nevet használta-e az előfizető létrehozásakor. 
- Ellenőrizze, hogy a virtuális hálózatok megfelelően vannak-e csatolva a privát DNS-zónában. 
- Ellenőrizze, hogy az A-rekord megfelelően van-e konfigurálva. 
- Ellenőrizze, hogy a VPN-társviszony-létesítés megfelelően van-e konfigurálva. 

### <a name="no-publications-to-which-you-can-subscribe"></a>Nincsenek olyan kiadványok, amelyekre feliratkozhatna

Amikor új előfizetést ad hozzá az **Új előfizetés** varázslóval, a **Kiadvány** lapon előfordulhat, hogy nincsenek elérhető beállításként felsorolt adatbázisok és kiadványok, és a következő hibaüzenet jelenhet meg:

`There are no publications to which you can subscribe, either because this server has no publications or because you do not have sufficient privileges to access the publications.`
 
Bár lehetséges, hogy ez a hibaüzenet pontos, és valóban nem érhetők el a közzétevő, amelyhez csatlakozott, vagy nem rendelkezik megfelelő engedélyekkel, ezt a hibát az SQL Server Management Studio egy régebbi verziója is okozhatja. Próbáljon meg frissíteni az SQL Server Management Studio 18.0-s vagy annál nagyobb verzióra, hogy ezt kiváltó okként kizárja. 


## <a name="next-steps"></a>További lépések

### <a name="enable-security-features"></a>Biztonsági szolgáltatások engedélyezése

Az adatbázis védelmének átfogó listáját az alábbi [felügyelt példányképességek biztonsági szolgáltatásairól](sql-database-managed-instance.md#azure-sql-database-security-features) szóló cikkismerteti. A következő biztonsági szolgáltatásokat tárgyaljuk:

- [Felügyelt példány naplózása](sql-database-managed-instance-auditing.md) 
- [Mindig titkosított](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Fenyegetés észlelése](sql-database-managed-instance-threat-detection.md) 
- [Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking)
- [Sorszintű biztonság](/sql/relational-databases/security/row-level-security) 
- [Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Felügyelt példány képességei

A felügyelt példányképességeinek teljes áttekintését a következő témakörben találja:

> [!div class="nextstepaction"]
> [Felügyelt példány képességei](sql-database-managed-instance.md)

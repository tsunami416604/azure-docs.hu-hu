---
title: Replikáció konfigurálása felügyelt példányadatbázisban
description: Ismerje meg, hogyan konfigurálja a tranzakciós replikációt az Azure SQL Database által felügyelt példány közzétevő/forgalmazó és a felügyelt példány előfizetője között.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 9af7b471210ca3cc69428e68aef4aafaee159344
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299073"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Replikáció konfigurálása Azure SQL Database felügyelt példányadatbázisában

A tranzakciós replikáció lehetővé teszi az adatok replikálását egy Azure SQL Database felügyelt példányadatbázisába egy SQL Server-adatbázisból vagy egy másik példányadatbázisból. 

Ez a cikk bemutatja, hogyan konfigurálhatja a replikációt egy felügyelt példány közzétevője/forgalmazója és egy felügyelt példány előfizetője között. 

![Replikálás két felügyelt példány között](media/replication-with-sql-database-managed-instance/sqlmi-sqlmi-repl.png)

A tranzakciós replikáció segítségével leküldéses módosítások egy példány adatbázis az Azure SQL Database felügyelt példány:

- SQL Server adatbázis.
- Egyetlen adatbázis az Azure SQL Database-ben.
- Egy készletbe adott adatbázis egy Azure SQL Database rugalmas készletben.
 
A tranzakciós replikáció nyilvános előzetes verzióban érhető el az [Azure SQL Database felügyelt példányán.](sql-database-managed-instance.md) A felügyelt példányok közzétevői, forgalmazói és előfizetői adatbázisokat üzemeltethetnek. Tekintse meg a [tranzakciós replikációs konfigurációk](sql-database-managed-instance-transactional-replication.md#common-configurations) at az elérhető konfigurációkról.

  > [!NOTE]
  > - Ez a cikk célja, hogy a felhasználó konfigurálása replikáció egy Azure Database felügyelt példány a végpontok között, kezdve az erőforráscsoport létrehozásával. Ha már felügyelt példányokat telepített, ugorjon a [4.](#4---create-a-publisher-database) [Step 6](#6---configure-distribution)  
  > - Ez a cikk konfigurálja a közzétevő és a forgalmazó ugyanazon a felügyelt példányon. Ha a forgalmazót egy külön manged példányra szeretné helyezni, olvassa el a [Replikáció konfigurálása a mi-közzétevő és a MI-forgalmazó közötti replikáció konfigurálása című oktatóanyagcímű témakört.](sql-database-managed-instance-configure-replication-tutorial.md) 

## <a name="requirements"></a>Követelmények

Egy felügyelt példány közzétevőként és/vagy forgalmazóként való működéséhez a következőkre van szükség:

- Hogy a közzétevő által felügyelt példány ugyanazon a virtuális hálózaton, mint a forgalmazó és az előfizető, vagy [a virtuális hálózati társviszony-létesítés](../virtual-network/tutorial-connect-virtual-networks-powershell.md) jött létre a virtuális hálózatok között mindhárom entitások. 
- A kapcsolat SQL-hitelesítést használ a replikációs résztvevők között.
- Egy Azure Storage-fiók megosztás a replikációs munkakönyvtárhoz.
- A 445-ös port (TCP kimenő) meg van nyitva az NSG biztonsági szabályaiban a felügyelt példányok számára az Azure-fájlmegosztás eléréséhez.  Ha a hiba "nem sikerült csatlakozni \<az Azure storage storage fiók neve> operációs rendszer hibája 53", hozzá kell adnia egy kimenő szabályt az NSG a megfelelő SQL felügyelt példány alhálózat.


 > [!NOTE]
 > Az Azure SQL Database egyetlen adatbázisai és készletezése csak előfizetők lehetnek. 


## <a name="features"></a>Szolgáltatások

Támogatja:

- Az SQL Server helyszíni és felügyelt példányainak tranzakciós és pillanatkép-replikációs keveréke az Azure SQL Database-ben.
- Az előfizetők lehetnek helyszíni SQL Server-adatbázisokban, egyetlen adatbázisokban/felügyelt példányokban az Azure SQL Database-ben, vagy az Azure SQL Database rugalmas készleteiben lévő készletezett adatbázisok.
- Egyirányú vagy kétirányú replikáció.

Az Azure SQL Database egyik felügyelt példánya nem támogatja a következő funkciókat:

- [Updatable előfizetések](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Aktív georeplikáció](sql-database-active-geo-replication.md) tranzakciós replikációval. Az aktív georeplikáció helyett használja az [automatikus feladatátvételi csoportokat,](sql-database-auto-failover-group.md)de vegye figyelembe, hogy a kiadványt [manuálisan](sql-database-managed-instance-transact-sql-information.md#replication) kell törölni az elsődleges felügyelt példányból, és újra kell létrehozni a másodlagos felügyelt példányon a feladatátvétel után.  
 
## <a name="1---create-a-resource-group"></a>1 - Erőforráscsoport létrehozása

Az [Azure Portal](https://portal.azure.com) használatával hozzon létre `SQLMI-Repl`egy erőforráscsoportot a nevével.  

## <a name="2---create-managed-instances"></a>2 - Felügyelt példányok létrehozása

Az [Azure Portal](https://portal.azure.com) használatával két [felügyelt példányt](sql-database-managed-instance-create-tutorial-portal.md) hozhat létre ugyanazon a virtuális hálózaton és alhálózaton. Nevezze el például a két felügyelt példányt:

- `sql-mi-pub`(néhány karakterrel együtt a randomizációhoz)
- `sql-mi-sub`(néhány karakterrel együtt a randomizációhoz)

Az Azure SQL Database által felügyelt példányokhoz [való csatlakozáshoz konfigurálnia](sql-database-managed-instance-configure-vm.md) kell egy Azure virtuális gép. 

## <a name="3---create-azure-storage-account"></a>3 - Azure storage-fiók létrehozása

[Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) a munkakönyvtárhoz, majd hozzon létre egy [fájlmegosztást](../storage/files/storage-how-to-create-file-share.md) a tárfiókon belül. 

Másolja a fájlmegosztás elérési útját a következő formátumban:`\\storage-account-name.file.core.windows.net\file-share-name`

Például: `\\replstorage.file.core.windows.net\replshare`

Másolja a tároló-hozzáférési kulcsokat a következő formátumban:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Például: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

További információt a [Tárfiók hozzáférési kulcsainak kezelése című témakörben talál.](../storage/common/storage-account-keys-manage.md) 

## <a name="4---create-a-publisher-database"></a>4 - Közzétevő-adatbázis létrehozása

Csatlakozzon `sql-mi-pub` a felügyelt példányhoz az SQL Server Management Studio használatával, és futtassa a következő Transact-SQL (T-SQL) kódot a közzétevői adatbázis létrehozásához:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
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

## <a name="5---create-a-subscriber-database"></a>5 - Előfizetői adatbázis létrehozása

Csatlakozzon `sql-mi-sub` a felügyelt példányhoz az SQL Server Management Studio használatával, és futtassa a következő T-SQL kódot az üres előfizetői adatbázis létrehozásához:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
    ID INT NOT NULL PRIMARY KEY,
    c1 VARCHAR(100) NOT NULL,
    dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 - A disztribúció konfigurálása

Csatlakozzon `sql-mi-pub` a felügyelt példányhoz az SQL Server Management Studio használatával, és futtassa a következő T-SQL kódot a terjesztési adatbázis konfigurálásához. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 - A gyártó konfigurálása a forgalmazó használatára 

A közzétevő `sql-mi-pub`által felügyelt példányon módosítsa a lekérdezés végrehajtását [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) módra, és futtassa a következő kódot az új forgalmazó regisztrálásához a közzétevőnél. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Ügyeljen arra, hogy csak`\`fordított perjeleket ( ) használjon a file_storage paraméterhez. Perjel (`/`) használata hibát okozhat a fájlmegosztáshoz való csatlakozáskor. 

Ez a parancsfájl konfigurálja a helyi közzétevőt a felügyelt példányon, hozzáad egy csatolt kiszolgálót, és létrehoz egy feladatkészletet az SQL Server Agent számára. 

## <a name="8---create-publication-and-subscriber"></a>8 - Közzététel és előfizető létrehozása

[SqlCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) mód használatával futtassa a következő T-SQL parancsfájlt az adatbázis replikációjának engedélyezéséhez, és konfigurálja a replikációt a közzétevő, a forgalmazó és az előfizető között. 

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reaer agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle 
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 - Ügynökparamétereinek módosítása

Az Azure SQL Database felügyelt példánya jelenleg tapasztalható néhány háttérproblémák a replikációs ügynökök kel való kapcsolat. A probléma megoldása közben a megoldás a replikációs ügynökök bejelentkezési időtúlértékének növelésére szolgáló megoldás. 

A bejelentkezési időszám növeléséhez futtassa a következő T-SQL parancsot a közzétevőn: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Futtassa újra a következő T-SQL parancsot, hogy a bejelentkezési időtúlot visszaállítsa az alapértelmezett értékre, ha erre szüksége van:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

A módosítások alkalmazásához indítsa újra mindhárom ügynököt. 

## <a name="10---test-replication"></a>10 - Teszt replikáció

A replikáció konfigurálása után tesztelheti azt, ha új elemeket szúr be a közzétevőbe, és figyeli, hogy a módosítások továbbítanak-e az előfizetőnek. 

Futtassa a következő T-SQL kódrészletet az előfizető sorainak megtekintéséhez:

```sql
select * from dbo.ReplTest
```

Futtassa a következő T-SQL kódrészletet további sorok beszúrásához a közzétevőbe, majd ellenőrizze újra a sorokat az előfizetőn. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kiadvány eldobásához futtassa a következő T-SQL parancsot:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

A replikációs beállítás adatbázisból való eltávolításához futtassa a következő T-SQL parancsot:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

A közzététel és a terjesztés letiltásához futtassa a következő T-SQL parancsot:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Az Azure-erőforrások törléséhez [törölje a felügyelt példány erőforrásait az erőforráscsoportból,](../azure-resource-manager/management/manage-resources-portal.md#delete-resources) `SQLMI-Repl`majd törölje az erőforráscsoportot. 

   
## <a name="see-also"></a>Lásd még:

- [Tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md)
- [Oktatóanyag: Tranzakciós replikáció konfigurálása a mi-közzétevő és az SQL Server-előfizető között](sql-database-managed-instance-configure-replication-tutorial.md)
- [Mi az a felügyelt példány?](sql-database-managed-instance.md)

---
title: Replikáció konfigurálása Azure SQL Database felügyelt példány-adatbázisban | Microsoft Docs
description: Tudnivalók a tranzakciós replikáció konfigurálásáról egy Azure SQL Database felügyelt példány adatbázisában
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
ms.date: 02/07/2019
ms.openlocfilehash: 3b76dc546b46718378d9b22ad80e17849eaf532d
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884078"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Replikáció konfigurálása Azure SQL Database felügyelt példány-adatbázisban

A tranzakciós replikáció lehetővé teszi az adatok replikálását egy Azure SQL Database felügyelt példány-adatbázisba egy SQL Server-adatbázisból vagy egy másik példány-adatbázisból. 

A tranzakciós replikálás használatával leküldheti a Azure SQL Database felügyelt példányban lévő példány-adatbázisban végrehajtott módosításokat a következőre:

- Egy SQL Server-adatbázis.
- Egyetlen adatbázis a Azure SQL Databaseban.
- Készletezett adatbázis egy Azure SQL Database rugalmas készletben.
 
A tranzakciós replikáció nyilvános előzetes verzióban érhető el [Azure SQL Database felügyelt példányon](sql-database-managed-instance.md). A felügyelt példányok közzétevői, terjesztői és előfizetői adatbázisokat is futtathatnak. Lásd: [tranzakciós replikációs konfigurációk](sql-database-managed-instance-transactional-replication.md#common-configurations) az elérhető konfigurációkhoz.

  > [!NOTE]
  > Ebből a cikkből megtudhatja, hogyan konfigurálhat egy felhasználót a replikáció egy Azure Database felügyelt példányra való konfigurálásához a végponttól a végéig, kezdve az erőforráscsoport létrehozásával. Ha már telepítette a felügyelt példányokat, ugorjon a [4](#4---create-a-publisher-database) . lépésre a közzétevői adatbázis létrehozásához, vagy a [6. lépést](#6---configure-distribution) , ha már rendelkezik közzétevői és előfizetői adatbázissal, és készen áll a replikáció konfigurálására.  

## <a name="requirements"></a>Követelmények

A felügyelt példányok közzétevőként és/vagy terjesztőként való konfigurálásához a következők szükségesek:

- A felügyelt példány jelenleg nem vesz részt földrajzi replikálási kapcsolatban.
- A közzétevő felügyelt példánya ugyanazon a virtuális hálózaton van, mint a terjesztő és az előfizető, vagy a [vNet](../virtual-network/tutorial-connect-virtual-networks-powershell.md) -társítás a mindhárom entitás virtuális hálózatai között létrejött. 
- A kapcsolat SQL-hitelesítést használ a replikációs résztvevők között.
- Egy Azure Storage-fiók megosztása a replikációs munkakönyvtárhoz.
- Az 445-as (TCP kimenő) port a felügyelt példányok NSG biztonsági szabályaiban van megnyitva az Azure-fájlmegosztás eléréséhez. 


 > [!NOTE]
 > A Azure SQL Databaseban az önálló adatbázisok és a készletezett adatbázisok csak előfizetők lehetnek. 


## <a name="features"></a>Szolgáltatások

A következőket támogatja:

- SQL Server helyszíni és felügyelt példányainak tranzakciós és pillanatkép-replikációs kombinációja Azure SQL Databaseban.
- Az előfizetők a helyszíni SQL Server adatbázisaiban, az önálló adatbázisokban/felügyelt példányokban Azure SQL Database vagy a készletezett adatbázisok Azure SQL Database rugalmas készletekben.
- Egyirányú vagy kétirányú replikálás.

A következő szolgáltatások nem támogatottak a felügyelt példányokban Azure SQL Databaseban:

- [Frissíthető](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication)előfizetések.
- [Aktív geo-replikáció](sql-database-active-geo-replication.md) tranzakciós replikációval. Az aktív földrajzi replikálás helyett használjon [automatikus feladatátvételi csoportokat](sql-database-auto-failover-group.md), de vegye figyelembe, hogy a kiadványt manuálisan kell [törölni](sql-database-managed-instance-transact-sql-information.md#replication) az elsődleges felügyelt példányból, és újból létre kell hozni a másodlagos felügyelt példányon a feladatátvételt követően.  
 
## <a name="1---create-a-resource-group"></a>1 – erőforráscsoport létrehozása

A [Azure Portal](https://portal.azure.com) használatával hozzon létre egy erőforráscsoportot a névvel `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 – felügyelt példányok létrehozása

A [Azure Portal](https://portal.azure.com) használatával hozzon létre két [felügyelt példányt](sql-database-managed-instance-create-tutorial-portal.md) ugyanazon a virtuális hálózaton és alhálózaton. A két felügyelt példány neve:

- `sql-mi-pub`
- `sql-mi-sub`

Az Azure SQL Database felügyelt példányaihoz való kapcsolódáshoz konfigurálnia kell [egy Azure-beli virtuális gépet](sql-database-managed-instance-configure-vm.md) is. 

## <a name="3---create-azure-storage-account"></a>3 – Azure Storage-fiók létrehozása

[Hozzon létre egy Azure Storage-fiókot](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) a munkakönyvtár számára, majd hozzon létre egy [fájlmegosztást](../storage/files/storage-how-to-create-file-share.md) a Storage-fiókon belül. 

Másolja a fájlmegosztás elérési útját a (z) formátumban:`\\storage-account-name.file.core.windows.net\file-share-name`

Másolja a Storage-hozzáférési kulcsokat a formátumba:`DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 További információért lásd: [View and copy storage access keys](../storage/common/storage-account-manage.md#access-keys) (A tárelérési kulcsok megtekintése és másolása). 

## <a name="4---create-a-publisher-database"></a>4 – közzétevő adatbázis létrehozása

Kapcsolódjon a `sql-mi-pub` felügyelt példányhoz SQL Server Management Studio használatával, és futtassa a következő Transact-SQL (T-SQL) kódot a közzétevő adatbázis létrehozásához:

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

## <a name="5---create-a-subscriber-database"></a>5 – előfizetői adatbázis létrehozása

Kapcsolódjon a `sql-mi-sub` felügyelt példányhoz SQL Server Management Studio használatával, és futtassa az alábbi T-SQL-kódot az üres előfizetői adatbázis létrehozásához:

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

## <a name="6---configure-distribution"></a>6 – eloszlás konfigurálása

Kapcsolódjon a `sql-mi-pub` felügyelt példányhoz SQL Server Management Studio segítségével, és futtassa az alábbi T-SQL-kódot a terjesztési adatbázis konfigurálásához. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 – a közzétevő konfigurálása a terjesztő használatára 

A közzétevő felügyelt példányán `sql-mi-pub`módosítsa a lekérdezés végrehajtását [Sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) módra, és futtassa a következő kódot az új terjesztő a közzétevővel való regisztrálásához. 

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"


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

Ez a parancsfájl egy helyi közzétevőt konfigurál a felügyelt példányon, hozzáadja a csatolt kiszolgálót, és létrehoz egy feladatot a SQL Server Agent számára. 

## <a name="8---create-publication-and-subscriber"></a>8 – kiadvány és előfizető létrehozása

A [Sqlcmd](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) mód használatával futtassa az alábbi T-SQL-szkriptet az adatbázis replikációjának engedélyezéséhez, és konfigurálja a közzétevő, a terjesztő és az előfizető közötti replikálást. 

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
  @job_login = N'$(target_username)',
  @job_password = N'$(target_password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 – ügynök paramétereinek módosítása

Azure SQL Database felügyelt példány jelenleg néhány háttérbeli problémát tapasztal a replikációs ügynökökkel való kapcsolattal. A probléma megoldása közben a megkerülő megoldással növelheti a replikációs ügynökök bejelentkezési időtúllépési értékét. 

A bejelentkezés időtúllépésének növeléséhez futtassa a következő T-SQL-parancsot a közzétevőn: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Futtassa újra az alábbi T-SQL-parancsot a bejelentkezési időkorlát alapértelmezett értékre való visszaállításához:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

A módosítások érvénybe léptetéséhez indítsa újra mindhárom ügynököt. 

## <a name="10---test-replication"></a>10 – a replikáció tesztelése

Miután konfigurálta a replikálást, tesztelheti úgy, hogy új elemeket szúr be a közzétevőbe, és figyeli a módosításokat az előfizetőnek. 

A következő T-SQL-kódrészlet futtatásával tekintheti meg az előfizető sorait:

```sql
select * from dbo.ReplTest
```

Futtassa az alábbi T-SQL-kódrészletet további sorok beszúrásához a közzétevőn, majd jelölje be újra a sorokat az előfizetőn. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A kiadvány eldobásához futtassa az alábbi T-SQL-parancsot:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

A replikálási lehetőség az adatbázisból való eltávolításához futtassa a következő T-SQL-parancsot:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

A közzététel és a terjesztés letiltásához futtassa az alábbi T-SQL-parancsot:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Az Azure-erőforrások tisztításához [törölje a felügyelt példányok erőforrásait az erőforráscsoporthoz](../azure-resource-manager/manage-resources-portal.md#delete-resources) , majd törölje az erőforráscsoportot `SQLMI-Repl`. 

   
## <a name="see-also"></a>Lásd még:

- [Tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md)
- [Mi az a felügyelt példány?](sql-database-managed-instance.md)

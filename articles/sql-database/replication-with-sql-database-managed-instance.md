---
title: Replikálás konfigurálása a egy Azure SQL Database felügyelt példány adatbázisa |} A Microsoft Docs
description: Egy Azure SQL Database felügyelt példány adatbázisa a tranzakciós replikáció konfigurálásával kapcsolatos tudnivalók
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: c72c4d21f948d6d6c4d1d4598efa0e13de9705a6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64926196"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Replikálás konfigurálása a egy Azure SQL Database felügyelt példány adatbázisa

Tranzakciós replikáció lehetővé teszi adatok replikálása egy Azure SQL Database felügyelt példány adatbázisa SQL Server-adatbázis vagy egy másik példány adatbázisa be. 

Tranzakciós replikáció használatával az Azure SQL Database felügyelt példány az egy példány adatbázison végzett módosításokat:

- SQL Server-adatbázis.
- Önálló adatbázis az Azure SQL Database-ben.
- Az Azure SQL Database rugalmas készlet készletezett adatbázis.
 
Tranzakciós replikáció jelenleg nyilvános előzetes verzióban a [Azure SQL Database felügyelt példány](sql-database-managed-instance.md). Felügyelt példány közzétevő terjesztője és előfizető adatbázisok is üzemeltethet. Lásd: [tranzakciós replikáció konfigurációk](sql-database-managed-instance-transactional-replication.md#common-configurations) elérhető konfigurációk esetén.

  > [!NOTE]
  > A cikk célja, hogy egy felhasználó a replikáció konfigurálása az Azure Database felügyelt példánya a teljes körű, kezdve az erőforráscsoport létrehozásakor. Ha már rendelkezik felügyelt példányok üzembe helyezett, folytassa a [4. lépés](#4---create-a-publisher-database) a közzétevői adatbázis létrehozásához vagy [6. lépés](#6---configure-distribution) Ha már rendelkezik közzétevő és előfizető-adatbázissal, és készen áll replikáció konfigurálása.  

## <a name="requirements"></a>Követelmények

A közzétevő és/vagy a forgalmazó adatbázisként működik, a felügyelt példány konfigurálása szükséges:

- Hogy a felügyelt példány nem jelenleg vesz részt a georeplikációs kapcsolatban.
- A terjesztő és az előfizető azonos virtuális hálózaton van, hogy a közzétevő felügyelt példány vagy [virtuális hálózatok közötti társviszony](../virtual-network/tutorial-connect-virtual-networks-powershell.md) létrejött a virtuális hálózat összes három entitások között. 
- Kapcsolat SQL-hitelesítést használ a replikációs résztvevők között.
- Az Azure Storage-fiók megosztások a replikációs munkakönyvtárhoz.
- 445-ös (TCP, kimenő) meg nyitva, a biztonsági szabályok az NSG-t a felügyelt példányok az Azure-fájlmegosztás eléréséhez. 


 > [!NOTE]
 > Önálló adatbázisok és a készletezett adatbázisok Azure SQL Database-ben csak előfizetők lehetnek. 


## <a name="features"></a>Szolgáltatások

Támogatott rendszerek:

- A helyszíni SQL Server és az Azure SQL Database felügyelt példány tranzakciós és pillanatkép-replikációs arányát.
- A helyszíni SQL Server-adatbázisok, önálló adatbázisok és felügyelt példányok az Azure SQL Database vagy Azure SQL Database rugalmas készletei a készletezett adatbázisok előfizetőket is lehet.
- Egyirányú és kétirányú replikációt.

A következő funkciók nem támogatottak az Azure SQL Database felügyelt példány:

- [Frissíthető előfizetések](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication).
- [Aktív georeplikáció](sql-database-active-geo-replication.md) és [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md) nem lehet, ha a tranzakciós replikáció van konfigurálva.
 
## <a name="1---create-a-resource-group"></a>1 – hozzon létre egy erőforráscsoportot

Használja a [az Azure portal](https://portal.azure.com) hozzon létre egy erőforráscsoportot nevű `SQLMI-Repl`.  

## <a name="2---create-managed-instances"></a>2 – felügyelt példány létrehozása

Használja a [az Azure portal](https://portal.azure.com) létrehozása két [felügyelt példányai](sql-database-managed-instance-create-tutorial-portal.md) az azonos virtuális hálózatot és alhálózatot az. A két felügyelt példány neve:

- `sql-mi-pub`
- `sql-mi-sub`

Is kell [csatlakozni egy Azure virtuális gép konfigurálása](sql-database-managed-instance-configure-vm.md) az Azure SQL Database felügyelt példányai. 

## <a name="3---create-azure-storage-account"></a>3 – az Azure Storage-fiók létrehozása

[Hozzon létre egy Azure Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) a munkakönyvtárhoz, majd hozzon létre egy [fájlmegosztás](../storage/files/storage-how-to-create-file-share.md) a tárfiókon belül. 

Másolja a fájlmegosztás útvonalának formátumban: `\\storage-account-name.file.core.windows.net\file-share-name`

Másolja a tárelérési kulcsok formátuma: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

 További információért lásd: [View and copy storage access keys](../storage/common/storage-account-manage.md#access-keys) (A tárelérési kulcsok megtekintése és másolása). 

## <a name="4---create-a-publisher-database"></a>4 – közzétevő-adatbázis létrehozása

Csatlakozás a `sql-mi-pub` felügyelt példány az SQL Server Management Studio használatával, és futtassa a következő Transact-SQL (T-SQL) kódot a közzétevői adatbázis létrehozásához:

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

## <a name="5---create-a-subscriber-database"></a>5 – előfizető-adatbázis létrehozása

Csatlakozás a `sql-mi-sub` felügyelt példány az SQL Server Management Studio használatával, és futtassa a következő T-SQL-kódot az előfizető üres adatbázis létrehozásához:

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

## <a name="6---configure-distribution"></a>6 – a terjesztés konfigurálása

Csatlakozás a `sql-mi-pub` felügyelt példány az SQL Server Management Studio használatával, és futtassa a következő T-SQL-kódot a terjesztési adatbázis konfigurálása. 

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 – közzétevő terjesztője konfigurálása 

A kiadói fiókjában a felügyelt példány `sql-mi-pub`, módosítsa a lekérdezés végrehajtása a [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) módban, és futtassa a következő kódot az új terjesztőhöz Regisztrálás a kiadói fiókjában. 

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
  @storage_connection_string = N'$(file_storage_key)';
```

Ez a szkript egy helyi közzétevő konfigurálja a következő felügyelt példányt, hozzáadja a csatolt kiszolgáló, és a feladatok sorozatát hoz létre az SQL Server Agent. 

## <a name="8---create-publication-and-subscriber"></a>8 – közzététel és az előfizető létrehozása

Használatával [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) mód, futtassa a következő T-SQL parancsfájlt engedélyezze a replikációt az adatbázis és a replikálást a közzétevő, forgalmazó és előfizető között. 

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

## <a name="9---modify-agent-parameters"></a>9 – ügynök paraméterek módosítása

Az Azure SQL Database felügyelt példány jelenleg a replikációs ügynökök kapcsolattal rendelkező háttérrendszer problémák tapasztalhatók. Amíg ez a probléma javítása folyamatban foglalkozik, növelje a bejelentkezési időkorlát értékét a replikációs ügynökök megkerülő megoldással. 

A következő T-SQL parancsot a közzétevőn az bejelentkezési időtúllépési: 

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Futtassa a következő T-SQL parancsot újra a bejelentkezési időkorlát állítsa vissza az alapértelmezett érték a, kell meg kell tennie:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30' 
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Indítsa újra a alkalmazni ezeket a módosításokat az összes három ügynök. 

## <a name="10---test-replication"></a>10 - replikáció tesztelése

Replikáció konfigurálása után tesztelheti a közzétevőn új elemek beszúrása és figyelése a módosítások propagálása az előfizető által. 

Futtassa a következő T-SQL-kódrészlet megtekintéséhez a sorokat az előfizetőn:

```sql
select * from dbo.ReplTest
```

Futtassa a következő T-SQL-kódrészlet beszúrása a közzétevőn további sorokat, és ezután ellenőrizze a sorokat az előfizetőn újra. 

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Dobni a kiadványt, futtassa a következő T-SQL-parancsot:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Az adatbázisból a következő replikálási beállítás eltávolításához futtassa a következő T-SQL-parancsot:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Közzététel és terjesztés letiltása, futtassa a következő T-SQL-parancsot:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Törölheti is az Azure-erőforrások által [a felügyelt példány erőforrások törlését az erőforráscsoport](../azure-resource-manager/manage-resources-portal.md#delete-resources) majd törölje az erőforráscsoportot `SQLMI-Repl`. 

   
## <a name="see-also"></a>Lásd még:

- [Tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md)
- [Mi a felügyelt példány?](sql-database-managed-instance.md)

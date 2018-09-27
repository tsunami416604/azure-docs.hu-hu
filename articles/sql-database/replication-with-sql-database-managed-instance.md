---
title: Replikálás az Azure SQL Database felügyelt példány |} A Microsoft Docs
description: További információ az SQL Server-replikáció használata Azure SQL Database felügyelt példánya
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
ms.date: 09/25/2018
ms.openlocfilehash: 95c27bcc99f08cb1e4998e43a6a2abd508bee0ac
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228366"
---
# <a name="replication-with-sql-database-managed-instance"></a>A replikáció az SQL Database felügyelt példánya

Nyilvános előzetes verzióban érhető el replikációs [Azure SQL Database felügyelt példányába](sql-database-managed-instance.md). Felügyelt példány közzétevő terjesztője és előfizető adatbázisok is üzemeltethet.

## <a name="common-configurations"></a>Általános beállításai

Általánosságban véve a közzétevő és a terjesztőn kell lennie a felhőben vagy a helyszínen. A következő konfigurációk támogatottak:

- **A felügyelt példány helyi terjesztő közzétevő**

   ![Replication-with-Azure-SQL-DB-Single-Managed-instance-Publisher-distributor](./media/replication-with-sql-database-managed-instance/01-single-instance-asdbmi-pubdist.png)

   Gyártó és forgalmazó adatbázisok egyetlen felügyelt példány konfigurálása.

- **A felügyelt példány a távoli terjesztőt közzétevő**

   ![Replication-with-Azure-SQL-DB-separate-Managed-Instances-Publisher-distributor](./media/replication-with-sql-database-managed-instance/02-separate-instances-asdbmi-pubdist.png)

   Gyártó és forgalmazó két felügyelt példány konfigurálása. Ebben a konfigurációban:

  - Felügyelt példány is vannak egyazon virtuális hálózaton.

  - Felügyelt példány is ugyanazon a helyen vannak.

- **Gyártó és forgalmazó a helyszínen az előfizető a felügyelt példány**

   ![Replication-from-on-premises-to-Azure-SQL-DB-Subscriber](./media/replication-with-sql-database-managed-instance/03-azure-sql-db-subscriber.png)

   Ebben a konfigurációban az Azure SQL database az előfizető. Ez a konfiguráció támogatja az áttelepítés a helyszínről az Azure-bA. Az előfizető szerepkör az SQL database nem igényel felügyelt példány, azonban, hogy a lépést az áttelepítés a helyszínről az Azure-ban is használhat egy SQL Database felügyelt példányain. Azure SQL Database-előfizetői kapcsolatos további információkért lásd: [SQL-adatbázis-replikáció](replication-to-sql-database.md).

## <a name="requirements"></a>Követelmények

Közzétevő és az Azure SQL Database terjesztő szükségesek:

- Az Azure SQL Database felügyelt példányain.

   >[!NOTE]
   >Az Azure SQL Database felügyelt példánya nem konfigurált előfizetők csak lehet.

- Az SQL Server összes példányát kell az azonos virtuális hálózaton.

- Kapcsolat SQL-hitelesítést használ a replikációs résztvevők között.

- Az Azure Storage-fiók megosztások a replikációs munkakönyvtárhoz.

## <a name="features"></a>Szolgáltatások

A következőket támogatja:

- A helyszíni és Azure SQL Database felügyelt példányába példányok tranzakciós és pillanatkép-replikációs vegyesen.

- Előfizetők lehetnek, a helyszínen, az Azure SQL Database önálló adatbázisok vagy készletezett adatbázisok az Azure SQL Database rugalmas készletei.

- Egyirányú és kétirányú replikációt

## <a name="configure-publishing-and-distribution-example"></a>Közzététel és terjesztés példa konfigurálása

1. [Hozzon létre egy Azure SQL Database felügyelt példányába](http://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-create-tutorial-portal) a portálon.

1. [Hozzon létre egy Azure Storage-fiók](http://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) a munkakönyvtárhoz. Győződjön meg arról, a tárelérési kulcsok másolása. Lásd: [megtekintése és másolása tárelérési kulcsok](http://docs.microsoft.com/azure/storage/common/storage-create-storage-account#manage-your-storage-access-keys).

1. Hozzon létre egy adatbázist a közzétevőre vonatkozóan.

   Cserélje le az alábbi parancsfájlpéldákat `<Publishing_DB>` e adatbázis nevére.

1. Hozzon létre egy adatbázis-felhasználót a terjesztő SQL-hitelesítés. Látható, [adatbázis-felhasználók létrehozása](http://docs.microsoft.com/azure/sql-database/sql-database-security-tutorial#creating-database-users). Használjon biztonságos jelszót.

   Használja az alábbi parancsfájlpéldákat `<SQL_USER>` és `<PASSWORD>` az SQL Server-fiók adatbázis-felhasználót és a jelszót.

1. [Csatlakozás az SQL Database felügyelt példány](http://docs.microsoft.com/azure/sql-database/sql-database-connect-query-ssms).

1. Futtassa a következő lekérdezést a terjesztőn és a terjesztési adatbázis hozzáadása.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

1. Megadott terjesztési adatbázis közzétevő konfigurálása, frissítése és a feloldását kérte lekérdezés futtatásához.

   Cserélje le `<SQL_USER>` és `<PASSWORD>` az SQL Server-fiók és jelszó.

   Cserélje le `\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>` a következő értékkel: a tárfiók.  

   Cserélje le `<STORAGE_CONNECTION_STRING>` a kapcsolati karakterláncra az **hozzáférési kulcsok** a Microsoft Azure storage-fiók fülre.

   Miután frissítette a következő lekérdezést, futtassa. 

   ```sql
   USE [master]
   EXEC sp_adddistpublisher @publisher = @@ServerName,
                @distribution_db = N'distribution',
                @security_mode = 0,
                @login = N'<SQL_USER>',
                @password = N'<PASSWORD>',
                @working_directory = N'\\<STORAGE_ACCOUNT>.file.core.windows.net\<SHARE>',
                @storage_connection_string = N'<STORAGE_CONNECTION_STRING>';
   GO
   ```

1. Konfigurálja a replikáció közzétevő. 

    Cserélje le a következő lekérdezést, `<Publishing_DB>` a közzétevői adatbázis nevével.

    Cserélje le `<Publication_Name>` a kiadvány-névvel.

    Cserélje le `<SQL_USER>` és `<PASSWORD>` az SQL Server-fiók és jelszó.

    Miután frissítette a lekérdezést, futtassa, létrehozni a kiadványt.

   ```sql
   USE [<Publishing_DB>]
   EXEC sp_replicationdboption @dbname = N'<Publishing_DB>',
                @optname = N'publish',
                @value = N'true';

   EXEC sp_addpublication @publication = N'<Publication_Name>',
                @status = N'active';

   EXEC sp_changelogreader_agent @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>';

   EXEC sp_addpublication_snapshot @publication = N'<Publication_Name>',
                @frequency_type = 1,
                @publisher_security_mode = 0,
                @publisher_login = N'<SQL_USER>',
                @publisher_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>',
                @job_password = N'<PASSWORD>'
   ```

1. Adja hozzá a cikkben, az előfizetés és a leküldéses előfizetési ügynök. 

   Adja hozzá ezeket az objektumokat, frissítse az alábbi parancsfájlt.

   Cserélje le `<Object_Name>` a kiadvány-objektum nevével.

   Cserélje le `<Object_Schema>` a forrás sémájának nevét. 

   Cserélje le a csúcsos zárójelpárban van a többi paraméter `<>` azokra az értékekre a korábbi parancsfájlok. 

   ```sql
   EXEC sp_addarticle @publication = N'<Publication_Name>',
                @type = N'logbased',
                @article = N'<Object_Name>',
                @source_object = N'<Object_Name>',
                @source_owner = N'<Object_Schema>'

   EXEC sp_addsubscription @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @destination_db = N'<Subscribing_DB>',
                @subscription_type = N'Push'

   EXEC sp_addpushsubscription_agent @publication = N'<Publication_Name>',
                @subscriber = @@ServerName,
                @subscriber_db = N'<Subscribing_DB>',
                @subscriber_security_mode = 0,
                @subscriber_login = N'<SQL_USER>',
                @subscriber_password = N'<PASSWORD>',
                @job_login = N'<SQL_USER>', 
                @job_password = N'<PASSWORD>'
   GO
   ```

## <a name="limitations"></a>Korlátozások

A következő funkciók nem támogatottak:

- Frissíthető előfizetések

- Aktív georeplikáció

## <a name="see-also"></a>Lásd még:

- [Mi a felügyelt példány?](http://docs.microsoft.com/azure/sql-database/sql-database-managed-instance)

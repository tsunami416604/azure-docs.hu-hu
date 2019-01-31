---
title: A replikáció konfigurálása az Azure SQL Database felügyelt példányába |} A Microsoft Docs
description: További információ az Azure SQL Database felügyelt példánya a tranzakciós replikáció konfigurálása
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: allenwux
ms.author: xiwu
ms.reviewer: mathoma
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: b0188a0983ea18490f3997b857386e313daa58ed
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55467663"
---
# <a name="configure-replication-in-azure-sql-database-managed-instance"></a>A replikáció konfigurálása az Azure SQL Database felügyelt példánya

Tranzakciós replikáció lehetővé teszi, az SQL Server vagy az Azure SQL Database felügyelt példányába adatbázisok másolatát a felügyelt példányon replikálni, vagy a felügyelt példány más SQL Server, az önálló SQL Database-adatbázist az adatbázisok végrehajtott módosítások leküldése vagy rugalmas készlet, vagy más felügyelt példány. Replikáció a nyilvános előzetes verzióban van, a [Azure SQL Database felügyelt példányába](sql-database-managed-instance.md). Felügyelt példány közzétevő terjesztője és előfizető adatbázisok is üzemeltethet. Lásd: [tranzakciós replikáció konfigurációk](sql-database-managed-instance-transactional-replication.md#common-configurations) elérhető konfigurációk esetén.

## <a name="requirements"></a>Követelmények

Közzétevő és az Azure SQL Database terjesztő szükségesek:

- Az Azure SQL Database felügyelt példánya, amely nem szerepel a Geo-DR-konfigurációt.

   >[!NOTE]
   >Az Azure SQL Database felügyelt példánya nem konfigurált előfizetők csak lehet.

- Az SQL Server összes példányát kell az azonos virtuális hálózaton.

- Kapcsolat SQL-hitelesítést használ a replikációs résztvevők között.

- Az Azure Storage-fiók megosztások a replikációs munkakönyvtárhoz.

- 445-ös (TCP, kimenő) meg kell kell megnyitni a biztonsági szabályok az Azure-fájlmegosztás eléréséhez a felügyelt példány alhálózatára

## <a name="features"></a>Szolgáltatások

A következőket támogatja:

- A helyszíni és Azure SQL Database felügyelt példányába példányok tranzakciós és pillanatkép-replikációs vegyesen.

- Előfizetők lehet a helyszíni, önálló adatbázis az Azure SQL Database vagy Azure SQL Database rugalmas készletei a készletezett adatbázisok.

- Egyirányú és kétirányú replikációt.

A következő funkciók nem támogatottak:

- Frissíthető előfizetések.

- Aktív georeplikáció.

## <a name="configure-publishing-and-distribution-example"></a>Közzététel és terjesztés példa konfigurálása

1. [Hozzon létre egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-create-tutorial-portal.md) a portálon.
2. [Hozzon létre egy Azure Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) a munkakönyvtárhoz.

   Győződjön meg arról, a tárelérési kulcsok másolása. Lásd: [megtekintése és másolása tárelérési kulcsok](../storage/common/storage-account-manage.md#access-keys
).
3. Hozzon létre egy adatbázist a közzétevőre vonatkozóan.

   Cserélje le az alábbi parancsfájlpéldákat `<Publishing_DB>` e adatbázis nevére.

4. Hozzon létre egy adatbázis-felhasználót a terjesztő SQL-hitelesítés. Használjon biztonságos jelszót.

   Használja az alábbi parancsfájlpéldákat `<SQL_USER>` és `<PASSWORD>` az SQL Server-fiók adatbázis-felhasználót és a jelszót.

5. [Csatlakozás az SQL Database felügyelt példány](sql-database-connect-query-ssms.md).

6. Futtassa a következő lekérdezést a terjesztőn és a terjesztési adatbázis hozzáadása.

   ```sql
   USE [master]
   GO
   EXEC sp_adddistributor @distributor = @@ServerName;
   EXEC sp_adddistributiondb @database = N'distribution';
   ```

7. Egy megadott terjesztési adatbázis közzétevő konfigurálása, frissítése és a következő lekérdezés futtatásával.

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

8. Konfigurálja a replikáció közzétevő.

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

9. Adja hozzá a cikkben, az előfizetés és a leküldéses előfizetési ügynök.

   Adja hozzá ezeket az objektumokat, frissítse az alábbi parancsfájlt.

   - Cserélje le `<Object_Name>` a kiadvány-objektum nevével.
   - Cserélje le `<Object_Schema>` a forrás sémájának nevét.
   - Cserélje le a csúcsos zárójelpárban van a többi paraméter `<>` azokra az értékekre a korábbi parancsfájlok.

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
   
## <a name="see-also"></a>Lásd még:

- [Tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md)
- [Mi a felügyelt példány?](sql-database-managed-instance.md)

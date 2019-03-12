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
ms.openlocfilehash: 164448f2e96b796d21419e90a3965390f22d7d38
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762976"
---
# <a name="configure-replication-in-an-azure-sql-database-managed-instance-database"></a>Replikálás konfigurálása a egy Azure SQL Database felügyelt példány adatbázisa

Tranzakciós replikáció lehetővé teszi adatok replikálása egy Azure SQL Database felügyelt példány adatbázisa SQL Server-adatbázis vagy egy másik példány adatbázisa be. Tranzakciós replikáció használatával az Azure SQL Database felügyelt példány az SQL Server-adatbázis, az önálló adatbázis az Azure SQL Database, az Azure SQL Database rugalmas készlet készletezett adatbázis-példány adatbázisban végrehajtott módosításokat. Tranzakciós replikáció a nyilvános előzetes verzióban van, a [Azure SQL Database felügyelt példány](sql-database-managed-instance.md). Felügyelt példány közzétevő terjesztője és előfizető adatbázisok is üzemeltethet. Lásd: [tranzakciós replikáció konfigurációk](sql-database-managed-instance-transactional-replication.md#common-configurations) elérhető konfigurációk esetén.

## <a name="requirements"></a>Követelmények

A felügyelt példány működhet a közzétevő vagy forgalmazó konfigurálása szükséges:

- Hogy a felügyelt példány nem jelenleg vesz részt a georeplikációs kapcsolatban.

   >[!NOTE]
   >Önálló adatbázisok és a készletezett adatbázisok Azure SQL Database-ben csak előfizetők lehetnek.

- Az összes felügyelt példányok ugyanabban a virtuális hálózatban kell lennie.

- Kapcsolat SQL-hitelesítést használ a replikációs résztvevők között.

- Az Azure Storage-fiók megosztások a replikációs munkakönyvtárhoz.

- 445-ös (TCP, kimenő) meg kell kell megnyitni a biztonsági szabályok az Azure-fájlmegosztás eléréséhez a felügyelt példány alhálózatára

## <a name="features"></a>Szolgáltatások

A következőket támogatja:

- A helyszíni SQL Server és az Azure SQL Database felügyelt példány tranzakciós és pillanatkép-replikációs arányát.
- A helyszíni SQL Server-adatbázisok, az Azure SQL Database önálló adatbázisok vagy készletezett adatbázisok az Azure SQL Database rugalmas készletei előfizetőket is lehet.
- Egyirányú és kétirányú replikációt.

A következő funkciók nem támogatottak az Azure SQL Database felügyelt példány:

- Frissíthető előfizetések.
- Aktív georeplikáció.

## <a name="configure-publishing-and-distribution-example"></a>Közzététel és terjesztés példa konfigurálása

1. [Hozzon létre egy Azure SQL Database felügyelt példány](sql-database-managed-instance-create-tutorial-portal.md) a portálon.
2. [Hozzon létre egy Azure Storage-fiók](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account#create-a-storage-account) a munkakönyvtárhoz.

   Győződjön meg arról, a tárelérési kulcsok másolása. Lásd: [megtekintése és másolása tárelérési kulcsok](../storage/common/storage-account-manage.md#access-keys
).
3. A közzétevő-példány adatbázis létrehozása.

   Cserélje le az alábbi parancsfájlpéldákat `<Publishing_DB>` a példány adatbázis nevével.

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

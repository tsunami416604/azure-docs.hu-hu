---
title: Az első Azure SQL-adatbázis megtervezése SSMS használatával | Microsoft Docs
description: Ismerje meg, hogyan tervezheti meg első Azure SQL-adatbázisát az SQL Server Management Studióval.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: mvc,develop databases
ms.topic: tutorial
ms.date: 04/04/2018
ms.author: carlrab
ms.openlocfilehash: b09807e1fa7624ed134a24c7df4a61abf2a1d723
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="design-your-first-azure-sql-database-using-ssms"></a>Az első Azure SQL-adatbázis megtervezése SSMS használatával

Az Azure SQL Database a Microsoft Cloudon (Azure) alapuló, szolgáltatásként nyújtott relációs adatbázis (DBaaS). Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket az Azure Portal és az [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) használatával: 

> [!div class="checklist"]
> * Adatbázis létrehozása az Azure Portalon
> * Kiszolgálószintű tűzfalszabály létrehozása az Azure Portalon
> * Kapcsolódás az adatbázishoz SSMS segítségével
> * Táblázatok létrehozása az SSMS használatával
> * Adatok kötegelt betöltése a BCP használatával
> * Lekérdezés végrehajtása az adatokon az SSMS használatával
> * Adatbázis egy korábbi [időponthoz kötött visszaállítása](sql-database-recovery-using-backups.md#point-in-time-restore) az Azure Portalon

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy telepítette a következőket:
- Az [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) legújabb verziója.
- A [BCP és SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) legújabb verziója.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Üres SQL-adatbázis létrehozása

Az Azure SQL-adatbázis [számítási és tárolási erőforrások](sql-database-service-tiers.md) egy meghatározott készletével együtt jön létre. Az adatbázis egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül egy [Azure SQL Database logikai kiszolgálón](sql-database-features.md) jön létre. 

Kövesse az alábbi lépéseket egy üres SQL-adatbázis létrehozásához. 

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

2. Az **Új** oldalon válassza az **Adatbázisok**, majd az **Új** oldal **SQL Database** területén a **Létrehozás** lehetőséget.

   ![üres adatbázis létrehozása](./media/sql-database-design-first-database/create-empty-database.png)

3. Töltse ki az SQL Database űrlapját a következő információkkal az előző képen látható módon:   

   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Adatbázis neve** | mySampleDatabase | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) ismertető cikket. | 
   | **Előfizetés** | Az Ön előfizetése  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   | **Forrás kiválasztása** | Üres adatbázis | Meghatározza, hogy egy üres adatbázist kell létrehozni. |

4. Kattintson a **Kiszolgáló** lehetőségre új kiszolgáló létrehozásához és konfigurálásához az új adatbázis számára. Adja meg az alábbi adatokat az **Új kiszolgálóűrlapon**: 

   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Kiszolgálónév** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. | 
   | **Kiszolgálói rendszergazdai bejelentkezés** | Bármely érvényes név | Az érvényes bejelentkezési nevekkel kapcsolatban lásd az [adatbázis-azonosítókat](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) ismertető cikket.|
   | **Jelszó** | Bármely érvényes jelszó | A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: nagybetűs karakterek, kisbetűs karakterek, számjegyek és nem alfanumerikus karakterek. |
   | **Hely** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

   ![adatbázis-kiszolgáló létrehozása](./media/sql-database-design-first-database/create-database-server.png)

5. Kattintson a **Kiválasztás** gombra.

6. Kattintson a **Tarifacsomag** parancsra a szolgáltatásszint, a DTU-k vagy virtuális magok száma és a tárterületméret megadásához. Fedezze fel a DTU-k/virtuális magok számára és a tárterületre vonatkozó, egyes szolgáltatásszinteken elérhető lehetőségeket. 

7. Ebben az oktatóanyagban válassza a **Standard** szolgáltatásszintet, majd a csúszkával állítson be **100 DTU (S3)** egységet, illetve **400** GB tárhelyet.

   ![adatbázis létrehozása-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. A **Kiegészítő tárterület** beállítás használatához el kell fogadnia az előzetes verziójú szolgáltatás feltételeit. 

   > [!IMPORTANT]
   > \* A szolgáltatási keretbe foglaltnál nagyobb tárterületek előzetes verzióban érhetők el, és extra költségek vonatkoznak rájuk. Részletes információ: [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* Az 1 TB tárhelyméretet meghaladó prémium szintű készletek jelenleg a következő régiókban érhetők el: Kelet-Ausztrália, Délkelet-Ausztrália, Dél-Brazília, Közép-Kanada, Kelet-Kanada, USA középső régiója, Közép-Franciaország, Közép-Németország, Kelet-Japán, Nyugat-Japán, Korea középső régiója, USA északi középső régiója, Észak-Európa, USA déli középső régiója, Délkelet-Ázsia, az Egyesült Királyság déli régiója, az Egyesült Királyság nyugati régiója, USA keleti régiója 2, USA nyugati régiója, USA-beli államigazgatás – Virginia, és Nyugat-Európa. Lásd: [P11–P15 – Aktuális korlátozások](sql-database-dtu-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. A kiszolgálószint, a DTU-szám és a tárterületméret kiválasztása után kattintson az **Alkalmaz** gombra.  

10. Válasszon **rendezést** az üres adatbázis számára (ebben az oktatóanyagban használja az alapértelmezett értéket). A rendezésekkel kapcsolatos további információkért lásd: [Rendezések](https://docs.microsoft.com/sql/t-sql/statements/collations)

11. Most, hogy kitöltötte az SQL Database űrlapját, kattintson a **Létrehozás** gombra az adatbázis létrehozásához. Az üzembe helyezés eltarthat néhány percig. 

12. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.
    
     ![értesítés](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

Az SQL Database szolgáltatás egy tűzfalat hoz létre a kiszolgáló szintjén, amely megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha létrehoz tűzfalszabályt, hogy adott IP-címek számára megnyissa a tűzfalat. A következő lépésekkel hozzon létre egy [kiszolgálószintű SQL Database-tűzfalszabályt](sql-database-firewall-configure.md) az ügyfél IP-címéhez, és engedélyezze a külső kapcsolatokat csak az Ön IP-címéhez az SQL Database-tűzfalon keresztül. 

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot.
>

1. Az üzembe helyezés befejezése után kattintson az **SQL-adatbázisok** elemre a bal oldali menüben, majd kattintson a **mySampleDatabase** adatbázisra az **SQL-adatbázisok** lapon. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes kiszolgálónév (például: **mynewserver-20170824.database.windows.net**), valamint a további konfigurálható beállítások. 

2. Másolja ezt a teljes kiszolgálónevet, mert a későbbi rövid útmutatók során szüksége lesz rá a kiszolgálóhoz és az adatbázisokhoz való csatlakozáshoz. 

   ![kiszolgáló neve](./media/sql-database-get-started-portal/server-name.png) 

3. Kattintson a **Kiszolgálótűzfal beállítása** lehetőségre az eszköztáron. Megnyílik az SQL Database kiszolgálóhoz tartozó **Tűzfalbeállítások** oldal. 

   ![kiszolgálói tűzfalszabály](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Az eszköztár **Ügyfél IP-címének hozzáadása** elemére kattintva vegye fel aktuális IP-címét egy új tűzfalszabályba. A tűzfalszabály az 1433-as portot egy egyedi IP-cím vagy egy IP-címtartomány számára nyithatja meg.

5. Kattintson a **Save** (Mentés) gombra. A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez, és megnyitja az 1433-as portot a logikai kiszolgálón.

6. Kattintson az **OK** gombra, majd zárja be a **Tűzfalbeállítások** lapot.

Mostantól csatlakozhat az SQL Database-kiszolgálóhoz és annak adatbázisaihoz erről az IP-címről az SQL Server Management Studióval vagy más választott eszközzel, az előzőekben létrehozott kiszolgálói rendszergazdai fiókkal.

> [!IMPORTANT]
> Alapértelmezés szerint az összes Azure-szolgáltatás számára engedélyezett a hozzáférés az SQL Database tűzfalán keresztül. Kattintson a **KI** gombra ezen az oldalon az összes Azure-szolgáltatás hozzáférésének letiltásához.

## <a name="sql-server-connection-information"></a>Az SQL-kiszolgáló kapcsolatadatai

Kérje le az Azure SQL Database kiszolgáló teljes kiszolgálónevét az Azure Portalon. Használja a teljes kiszolgálónevet az SQL Server Management Studióban a kiszolgálóhoz történő csatlakozáshoz.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az Azure Portalon az adatbázishoz tartozó lap **Alapvető erőforrások** ablaktábláján keresse meg, majd másolja ki a **Kiszolgáló nevét**.

   ![kapcsolatadatok](./media/sql-database-get-started-portal/server-name.png)

## <a name="connect-to-the-database-with-ssms"></a>Kapcsolódás az adatbázishoz SSMS segítségével

Az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) segítségével hozzon létre kapcsolatot az Azure SQL Database-kiszolgálóval.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Kiszolgáló típusa | Adatbázismotor | Kötelezően megadandó érték |
   | Kiszolgálónév | A teljes kiszolgálónév | A névnek a következőhöz hasonlónak kell lennie: **mynewserver20170824.database.windows.net**. |
   | Hitelesítés | SQL Server-hitelesítés | Az SQL-hitelesítés az egyetlen hitelesítési típus, amelyet ebben az oktatóanyagban konfiguráltunk. |
   | Bejelentkezés | A kiszolgálói rendszergazdai fiók | Ez az a fiók, amely a kiszolgáló létrehozásakor lett megadva. |
   | Jelszó | A kiszolgálói rendszergazdai fiók jelszava | Ezt a jelszót adta meg a kiszolgáló létrehozásakor. |

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-connect-query-ssms/connect.png)

3. A **Connect to server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen kattintson az **Options** (Beállítások) elemre. A **Connect to database** (Csatlakozás az adatbázishoz) szakaszban adja meg a következőt: **mySampleDatabase** az adatbázishoz való csatlakozáshoz.

   ![csatlakozás kiszolgálón található adatbázishoz](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Kattintson a **Connect** (Csatlakozás) gombra. Megnyílik az Object Explorer ablak az SSMS-ben. 

5. Az Object Explorerben bontsa ki a **Database** (Adatbázisok), majd a **mySampleDatabese** csomópontot a mintaadatbázisban található objektumok megtekintéséhez.

   ![adatbázis-objektumok](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>Táblák létrehozása az adatbázisban 

A [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) használatával hozzon létre egy adatbázis-sémát négy táblázattal, amelyek az egyetemek hallgatókezelési rendszerét modellezik:

- Személy
- Kurzus
- Hallgató
- Kredit

Az alábbi ábrán látható, hogyan kapcsolódnak ezek a táblázatok egymáshoz. Ezen táblázatok némelyike más táblák oszlopaira hivatkozik. Például a Hallgató tábla a **PersonId** oszlopra hivatkozik, amely a **Személy** táblában található. Tanulmányozza a diagramot az ebben az oktatóanyagban szereplő táblák kapcsolatainak megértéséhez. A hatékony adatbázistáblák létrehozásának részleteiért lásd: [Hatékony adatbázistáblák létrehozása](https://msdn.microsoft.com/library/cc505842.aspx). Az adattípusok kiválasztására vonatkozó további információkért lásd: [Adattípusok](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Használhatja az [SQL Server Management Studio táblatervezőjét](https://msdn.microsoft.com/library/hh272695.aspx) is a táblák létrehozásához és tervezéséhez. 

![Táblák közötti kapcsolatok](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. Az Object Explorerben kattintson a jobb gombbal a **mySampleDatabase** adatbázisra, majd kattintson a **New Query** (Új lekérdezés) elemre. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.

2. A lekérdezési ablakban hozzon létre négy táblát az adatbázisban a következő lekérdezés futtatásával: 

   ```sql 
   -- Create Person table

   CREATE TABLE Person
   (
   PersonId   INT IDENTITY PRIMARY KEY,
   FirstName   NVARCHAR(128) NOT NULL,
   MiddelInitial NVARCHAR(10),
   LastName   NVARCHAR(128) NOT NULL,
   DateOfBirth   DATE NOT NULL
   )
   
   -- Create Student table
 
   CREATE TABLE Student
   (
   StudentId INT IDENTITY PRIMARY KEY,
   PersonId  INT REFERENCES Person (PersonId),
   Email   NVARCHAR(256)
   )
   
   -- Create Course table
 
   CREATE TABLE Course
   (
   CourseId  INT IDENTITY PRIMARY KEY,
   Name   NVARCHAR(50) NOT NULL,
   Teacher   NVARCHAR(256) NOT NULL
   ) 

   -- Create Credit table
 
   CREATE TABLE Credit
   (
   StudentId   INT REFERENCES Student (StudentId),
   CourseId   INT REFERENCES Course (CourseId),
   Grade   DECIMAL(5,2) CHECK (Grade <= 100.00),
   Attempt   TINYINT,
   CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
   (
   StudentId, CourseId, Grade, Attempt
   )
   )
   ```

   ![Táblázatok létrehozása](./media/sql-database-design-first-database/create-tables.png)

3. Bontsa ki a „táblák” csomópontot az SQL Server Management Studio Object Explorer eszközében a létrehozott táblák megtekintéséhez.

   ![létrehozott ssms-táblák](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba

1. Hozzon létre egy **SampleTableData** nevű mappát a Letöltések mappában az adatbázisba szánt mintaadatok tárolásához. 

2. Kattintson a jobb gombbal az alábbi hivatkozásokra, és mentse őket a **SampleTableData** mappába. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Nyisson meg egy parancssori ablakot, és lépjen a SampleTableData mappára.

4. Futtassa a következő parancsokat a mintaadatok táblákba való beszúrásához, a környezetének megfelelő értékekre cserélve a **ServerName** (kiszolgálónév), **DatabaseName** (adatbázisnév), **UserName** (felhasználónév) és **Password** (jelszó) értékeit.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

Ezzel mintaadatokat töltött be a korábban létrehozott táblákba.

## <a name="query-data"></a>Adatok lekérdezése

Hajtsa végre a következő lekérdezéseket az adatbázistáblákban lévő információk lekéréséhez. Az SQL-lekérdezések írására vonatkozó további információkért lásd: [SQL-lekérdezések írása](https://technet.microsoft.com/library/bb264565.aspx). Az első lekérdezés mind a négy tábla összekapcsolásával keresi meg az összes olyan, „Dominick Pope” által tanított hallgatót, aki 75%-nál jobb osztályzatot ért el az adott kurzuson. A második lekérdezés mind a négy tábla összekapcsolásával keresi meg az összes olyan kurzust, amelyekre „Noe Coleman” valaha feliratkozott.

1. Az SQL Server Management Studio lekérdezési ablakában hajtsa végre a következő lekérdezést:

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

   SELECT  person.FirstName,
   person.LastName,
   course.Name,
   credit.Grade
   FROM  Person AS person
   INNER JOIN Student AS student ON person.PersonId = student.PersonId
   INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
   INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope' 
   AND Grade > 75
   ```

2. Az SQL Server Management Studio lekérdezési ablakában hajtsa végre a következő lekérdezést:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

   SELECT  course.Name,
   course.Teacher,
   credit.Grade
   FROM  Course AS course
   INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
   INNER JOIN Student AS student ON student.StudentId = credit.StudentId
   INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
   AND person.LastName = 'Coleman'
   ```

## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra

Tegyük fel, hogy véletlenül törölt egy táblát. Ez nem olyasvalami, ami könnyen helyreállítható. Az Azure SQL Database segítségével az elmúlt 35 nap bármelyik pillanatára vissza lehet térni, és ezt az időpontot egy új adatbázisra vissza lehet állítani. Ezzel az adatbázissal visszaállíthatók a törölt adatok. Az alábbi lépések a mintaadatbázist a táblák hozzáadása előtti időpontra állítják vissza.

1. Az adatbázishoz tartozó SQL Database oldalán kattintson az eszköztár **Visszaállítás** elemére. Megnyílik a **Visszaállítás** oldal.

   ![visszaállítás](./media/sql-database-design-first-database/restore.png)

2. Töltse ki a **Visszaállítás** űrlapot a szükséges információkkal:
    * Adatbázis neve: Adjon meg egy adatbázisnevet 
    * Időponthoz kötött: Válassza az **Időponthoz kötött** fület a Visszaállítás űrlapon 
    * Visszaállítási pont: Válasszon ki egy, az adatbázis módosítása előtti időpontot
    * Célkiszolgáló: Ez az érték adatbázis visszaállításakor nem módosítható 
    * Rugalmas adatbáziskészlet: Válassza a **Nincs** lehetőséget  
    * Tarifacsomag: Válassza a **20 DTU** egységet, illetve a **40 GB** tárhelyet.

   ![visszaállítási pont](./media/sql-database-design-first-database/restore-point.png)

3. Kattintson az **OK** gombra az adatbázis a táblák hozzáadása előtti [időpontra](sql-database-recovery-using-backups.md#point-in-time-restore) való visszaállításához. Az adatbázis egy másik időpontra való visszaállítása a megadott időpontnak megfelelő duplikált adatbázist hoz létre ugyanazon a kiszolgálón, amelyen az eredeti adatbázis található, amennyiben az adott pont a [szolgáltatásszint](sql-database-service-tiers.md) adatmegőrzési időszakán belül esik.

## <a name="next-steps"></a>További lépések 
Ebben az oktatóanyagban megismerte az alapvető adatbázis-feladatokat, mint például az adatbázisok és táblák létrehozását, az adatok betöltését és lekérdezését, valamint az adatbázisok korábbi időpontra való visszaállítását. Megismerte, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Adatbázis létrehozása
> * Tűzfalszabály beállítása
> * Kapcsolódás az adatbázishoz [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) használatával
> * Táblázatok létrehozása
> * Adatok kötegelt betöltése
> * Az adatok lekérdezése
> * Adatbázis visszaállítása egy korábbi időpontra az SQL Database [időponthoz kötött visszaállítási](sql-database-recovery-using-backups.md#point-in-time-restore) képességeinek használatával

Folytassa a következő oktatóanyaggal, amely az adatbázisok Visual Studio és C# használatával történő tervezését ismerteti.

> [!div class="nextstepaction"]
>[Azure SQL-adatbázis megtervezése, és kapcsolódás C# és ADO.NET használatával](sql-database-design-first-database-csharp.md)

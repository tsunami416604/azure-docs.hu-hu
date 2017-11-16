---
title: "Az első Azure SQL-adatbázis megtervezése |} Microsoft Docs"
description: "Ismerje meg, az első Azure SQL-adatbázis az Azure portálon, és az SQL Server Management Studio tervezéséhez."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop databases
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 08/25/2017
ms.author: carlrab
ms.openlocfilehash: 329003c7c4abe89f4af04473ee3664605b2ea81f
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="design-your-first-azure-sql-database"></a>Az első Azure SQL-adatbázis megtervezése

Az Azure SQL-adatbázis egy relációs adatbázis-mint – a szolgáltatás, a Microsoft Cloud (Azure) a rendszer. Ebben az oktatóanyagban elsajátíthatja, hogyan használhatja az Azure portált és [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) számára: 

> [!div class="checklist"]
> * Adatbázis létrehozása az Azure-portálon
> * Állítson be egy kiszolgálószintű tűzfalszabályt az Azure-portálon
> * Kapcsolódni az adatbázishoz ssms alkalmazásával
> * Táblázatok létrehozása az ssms alkalmazásával
> * A tömeges adatok betöltése a BCP-vel
> * Lekérdezés ssms alkalmazásával adatok
> * Állítsa vissza az adatbázist egy előző [időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore) az Azure-portálon

Ha nem rendelkezik Azure-előfizetéssel, [ingyenes fiók létrehozását](https://azure.microsoft.com/free/) megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez, győződjön meg arról, hogy telepítette:
- A legújabb verziójának [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS).
- A legújabb verziójának [BCP és SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433).

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Üres SQL-adatbázis létrehozása

Az Azure SQL-adatbázis [számítási és tárolási erőforrások](sql-database-service-tiers.md) egy meghatározott készletével együtt jön létre. Az adatbázis egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül egy [Azure SQL Database logikai kiszolgálón](sql-database-features.md) jön létre. 

Kövesse az alábbi lépéseket egy üres SQL-adatbázis létrehozásához. 

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Az **Új** oldalon válassza az **Adatbázisok** lehetőséget, majd az **Új** oldal **SQL Database** területén válassza a **Létrehozás** lehetőséget.

   ![Üres-adatbázis létrehozása](./media/sql-database-design-first-database/create-empty-database.png)

3. Töltse ki az SQL Database űrlapját a következő információkkal az előző képen látható módon:   

   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Adatbázis neve** | mySampleDatabase | Az érvényes adatbázisnevekkel kapcsolatban lásd az [adatbázis-azonosítókat](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) ismertető cikket. | 
   | **Előfizetés** | Az Ön előfizetése  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | myResourceGroup | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   | **Forrás kiválasztása** | Az üres adatbázis | Meghatározza, hogy egy üres adatbázist kell létrehozni. |

4. Kattintson a **Kiszolgáló** lehetőségre új kiszolgáló létrehozásához és konfigurálásához az új adatbázis számára. Töltse ki a **új kiszolgáló űrlap** a következő információkat: 

   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Kiszolgálónév** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket. | 
   | **Kiszolgálói rendszergazdai bejelentkezés** | Bármely érvényes név | Az érvényes bejelentkezési nevekkel kapcsolatban lásd az [adatbázis-azonosítókat](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) ismertető cikket.|
   | **Jelszó** | Bármely érvényes jelszó | A jelszó legalább 8 karakterből kell állnia, és az alábbiak közül hármat tartalmaznia kell: nagybetűk, kisbetűk, számok és nem alfanumerikus karakterek száma. |
   | **Hely** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

   ![adatbázis-kiszolgáló létrehozása](./media/sql-database-design-first-database/create-database-server.png)

5. Kattintson a **Kiválasztás** gombra.

6. Kattintson a **Tarifacsomag** parancsra a szolgáltatásszint, a DTU-szám és a tárterületméret megadásához. Megismerkedhet a dtu-inak száma és az Ön számára elérhető egyes száma beállításait. 

7. A jelen oktatóanyag esetében válassza ki a **szabványos** szolgáltatásréteget, és a csúszka segítségével válassza ki a **100 dtu-i (S3)** és **400** GB tárhelyet.

   ![adatbázis létrehozása-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. A **Kiegészítő tárterület** beállítás használatához el kell fogadnia az előzetes verziójú szolgáltatás feltételeit. 

   > [!IMPORTANT]
   > \* A szolgáltatási keretbe foglaltnál nagyobb tárterületek előzetes verzióban érhetők el, és extra költségek vonatkoznak rájuk. Részletes információ: [SQL Database – Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* Az 1 TB tárhelyméretet meghaladó prémium szintű készletek jelenleg a következő régiókban érhetők el: USA 2. keleti régiója, USA nyugati régiója, USA-beli államigazgatás – Virginia, Nyugat-Európa, Közép-Németország, Délkelet-Ázsia, Kelet-Japán, Kelet-Ausztrália, Közép-Kanada és Kelet-Kanada. Lásd: [P11–P15 – Aktuális korlátozások](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. A kiszolgálószint, a DTU-szám és a tárterületméret kiválasztása után kattintson az **Alkalmaz** gombra.  

10. Válassza ki a **rendezés** az üres adatbázis (a jelen oktatóanyag esetében használja az alapértelmezett érték). Rendezések kapcsolatos további információkért lásd: [rendezések](https://docs.microsoft.com/sql/t-sql/statements/collations)

11. Most, hogy kitöltötte az SQL Database űrlapját, kattintson a **Létrehozás** gombra az adatbázis létrehozásához. Az üzembe helyezés eltarthat néhány percig. 

12. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.
    
     ![értesítés](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

Az SQL Database szolgáltatás egy tűzfalat hoz létre a kiszolgáló szintjén, amely megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz, kivéve, ha létrehoz tűzfalszabályt, hogy adott IP-címek számára megnyissa a tűzfalat. A következő lépésekkel hozzon létre egy [kiszolgálószintű SQL Database-tűzfalszabályt](sql-database-firewall-configure.md) az ügyfél IP-címéhez, és engedélyezze a külső kapcsolatokat csak az Ön IP-címéhez az SQL Database-tűzfalon keresztül. 

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ebben az esetben nem tud csatlakozni az Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg nem nyitja meg az 1433-as portot.
>

1. Az üzembe helyezés befejezése után kattintson az **SQL-adatbázisok** elemre a bal oldali menüben, majd kattintson a **mySampleDatabase** adatbázisra az **SQL-adatbázisok** lapon. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes kiszolgálónév (például: **mynewserver-20170824.database.windows.net**), valamint a további konfigurálható beállítások. 

2. Másolja le ezt a teljes kiszolgálónevet, mert a későbbi rövid útmutatók során szüksége lesz rá a kiszolgálóhoz és az adatbázisokhoz való csatlakozáshoz. 

   ![kiszolgáló neve](./media/sql-database-get-started-portal/server-name.png) 

3. Kattintson a **kiszolgáló tűzfalának beállítása** az eszköztáron. Megnyílik az SQL Database kiszolgálóhoz tartozó **Tűzfalbeállítások** oldal. 

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

## <a name="connect-to-the-database-with-ssms"></a>Kapcsolódni az adatbázishoz ssms alkalmazásával

Használjon [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) az Azure SQL Database-kiszolgálóhoz csatlakozni.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Kiszolgáló típusa | Adatbázismotor | Ez értékének megadása kötelező. |
   | Kiszolgálónév | A teljes kiszolgálónév | A névnek kell lennie, például ehhez hasonló: **mynewserver20170824.database.windows.net**. |
   | Authentication | SQL Server-hitelesítés | Az SQL-hitelesítés az egyetlen hitelesítési típus, amelyet ebben az oktatóanyagban konfiguráltunk. |
   | Bejelentkezés | A kiszolgálói rendszergazdai fiók | Ez az a fiók, amely a kiszolgáló létrehozásakor lett megadva. |
   | Jelszó | A kiszolgálói rendszergazdai fiók jelszava | Ezt a jelszót adta meg a kiszolgáló létrehozásakor. |

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-connect-query-ssms/connect.png)

3. A **Connect to server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen kattintson az **Options** (Beállítások) elemre. A **Connect to database** (Csatlakozás az adatbázishoz) szakaszban adja meg a következőt: **mySampleDatabase** az adatbázishoz való csatlakozáshoz.

   ![csatlakozás kiszolgálón található adatbázishoz](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Kattintson a **Connect** (Csatlakozás) gombra. Megnyílik az Object Explorer ablak az SSMS-ben. 

5. Az Object Explorerben bontsa ki a **Database** (Adatbázisok), majd a **mySampleDatabese** csomópontot a mintaadatbázisban található objektumok megtekintéséhez.

   ![Adatbázis-objektumok](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="create-tables-in-the-database"></a>Hozzon létre táblák az adatbázisban 

Hozzon létre egy adatbázis-séma négy-táblázatot, amely a modell használatával felsőoktatási student felügyeleti rendszer [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference):

- Személy
- Tanfolyam
- Tanuló
- A modell felsőoktatási student felügyeleti rendszer jóváírása

Az alábbi ábrán látható, hogyan ezek a táblázatok kapcsolódnak egymáshoz. Ezek a táblázatok némelyike hivatkozhat, más táblák oszlopaira. Például a Student tábla hivatkozik a **PersonId** oszlopa a **személy** tábla. A diagram megértése, hogyan az ebben az oktatóanyagban kapcsolódó egy másik elemzésére. Egy hatékony adatbázistáblák létrehozásáról részletes pillantást, lásd: [hatékony adatbázistáblák létrehozása](https://msdn.microsoft.com/library/cc505842.aspx). Adattípusok kiválasztására vonatkozó további információkért lásd: [adattípusok](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Használhatja a [tábla designer az SQL Server Management Studio](https://msdn.microsoft.com/library/hh272695.aspx) létrehozásához, és a táblák tervezéséhez. 

![Tábla kapcsolatokat](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. Az Object Explorerben kattintson a jobb gombbal a **mySampleDatabase** adatbázisra, majd kattintson a **New Query** (Új lekérdezés) elemre. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.

2. A lekérdezési ablakban hajtható végre a következő lekérdezés futtatásával hozzon létre négy táblák az adatbázisban: 

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

3. Bontsa ki a "tábla" csomópontot az SQL Server Management Studio objektum explorer létrehozott tábláinak megtekintéséhez.

   ![az ssms táblák létrehozása](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba

1. Hozzon létre egy nevű **SampleTableData** a Letöltések mappába, az adatbázis mintaadatok tárolásához. 

2. Kattintson a jobb gombbal az alábbi hivatkozások, és mentse őket a **SampleTableData** mappa. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Nyisson meg egy parancssori ablakot, és keresse meg a SampleTableData mappát.

4. A következő parancsok futtatásával mintaadatok beilleszteni a táblázatok értékeit cseréje **kiszolgálónév**, **DatabaseName**, **felhasználónév**, és  **Jelszó** a környezetnek az értékekkel.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

A korábban létrehozott táblázataiba mintaadatok most töltött.

## <a name="query-data"></a>Adatok lekérdezése

Hajtsa végre az alábbi lekérdezéseket a információt lekérni a táblák. Lásd: [SQL-lekérdezések írása](https://technet.microsoft.com/library/bb264565.aspx) tudhat meg többet az SQL-lekérdezések írása. Az első lekérdezés csatlakozik a diákok által "Dominick Pope" tanított egy besorolási 75 %-nál nagyobb rendelkező annak osztályban található összes négy tábla. A második lekérdezés összes négy táblák, és megkeresi, amelyben "Noe Coleman" legalább egyszer regisztrált összes tanfolyamokat.

1. Egy SQL Server Management Studio lekérdezési ablakban hajtható végre a következő lekérdezést:

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

2. Egy SQL Server Management Studio lekérdezési ablakban hajtható végre a következő lekérdezést:

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

Tegyük fel, hogy véletlenül törölt egy tábla. Ez a valami nem egyszerűen állíthat helyre. Az Azure SQL Database teszi lépjen vissza bármely pontra az utolsó legfeljebb 35 nappal időpont, és állítsa vissza ezt a pontot egy új adatbázist szükséges idő. Ez az adatbázis helyreállítása a törölt adatokat van lehetősége. Az alábbi lépéseket a a mintaadatbázis pontra visszaállításához, mielőtt a táblák lettek hozzáadva.

1. Az adatbázis SQL-adatbázis lapján kattintson a **visszaállítása** az eszköztáron. A **visszaállítása** lap megnyitásakor.

   ![Visszaállítás](./media/sql-database-design-first-database/restore.png)

2. Töltse ki a **visszaállítása** a szükséges adatokat az űrlap:
    * Adatbázis neve: Adjon meg egy adatbázisnevet 
    * Időpontban: Válassza ki a **-időpontban** visszaállítási képernyő lapján 
    * Visszaállítási pont: válassza ki a megfelelő, amely az adatbázis módosítása előtt
    * Célkiszolgáló: Ez az érték nem módosítható, ha az adatbázis visszaállítása 
    * A rugalmas adatbáziskészlet: válasszon **nincs**  
    * Tarifacsomag: válasszon **20 Dtu** és **40 GB** tárolási.

   ![helyreállítási pont](./media/sql-database-design-first-database/restore-point.png)

3. Kattintson a **OK** állítsa vissza az adatbázist a [egy időben visszaállítása](sql-database-recovery-using-backups.md#point-in-time-restore) előtt a táblák lettek hozzáadva. Adatbázis visszaállítása egy másik pontra időben adatbázist hoz létre duplikált ugyanarra a kiszolgálóra az eredeti adatbázist a pont frissítésétől időben ad meg, mindaddig, amíg a megőrzési idő belül van a [szolgáltatásréteg](sql-database-service-tiers.md).

## <a name="next-steps"></a>Következő lépések 
Ebben az oktatóanyagban megismerte az alapvető adatbázis-feladatok például egy adatbázis és a táblák létrehozása, betölteni és kérdezhet le adatokat, és állítsa vissza az adatbázist egy korábbi időpontra időben. Megismerte, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Adatbázis létrehozása
> * A tűzfalszabályok beállítása
> * Kapcsolódás az adatbázishoz [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS)
> * Táblázatok létrehozása
> * Tömeges betöltési adatok
> * Adatok lekérdezése
> * Állítsa vissza az adatbázist egy korábbi időpontra, amikor az SQL-adatbázis [időponthoz kötött visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore) képességek

Előzetes tervezése a Visual Studio és a C# egy adatbázist a következő oktatóanyagot.

> [!div class="nextstepaction"]
>[Azure SQL-adatbázis megtervezése és C# és ADO.NET](sql-database-design-first-database-csharp.md)

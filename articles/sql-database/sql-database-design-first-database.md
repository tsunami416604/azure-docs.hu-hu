---
title: 'Oktatóanyag: Az első, az SSMS használatával Azure SQL Database relációs adatbázis megtervezésével |} A Microsoft Docs'
description: Ismerje meg az Azure SQL Database az SQL Server Management Studióval egy önálló adatbázis az első relációs adatbázis megtervezésével.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 3ca17ae905fff0911b58a0d336e0899ff385085c
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990479"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Oktatóanyag: Egyetlen adatbázisban az SSMS használatával Azure SQL Database relációs adatbázis tervezése

Az Azure SQL database egy relációs adatbázis-a-szolgáltatás (DBaaS) a Microsoft cloud (Azure). Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket az Azure Portal és az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával:

> [!div class="checklist"]
> - Hozzon létre egy önálló adatbázis az Azure portal *
> - Az Azure portal használatával kiszolgálószintű IP tűzfalszabály beállítása
> - Kapcsolódás az adatbázishoz SSMS segítségével
> - Táblázatok létrehozása az SSMS használatával
> - Adatok kötegelt betöltése a BCP használatával
> - Adatok lekérdezése az ssms használatával

Ha a nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

> [!NOTE]
> A jelen oktatóanyag egy adatbázist használjuk. A rugalmas készlet készletezett adatbázis vagy egy felügyelt példány-példány adatbázis is használhatja. A felügyelt példány csatlakozást tekintse meg a felügyelt példány útmutatókból: [Rövid útmutató: Konfigurálja az Azure-beli virtuális gép csatlakozni egy Azure SQL Database felügyelt példányába](sql-database-managed-instance-configure-vm.md) és [a rövid útmutató: Pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányába való helyszíni](sql-database-managed-instance-configure-p2s.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy telepítette:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (latest version)
- [BCP és SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (legújabb verzió)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-blank-single-database"></a>Üres önálló adatbázis létrehozása

Egy Azure SQL Database-adatbázis számítási és tárolási erőforrások egy meghatározott készletével együtt jön létre. Az adatbázis létrejön egy [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) és használatával kezelhetők egy [adatbázis-kiszolgáló](sql-database-servers.md).

Kövesse az alábbi lépéseket egy üres önálló adatbázis létrehozása.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Az **Új** oldalon válassza az **Adatbázisok** elemet az Azure Marketplace szakaszban, majd kattintson az **SQL Database** elemre a **Kiemelt** szakaszban.

   ![üres adatbázis létrehozása](./media/sql-database-design-first-database/create-empty-database.png)

3. Töltse ki a **SQL Database** űrlapon az alábbi információkkal az előző képen látható módon:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Adatbázis neve** | *yourDatabase* | Érvényes adatbázisnevekkel kapcsolatban lásd: [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers). |
    | **Előfizetés** | *yourSubscription*  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
    | **Erőforráscsoport** | *yourResourceGroup* | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
    | **Forrás kiválasztása** | Üres adatbázis | Meghatározza, hogy egy üres adatbázist kell létrehozni. |

4. Kattintson a **kiszolgáló** használja a meglévő adatbázis-kiszolgáló és a egy új adatbázis-kiszolgáló konfigurálása és létrehozása. Válasszon egy meglévő kiszolgálót, vagy kattintson a **hozzon létre egy új kiszolgálót** , és töltse ki a **új kiszolgáló** űrlapján az alábbi adatokat:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Kiszolgálónév** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
    | **Kiszolgálói rendszergazdai bejelentkezés** | Bármely érvényes név | Az érvényes bejelentkezési nevekkel kapcsolatban lásd: [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers). |
    | **Jelszó** | Bármely érvényes jelszó | A jelszó legalább nyolc karakter hosszúságúnak kell lennie, és kell használnia a karaktereket a következő kategóriák közül legalább háromból: nagybetűs karakterek, kisbetűs karakterek, számok és nem alfanumerikus karakterek. |
    | **Hely** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

    ![adatbázis-kiszolgáló létrehozása](./media/sql-database-design-first-database/create-database-server.png)

5. Kattintson a **Kiválasztás** gombra.
6. Kattintson a **Tarifacsomag** parancsra a szolgáltatásszint, a DTU-k vagy virtuális magok száma és a tárterületméret megadásához. Előfordulhat, hogy fedezze fel a lehetőségeket a dtu-k/virtuális magok és a storage, amely az egyes szolgáltatásszinteken elérhető.

    A szolgáltatási rétegben kiválasztása után a dtu-k vagy virtuális magok számát, és a tárterületméret kattintson **alkalmaz**.

7. Adjon meg egy **rendezést** az üres adatbázis (a jelen oktatóanyag esetében használja az alapértelmezett érték). A rendezésekkel kapcsolatos további információkért lásd: [Rendezések](/sql/t-sql/statements/collations)

8. Most, hogy Ön teljesítette a **SQL Database** alkotnak, kattintson a **létrehozás** az egyetlen adatbázis létrehozásához. Ez a lépés több percet is igénybe vehet.

9. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

   ![értesítés](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>IP-kiszolgálószintű tűzfalszabály létrehozása

Az SQL Database szolgáltatás a kiszolgáló szintjén hoz létre egy IP-tűzfalon. Ez a tűzfal megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a kiszolgáló és a kiszolgálón lévő adatbázisokhoz, kivéve, ha egy tűzfalszabály lehetővé teszi, hogy azok a tűzfalon keresztül IP. Ahhoz, hogy a külső kapcsolatokat az egyetlen adatbázishoz, először hozzá kell adnia egy IP-tűzfalszabály, az IP-cím (vagy IP-címtartomány). Kövesse az alábbi lépéseket követve létrehozhat egy [SQL Database kiszolgálószintű IP-tűzfalszabály](sql-database-firewall-configure.md).

> [!IMPORTANT]
> Az SQL Database szolgáltatás 1433-as porton keresztül kommunikál. Ha ez a szolgáltatás a vállalati hálózaton belülről csatlakozni próbál, a hálózati tűzfal előfordulhat, hogy nem engedélyezett a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem lehet csatlakoztatni az egyetlen adatbázishoz, kivéve, ha a rendszergazda megnyitja az 1433-as porton.

1. Az üzembe helyezés befejezése után kattintson a **SQL-adatbázisok** az bal oldali menüben, majd kattintson a *yourDatabase* a a **SQL-adatbázisok** lapot. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes **kiszolgálónév** (például *yourserver.database.windows.net*) és a további lehetőségeket biztosít.

2. Másolja ki a teljes kiszolgálónevet az SQL Server Management Studióval csatlakozhat a kiszolgáló és az adatbázisokhoz való használatra.

   ![kiszolgáló neve](./media/sql-database-design-first-database/server-name.png)

3. Kattintson a **Kiszolgálótűzfal beállítása** lehetőségre az eszköztáron. Megnyílik az SQL Database kiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

   ![kiszolgálószintű IP-tűzfalszabály](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Kattintson a **ügyfél IP-cím hozzáadása** az aktuális IP-cím hozzáadása egy új IP-tűzfalszabály az eszköztáron. Az IP-tűzfalszabály egyetlen IP-címet vagy egy IP-címtartományt az 1433-as port is megnyithatja.

5. Kattintson a **Save** (Mentés) gombra. Egy IP-kiszolgálószintű tűzfalszabályt az aktuális IP-címhez, az SQL Database-kiszolgálóhoz az 1433-as port megnyitása jön létre.

6. Kattintson az **OK** gombra, majd zárja be a **Tűzfalbeállítások** lapot.

Az IP-cím már továbbíthatja az IP-tűzfalon keresztül. Mostantól csatlakozhat az SQL Server Management Studio vagy más választott eszközzel, egyetlen adatbázis. Ügyeljen arra, használja a korábban létrehozott kiszolgálói rendszergazdai fiókkal.

> [!IMPORTANT]
> Alapértelmezés szerint az SQL Database IP-tűzfalon keresztül érhető el az Azure-szolgáltatásokhoz. Kattintson a **KI** gombra ezen az oldalon az összes Azure-szolgáltatás hozzáférésének letiltásához.

## <a name="connect-to-the-database"></a>Csatlakozzon az adatbázishoz

Használat [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) az egyetlen adatbázis-kapcsolatot létesíteni.

1. Nyissa meg az SQL Server Management Studiót.
2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Kiszolgáló típusa** | Adatbázismotor | Kötelezően megadandó érték. |
   | **Kiszolgálónév** | A teljes kiszolgálónév | Ha például *yourserver.database.windows.net*. |
   | **Hitelesítés** | SQL Server-hitelesítés | SQL-hitelesítésen kívül az egyetlen hitelesítési típus, ebben az oktatóanyagban korábban konfigurált. |
   | **Bejelentkezés** | A kiszolgálói rendszergazdai fiók | Az a fiók, amely a kiszolgáló létrehozásakor lett megadva. |
   | **Jelszó** | A kiszolgálói rendszergazdai fiók jelszava | Az a kiszolgáló létrehozásakor megadott jelszót. |

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-design-first-database/connect.png)

3. A **Connect to server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen kattintson az **Options** (Beállítások) elemre. Az a **csatlakozhat az adatbázishoz** területén adja meg *yourDatabase* ehhez az adatbázishoz való kapcsolódáshoz.

    ![csatlakozás kiszolgálón található adatbázishoz](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. Kattintson a **Connect** (Csatlakozás) gombra. A **Object Explorer** ablak az ssms-ben.

5. A **Object Explorer**, bontsa ki a **adatbázisok** majd *yourDatabase* a mintaadatbázisban található objektumok megtekintéséhez.

   ![adatbázis-objektumok](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Hozzon létre táblák az adatbázisban

A [Transact-SQL](/sql/t-sql/language-reference) használatával hozzon létre egy adatbázis-sémát négy táblázattal, amelyek az egyetemek hallgatókezelési rendszerét modellezik:

- Személy
- Kurzus
- Hallgató
- Kredit

Az alábbi ábrán látható, hogyan kapcsolódnak ezek a táblázatok egymáshoz. Ezen táblázatok némelyike más táblák oszlopaira hivatkozik. Például a *tanulói* táblára hivatkozik az *PersonId* oszlopa a *személy* tábla. Tanulmányozza a diagramot az ebben az oktatóanyagban szereplő táblák kapcsolatainak megértéséhez. A hatékony adatbázistáblák létrehozásának részleteiért lásd: [Hatékony adatbázistáblák létrehozása](https://msdn.microsoft.com/library/cc505842.aspx). Az adattípusok kiválasztására vonatkozó további információkért lásd: [Adattípusok](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Használhatja az [SQL Server Management Studio táblatervezőjét](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) is a táblák létrehozásához és tervezéséhez.

![Táblák közötti kapcsolatok](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. A **Object Explorer**, kattintson a jobb gombbal *yourDatabase* válassza **új lekérdezés**. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.

2. A lekérdezési ablakban hozzon létre négy táblát az adatbázisban a következő lekérdezés futtatásával:

   ```sql
   -- Create Person table
   CREATE TABLE Person
   (
       PersonId INT IDENTITY PRIMARY KEY,
       FirstName NVARCHAR(128) NOT NULL,
       MiddelInitial NVARCHAR(10),
       LastName NVARCHAR(128) NOT NULL,
       DateOfBirth DATE NOT NULL
   )

   -- Create Student table
   CREATE TABLE Student
   (
       StudentId INT IDENTITY PRIMARY KEY,
       PersonId INT REFERENCES Person (PersonId),
       Email NVARCHAR(256)
   )

   -- Create Course table
   CREATE TABLE Course
   (
       CourseId INT IDENTITY PRIMARY KEY,
       Name NVARCHAR(50) NOT NULL,
       Teacher NVARCHAR(256) NOT NULL
   )

   -- Create Credit table
   CREATE TABLE Credit
   (
       StudentId INT REFERENCES Student (StudentId),
       CourseId INT REFERENCES Course (CourseId),
       Grade DECIMAL(5,2) CHECK (Grade <= 100.00),
       Attempt TINYINT,
       CONSTRAINT [UQ_studentgrades] UNIQUE CLUSTERED
       (
           StudentId, CourseId, Grade, Attempt
       )
   )
   ```

   ![Táblázatok létrehozása](./media/sql-database-design-first-database/create-tables.png)

3. Bontsa ki a **táblák** csomópont alatt *yourDatabase* a a **Object Explorer** létrehozott táblák megtekintéséhez.

   ![létrehozott ssms-táblák](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba

1. Hozzon létre egy nevű *sampleData* a letöltések mappában az adatbázis mintaadatok tárolásához.

2. Kattintson a jobb gombbal az alábbi hivatkozásokat, és mentse őket a *sampleData* mappát.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Nyisson meg egy parancssori ablakot, és keresse meg a *sampleData* mappát.

4. Hajtsa végre az alábbi parancsokat a táblákat, és cserélje le az értékeket a Mintaadat beszúrásához *kiszolgáló*, *adatbázis*, *felhasználói*, és *jelszó* azokra az értékekre a környezethez.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Ezzel mintaadatokat töltött be a korábban létrehozott táblákba.

## <a name="query-data"></a>Adatok lekérdezése

Hajtsa végre a következő lekérdezéseket az adatbázistáblákban lévő információk lekéréséhez. Lásd: [írhat SQL-lekérdezések](https://technet.microsoft.com/library/bb264565.aspx) tudhat meg többet az SQL-lekérdezések írására. Az első lekérdezés mind a négy tábla található szerint "Dominick Pope" színesített a tanulók, akik hallgatót, mint 75 %-os csatlakozik. A második lekérdezés mind a négy tábla, és megkeresi a tanfolyamok, amelyben a "Noe Coleman" valaha regisztrálta.

1. Az SQL Server Management Studio lekérdezési ablakában hajtsa végre a következő lekérdezést:

   ```sql
   -- Find the students taught by Dominick Pope who have a grade higher than 75%
   SELECT  person.FirstName, person.LastName, course.Name, credit.Grade
   FROM  Person AS person
       INNER JOIN Student AS student ON person.PersonId = student.PersonId
       INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
       INNER JOIN Course AS course ON credit.CourseId = course.courseId
   WHERE course.Teacher = 'Dominick Pope'
       AND Grade > 75
   ```

2. A lekérdezési ablakban hajtsa végre a következő lekérdezést:

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled
   SELECT  course.Name, course.Teacher, credit.Grade
   FROM  Course AS course
       INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
       INNER JOIN Student AS student ON student.StudentId = credit.StudentId
       INNER JOIN Person AS person ON person.PersonId = student.PersonId
   WHERE person.FirstName = 'Noe'
       AND person.LastName = 'Coleman'
   ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, számos alapvető adatbázis-feladatokat. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Önálló adatbázis létrehozása
> - IP-kiszolgálószintű tűzfalszabály beállítása
> - Kapcsolódás az adatbázishoz [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával
> - Táblázatok létrehozása
> - Adatok kötegelt betöltése
> - Az adatok lekérdezése

Folytassa a következő oktatóanyaggal, amely az adatbázisok Visual Studio és C# használatával történő tervezését ismerteti.

> [!div class="nextstepaction"]
> [Egy Azure SQL Database-adatbázis a relációs adatbázis tervezése C# és az ADO.NET](sql-database-design-first-database-csharp.md)

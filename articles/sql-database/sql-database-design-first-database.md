---
title: 'Oktatóanyag: Tervezze meg az első relációs adatbázist az SSMS használatával'
description: Ismerje meg, hogyan tervezheti meg első relációs adatbázisát egyetlen adatbázisban az Azure SQL Database-ben az SQL Server Management Studio használatával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 07/29/2019
ms.openlocfilehash: 9764c4bc794eb8d133270b762fa2bca30a056fea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75459628"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-using-ssms"></a>Oktatóanyag: Relációs adatbázis tervezése egyetlen adatbázisban az Azure SQL-adatbázisban az SSMS használatával

Az Azure SQL-adatbázis egy relációs adatbázis-szolgáltatásként (DBaaS) a Microsoft Cloud (Azure). Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket az Azure Portal és az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával:

> [!div class="checklist"]
> - Egyetlen adatbázis létrehozása az Azure Portalon*
> - Kiszolgálószintű IP-tűzfalszabály beállítása az Azure Portal használatával
> - Kapcsolódás az adatbázishoz SSMS segítségével
> - Táblázatok létrehozása az SSMS használatával
> - Adatok kötegelt betöltése a BCP használatával
> - Adatok lekérdezése SSMS-sel

*Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt [hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/)

> [!TIP]
> A következő Microsoft Learn modul segítségével ingyenesen [megtudhatja, hogyan fejleszthet és konfigurálhat egy ASP.NET alkalmazást, amely lekérdezi az Azure SQL-adatbázist,](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)beleértve egy egyszerű adatbázis létrehozását is.
> [!NOTE]
> Ennek az oktatóanyagnak az alkalmazásában egyetlen adatbázist használunk. Használhat egy készletezett adatbázist egy rugalmas készletben vagy egy példány-adatbázist egy felügyelt példányban. A felügyelt példányhoz való kapcsolódásról a következő felügyelt példány-rövid útmutatók: [Rövid útmutató: Konfigurálja az Azure VM-et egy Azure SQL Database felügyelt példányhoz való csatlakozáshoz](sql-database-managed-instance-configure-vm.md) és [a gyorsútmutatóhoz: Konfiguráljon egy pont-hely kapcsolatot egy Azure SQL Database Felügyelt példányhoz a helyszíni környezetből.](sql-database-managed-instance-configure-p2s.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez győződjön meg arról, hogy telepítette:

- [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (legújabb verzió)
- [BCP és SQLCMD](https://www.microsoft.com/download/details.aspx?id=36433) (legújabb verzió)

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-a-blank-single-database"></a>Üres önálló adatbázis létrehozása

Az Azure SQL Database egyetlen adatbázisa jön létre a számítási és tárolási erőforrások meghatározott készletével. Az adatbázis egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül jön létre, és [egy adatbázis-kiszolgáló](sql-database-servers.md)használatával történik.

Az üres adatbázis létrehozásához kövesse az alábbi lépéseket.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
2. Az **Új** oldalon válassza az **Adatbázisok** elemet az Azure Marketplace szakaszban, majd kattintson az **SQL Database** elemre a **Kiemelt** szakaszban.

   ![üres adatbázis létrehozása](./media/sql-database-design-first-database/create-empty-database.png)

3. Töltse ki az **SQL Database** űrlapot a következő információkkal, ahogy az az előző képen látható:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Adatbázis neve** | *yourDatabase* | Az érvényes adatbázisneveket az [Adatbázis-azonosítók](/sql/relational-databases/databases/database-identifiers). |
    | **Előfizetés** | *az előfizetés*  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
    | **Erőforráscsoport** | *yourResourceGroup* | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
    | **Forrás kijelölése** | Üres adatbázis | Meghatározza, hogy egy üres adatbázist kell létrehozni. |

4. Kattintson a **Kiszolgáló** gombra meglévő adatbázis-kiszolgáló használatához, vagy hozzon létre és konfiguráljon egy új adatbázis-kiszolgálót. Válasszon ki egy meglévő kiszolgálót, vagy kattintson **az Új kiszolgáló létrehozása** gombra, és töltse ki az Új **kiszolgáló** űrlapot a következő információkkal:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Kiszolgáló neve** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
    | **Kiszolgálórendszergazdai bejelentkezés** | Bármely érvényes név | Az érvényes bejelentkezési nevekről az [Adatbázis-azonosítók](/sql/relational-databases/databases/database-identifiers)oldalon van. |
    | **Jelszó** | Bármely érvényes jelszó | A jelszónak legalább nyolc karakterből kell rendelkeznie, és a következő kategóriák közül három karaktert kell használnia: nagybetűs karakterek, kisbetűs karakterek, számok és nem alfanumerikus karakterek. |
    | **Helyen** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

    ![adatbázis-kiszolgáló létrehozása](./media/sql-database-design-first-database/create-database-server.png)

5. Kattintson a **Kiválasztás** gombra.
6. Kattintson a **Tarifacsomag** parancsra a szolgáltatási szint, a DTU-k vagy virtuális magok száma és a tárterületméret megadásához. A DIT-ek/virtuális magok és a szolgáltatásszintekhez elérhető tárhelyek számát is megismerheti.

    Miután kiválasztotta a szolgáltatási szintet, a DT-k vagy a virtuális magok számát és a tárterület mennyiségét, kattintson az **Alkalmaz**gombra.

7. Adja meg az üres adatbázis **illesztését** (ebben az oktatóanyagban használja az alapértelmezett értéket). A rendezésekkel kapcsolatos további információkért lásd: [Rendezések](/sql/t-sql/statements/collations)

8. Most, hogy kitöltötte az **SQL Database** űrlapot, kattintson a **Létrehozás** gombra az egyetlen adatbázis kiépítéséhez. Ez a lépés eltarthat néhány percig.

9. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

   ![értesítés](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Kiszolgálószintű IP-tűzfalszabály létrehozása

Az SQL Database szolgáltatás létrehoz egy IP-tűzfalat a kiszolgáló szintjén. Ez a tűzfal megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz és a kiszolgáló bármely adatbázisához, kivéve, ha a tűzfalszabály engedélyezi az IP-címüket a tűzfalon keresztül. Az egyetlen adatbázishoz való külső csatlakozás engedélyezéséhez először hozzá kell adnia egy IP-tűzfalszabályt az IP-címhez (vagy AZ IP-címtartományhoz). Sql [Database-kiszolgálószintű IP-tűzfalszabály](sql-database-firewall-configure.md)létrehozásához kövesse az alábbi lépéseket.

> [!IMPORTANT]
> Az SQL Database szolgáltatás az 1433-as porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni ehhez a szolgáltatáshoz, előfordulhat, hogy a hálózat tűzfala nem engedélyezi az 1433-as porton keresztüli kimenő forgalmat. Ha igen, csak akkor csatlakozhat egyetlen adatbázishoz, ha a rendszergazda megnyitja az 1433-as portot.

1. A központi telepítés befejezése után válassza ki az **SQL-adatbázisokat** az Azure Portal menüből, vagy keressen és válasszon *SQL-adatbázisokat* bármelyik lapról.  

1. Válassza *ki a yourDatabase* elemet az **SQL-adatbázisok** lapon. Megnyílik az adatbázis áttekintő lapja, amely a `contosodatabaseserver01.database.windows.net`teljesen minősített **kiszolgálónevet** (például ) mutatja, és további konfigurációs lehetőségeket biztosít.

   ![kiszolgáló neve](./media/sql-database-design-first-database/server-name.png)

1. Másolja a teljesen minősített kiszolgálónevet a kiszolgálóhoz és az adatbázisokhoz való csatlakozáshoz az SQL Server Management Studio kiszolgálóról.

1. Kattintson a **Kiszolgálótűzfal beállítása** lehetőségre az eszköztáron. Megnyílik az SQL-adatbáziskiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

   ![kiszolgálószintű IP-tűzfal szabály](./media/sql-database-design-first-database/server-firewall-rule.png)

1. Kattintson az **eszköztár Ügyfél IP-cím hozzáadása** gombjára, ha az aktuális IP-címet hozzá szeretné adni egy új IP-tűzfalszabályhoz. Az IP-tűzfalszabály egyetlen IP-címhez vagy IP-címtartományhoz is megnyithatja az 1433-as portot.

1. Kattintson a **Mentés** gombra. Az SQL Database-kiszolgálón az aktuális 1433-as IP-címmegnyitásához kiszolgálószintű IP-tűzfalszabály jön létre.

1. Kattintson az **OK** gombra, majd zárja be a **Tűzfalbeállítások** lapot.

Az IP-cím most már áthaladhat az IP tűzfalon. Most már csatlakozhat az egyetlen adatbázishoz az SQL Server Management Studio vagy más választott eszköz használatával. Ügyeljen arra, hogy a korábban létrehozott kiszolgálói rendszergazdai fiókot használja.

> [!IMPORTANT]
> Alapértelmezés szerint az SQL Database IP tűzfalon keresztüli hozzáférés engedélyezve van az összes Azure-szolgáltatáshoz. Kattintson a **KI** gombra ezen az oldalon az összes Azure-szolgáltatás hozzáférésének letiltásához.

## <a name="connect-to-the-database"></a>Csatlakozás az adatbázishoz

Az [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) segítségével hozzon létre kapcsolatot az egyetlen adatbázissal.

1. Nyissa meg az SQL Server Management Studiót.
2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Kiszolgáló típusa** | Adatbázismotor | Kötelezően megadandó érték. |
   | **Kiszolgáló neve** | A teljes kiszolgálónév | Például *yourserver.database.windows.net*. |
   | **Hitelesítés** | SQL Server-hitelesítés | Az SQL-hitelesítés az egyetlen hitelesítési típus, amelyet ebben az oktatóanyagban konfiguráltunk. |
   | **Bejelentkezés** | A kiszolgálói rendszergazdafiók | Az a fiók, amely a kiszolgáló létrehozásakor lett megadva. |
   | **Jelszó** | A kiszolgálói rendszergazdai fiók jelszava | A kiszolgáló létrehozásakor megadott jelszó. |

   ![kapcsolódás a kiszolgálóhoz](./media/sql-database-design-first-database/connect.png)

3. A **Connect to server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen kattintson az **Options** (Beállítások) elemre. A **Csatlakozás adatbázishoz** csoportban adja meg *az adatbázist* az adatbázishoz való csatlakozáshoz.

    ![csatlakozás kiszolgálón található adatbázishoz](./media/sql-database-design-first-database/options-connect-to-db.png)  

4. Kattintson a **Csatlakozás** gombra. Az **Objektumkezelő** ablak az SSMS-ben nyílik meg.

5. Az **Objektumkezelőben** **bontsa ki az Adatbázisok csomópontot, majd bontsa** ki az *adatbázist* a mintaadatbázis objektumainak megtekintéséhez.

   ![adatbázis-objektumok](./media/sql-database-design-first-database/connected.png)  

## <a name="create-tables-in-your-database"></a>Táblák létrehozása az adatbázisban

A [Transact-SQL](/sql/t-sql/language-reference) használatával hozzon létre egy adatbázis-sémát négy táblázattal, amelyek az egyetemek hallgatókezelési rendszerét modellezik:

- Személy
- Kurzus
- Hallgató
- Kredit

Az alábbi ábrán látható, hogyan kapcsolódnak ezek a táblázatok egymáshoz. Ezen táblázatok némelyike más táblák oszlopaira hivatkozik. *A Diák* tábla például a *Személy* tábla *PersonId* oszlopára hivatkozik. Tanulmányozza a diagramot az ebben az oktatóanyagban szereplő táblák kapcsolatainak megértéséhez. A hatékony adatbázistáblák létrehozásának részleteiért lásd: [Hatékony adatbázistáblák létrehozása](https://msdn.microsoft.com/library/cc505842.aspx). Az adattípusok kiválasztására vonatkozó további információkért lásd: [Adattípusok](/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Használhatja az [SQL Server Management Studio táblatervezőjét](/sql/ssms/visual-db-tools/design-database-diagrams-visual-database-tools) is a táblák létrehozásához és tervezéséhez.

![Táblák közötti kapcsolatok](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. Az **Objektumkezelőben**kattintson a jobb gombbal *az Adatbázisra,* és válassza az **Új lekérdezés**parancsot. Megnyílik egy, az adatbázishoz csatlakoztatott üres lekérdezési ablak.

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

3. **Bontsa** ki az *Adatbázis* alatti Táblák csomópontot az **Objektumkezelőben** a létrehozott táblák megtekintéséhez.

   ![létrehozott ssms-táblák](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="load-data-into-the-tables"></a>Adatok betöltése a táblákba

1. Hozzon létre egy *sampleData* nevű mappát a Letöltések mappában az adatbázis mintaadatainak tárolásához.

2. Kattintson a jobb gombbal az alábbi hivatkozásokra, és mentse őket a *sampleData* mappába.

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Nyisson meg egy parancssorablakot, és keresse meg a *sampleData* mappát.

4. A következő parancsokkal szúrhat be mintaadatokat a *kiszolgáló,* *az adatbázis*, a *felhasználó*és a *jelszó* értékeit a környezetben lévő értékekre cserélő táblákba.

   ```cmd
   bcp Course in SampleCourseData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <server>.database.windows.net -d <database> -U <user> -P <password> -q -c -t ","
   ```

Ezzel mintaadatokat töltött be a korábban létrehozott táblákba.

## <a name="query-data"></a>Adatok lekérdezése

Hajtsa végre a következő lekérdezéseket az adatbázistáblákban lévő információk lekéréséhez. Az [SQL-lekérdezések írása](https://technet.microsoft.com/library/bb264565.aspx) című témakörben olvashat bővebben az SQL-lekérdezések írásáról. Az első lekérdezés csatlakozik mind a négy táblát, hogy megtalálják a diákok által tanított "Dominick Pope", akik egy fokozat magasabb, mint 75%. A második lekérdezés mind a négy táblát egyesíti, és megkeresi azokat a tanfolyamokat, amelyekbe a "Noe Coleman" valaha is beiratkozott.

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

Ebben az oktatóanyagban számos alapvető adatbázis-feladatot ismertet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Egyetlen adatbázis létrehozása
> - Kiszolgálószintű IP-tűzfalszabály beállítása
> - Kapcsolódás az adatbázishoz [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS) használatával
> - Táblázatok létrehozása
> - Adatok kötegelt betöltése
> - Az adatok lekérdezése

Folytassa a következő oktatóanyaggal, amely az adatbázisok Visual Studio és C# használatával történő tervezését ismerteti.

> [!div class="nextstepaction"]
> [Relációs adatbázis tervezése egyetlen adatbázisban az Azure SQL Database C# és ADO.NET](sql-database-design-first-database-csharp.md)

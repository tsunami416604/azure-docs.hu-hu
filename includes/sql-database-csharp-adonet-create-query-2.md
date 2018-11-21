---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 11/09/2018
ms.author: genemi
ms.openlocfilehash: c4329b9efef3cdb2911466e64ac6c9f07a1e9b31
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52270369"
---
<a name="cs_0_csharpprogramexample_h2"/>

## <a name="c-program-example"></a>C#program példa

A következő szakaszok a jelen cikk egy C# program, amely az SQL database Transact-SQL-utasítások küldése az ADO.NET használatával. A C# program a következő műveleteket hajtja végre:

1. [Az SQL Database adatbázishoz az ADO.NET használatával kapcsolódó](#cs_1_connect).
2. [Táblák létrehozása](#cs_2_createtables).
3. [Tölti fel adatokkal, a táblák a T-SQL INSERT utasítások kiállításával](#cs_3_insert).
4. [Frissíti az adatokat a join használatával](#cs_4_updatejoin).
5. [Törli az adatokat a join használatával](#cs_5_deletejoin).
6. [Adatsorokat kijelölése segítségével történik a csatlakozás](#cs_6_selectrows).
7. Bezárja a kapcsolatot (amely ideiglenes táblákkal csökken a TempDB adatbázis).

A C# program tartalmazza:

- C#kód az adatbázishoz való csatlakozáshoz.
- A T-SQL-forráskód visszaadó módszerek.
- Küldje el a T-SQL-adatbázis a két módszer.

#### <a name="to-compile-and-run"></a>Fordítása és futtatása

Ez C# program logikailag egy .cs fájl. De itt a program fizikailag oszlik több kódblokkok, minden egyes könnyebben látható és értelmezhető legyen. Fordítsa le és futtassa a programot, tegye a következőket:

1. Hozzon létre egy C# projektre a Visual Studióban.
    - A projekt típusúnak kell lennie egy *konzol* alkalmazás, az alábbihoz hasonló a következő hierarchia: **sablonok** > **Visual C#**  > **Windows klasszikus Asztalialkalmazás** > **Console App (.NET Framework)**.
3. A fájl **Program.cs**, a kis alapszintű kódsorokat törlése.
3. Program.cs másolja és illessze be a következő blokkok mindegyike ugyanabban a sorrendben jelenjenek meg ezek itt.
4. A program.cs fájlban szerkessze a következő értékeket a **fő** módszer:

   - **CB. Adatforrás**
   - **CD-ről. Felhasználói azonosító**
   - **CB. Jelszó**
   - **InitialCatalog**

5. Ellenőrizze, hogy a szerelvény **System.Data.dll** hivatkozik. Annak ellenőrzéséhez, bontsa ki a **hivatkozások** csomópontja a **Megoldáskezelőben** ablaktáblán.
6. Hozhat létre a program a Visual Studióban, kattintson a **összeállítása** menü.
7. Futtassa a programot a Visual Studióban, kattintson a **Start** gombra. A jelentés kimenetében a cmd.exe ablakban jelenik meg.

> [!NOTE]
> Lehetősége van módosítani a T-SQL egy bevezető hozzáadandó **#** a táblanevek, amely létrehozza őket az ideiglenes táblák **tempdb**. Ez akkor hasznos, bemutatási céllal, ha nincs teszt adatbázis nem érhető el. A kapcsolat bezárása után automatikusan törlődnek az ideiglenes táblák. Az idegen kulcsok minden HIVATKOZÁST a rendszer nem tartatja ideiglenes táblák esetében.
>

<a name="cs_1_connect"/>
### <a name="c-block-1-connect-by-using-adonet"></a>C#1 letiltása: Csatlakozás az ADO.NET használatával

- [Tovább](#cs_2_createtables)


```csharp
using System;
using System.Data.SqlClient;   // System.Data.dll 
//using System.Data;           // For:  SqlDbType , ParameterDirection

namespace csharp_db_test
{
   class Program
   {
      static void Main(string[] args)
      {
         try
         {
            var cb = new SqlConnectionStringBuilder();
            cb.DataSource = "your_server.database.windows.net";
            cb.UserID = "your_user";
            cb.Password = "your_password";
            cb.InitialCatalog = "your_database";

            using (var connection = new SqlConnection(cb.ConnectionString))
            {
               connection.Open();

               Submit_Tsql_NonQuery(connection, "2 - Create-Tables",
                  Build_2_Tsql_CreateTables());

               Submit_Tsql_NonQuery(connection, "3 - Inserts",
                  Build_3_Tsql_Inserts());

               Submit_Tsql_NonQuery(connection, "4 - Update-Join",
                  Build_4_Tsql_UpdateJoin(),
                  "@csharpParmDepartmentName", "Accounting");

               Submit_Tsql_NonQuery(connection, "5 - Delete-Join",
                  Build_5_Tsql_DeleteJoin(),
                  "@csharpParmDepartmentName", "Legal");

               Submit_6_Tsql_SelectEmployees(connection);
            }
         }
         catch (SqlException e)
         {
            Console.WriteLine(e.ToString());
         }
         Console.WriteLine("View the report output here, then press any key to end the program...");
         Console.ReadKey();
      }
```


<a name="cs_2_createtables"/>
### <a name="c-block-2-t-sql-to-create-tables"></a>C#2 letiltása: T-SQL-táblák létrehozása

- [Előző](#cs_1_connect) &nbsp;  /  &nbsp; [tovább](#cs_3_insert)

```csharp
      static string Build_2_Tsql_CreateTables()
      {
         return @"
DROP TABLE IF EXISTS tabEmployee;
DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.


CREATE TABLE tabDepartment
(
   DepartmentCode  nchar(4)          not null
      PRIMARY KEY,
   DepartmentName  nvarchar(128)     not null
);

CREATE TABLE tabEmployee
(
   EmployeeGuid    uniqueIdentifier  not null  default NewId()
      PRIMARY KEY,
   EmployeeName    nvarchar(128)     not null,
   EmployeeLevel   int               not null,
   DepartmentCode  nchar(4)              null
      REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
);
";
      }
```

#### <a name="entity-relationship-diagram-erd"></a>Entitás kapcsolati Diagram (helyreállító lemez)

Az előző CREATE TABLE utasítás magában foglalja a **hivatkozások** kulcsszó használatával hozzon létre egy *idegen kulcs* (FK) kapcsolatnak két táblázat között.  Ha a tempdb használ, tegye megjegyzésbe a `--REFERENCES` kulcsszó használatával vezető kötőjelek párjai.

Ezután a Helyreállító, amely megjeleníti a kapcsolat a két táblázat között. A #tabEmployee.DepartmentCode értékei *gyermek* oszlop korlátozva, az értékek a #tabDepartment.Department *szülő* oszlop.

![Helyreállító lemez ábrázoló idegen kulcs](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)


<a name="cs_3_insert"/>
### <a name="c-block-3-t-sql-to-insert-data"></a>C#3 letiltása: T-SQL-adatok beszúrása

- [Előző](#cs_2_createtables) &nbsp;  /  &nbsp; [tovább](#cs_4_updatejoin)


```csharp
      static string Build_3_Tsql_Inserts()
      {
         return @"
-- The company has these departments.
INSERT INTO tabDepartment
   (DepartmentCode, DepartmentName)
      VALUES
   ('acct', 'Accounting'),
   ('hres', 'Human Resources'),
   ('legl', 'Legal');

-- The company has these employees, each in one department.
INSERT INTO tabEmployee
   (EmployeeName, EmployeeLevel, DepartmentCode)
      VALUES
   ('Alison'  , 19, 'acct'),
   ('Barbara' , 17, 'hres'),
   ('Carol'   , 21, 'acct'),
   ('Deborah' , 24, 'legl'),
   ('Elle'    , 15, null);
";
      }
```


<a name="cs_4_updatejoin"/>
### <a name="c-block-4-t-sql-to-update-join"></a>C#4 letiltása: T-SQL-frissítés való csatlakozás

- [Előző](#cs_3_insert) &nbsp;  /  &nbsp; [tovább](#cs_5_deletejoin)


```csharp
      static string Build_4_Tsql_UpdateJoin()
      {
         return @"
DECLARE @DName1  nvarchar(128) = @csharpParmDepartmentName;  --'Accounting';


-- Promote everyone in one department (see @parm...).
UPDATE empl
   SET
      empl.EmployeeLevel += 1
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName1;
";
      }
```


<a name="cs_5_deletejoin"/>
### <a name="c-block-5-t-sql-to-delete-join"></a>C#5 letiltása: T-SQL-delete-csatlakoztatás

- [Előző](#cs_4_updatejoin) &nbsp;  /  &nbsp; [tovább](#cs_6_selectrows)


```csharp
      static string Build_5_Tsql_DeleteJoin()
      {
         return @"
DECLARE @DName2  nvarchar(128);
SET @DName2 = @csharpParmDepartmentName;  --'Legal';


-- Right size the Legal department.
DELETE empl
   FROM
      tabEmployee   as empl
      INNER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   WHERE
      dept.DepartmentName = @DName2

-- Disband the Legal department.
DELETE tabDepartment
   WHERE DepartmentName = @DName2;
";
      }
```



<a name="cs_6_selectrows"/>
### <a name="c-block-6-t-sql-to-select-rows"></a>C#6 letiltása: T-SQL-sorok kiválasztása

- [Előző](#cs_5_deletejoin) &nbsp;  /  &nbsp; [tovább](#cs_6b_datareader)


```csharp
      static string Build_6_Tsql_SelectEmployees()
      {
         return @"
-- Look at all the final Employees.
SELECT
      empl.EmployeeGuid,
      empl.EmployeeName,
      empl.EmployeeLevel,
      empl.DepartmentCode,
      dept.DepartmentName
   FROM
      tabEmployee   as empl
      LEFT OUTER JOIN
      tabDepartment as dept ON dept.DepartmentCode = empl.DepartmentCode
   ORDER BY
      EmployeeName;
";
      }
```


<a name="cs_6b_datareader"/>
### <a name="c-block-6b-executereader"></a>C#6b letiltása: Executereadert

- [Előző](#cs_6_selectrows) &nbsp;  /  &nbsp; [tovább](#cs_7_executenonquery)

Ez a módszer futtatásra tervezték, a T-SQL SELECT utasítást, amely szerint a **Build_6_Tsql_SelectEmployees** metódust.


```csharp
      static void Submit_6_Tsql_SelectEmployees(SqlConnection connection)
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("Now, SelectEmployees (6)...");

         string tsql = Build_6_Tsql_SelectEmployees();

         using (var command = new SqlCommand(tsql, connection))
         {
            using (SqlDataReader reader = command.ExecuteReader())
            {
               while (reader.Read())
               {
                  Console.WriteLine("{0} , {1} , {2} , {3} , {4}",
                     reader.GetGuid(0),
                     reader.GetString(1),
                     reader.GetInt32(2),
                     (reader.IsDBNull(3)) ? "NULL" : reader.GetString(3),
                     (reader.IsDBNull(4)) ? "NULL" : reader.GetString(4));
               }
            }
         }
      }
```


<a name="cs_7_executenonquery"/>
### <a name="c-block-7-executenonquery"></a>C#7 letiltása: ExecuteNonQuery

- [Előző](#cs_6b_datareader) &nbsp;  /  &nbsp; [tovább](#cs_8_output)

Ezt a módszert nevezik, műveletek, amelyek módosítják a táblákat adatok tartalmának bármely adatsor visszaszolgáltatása nélkül.


```csharp
      static void Submit_Tsql_NonQuery(
         SqlConnection connection,
         string tsqlPurpose,
         string tsqlSourceCode,
         string parameterName = null,
         string parameterValue = null
         )
      {
         Console.WriteLine();
         Console.WriteLine("=================================");
         Console.WriteLine("T-SQL to {0}...", tsqlPurpose);

         using (var command = new SqlCommand(tsqlSourceCode, connection))
         {
            if (parameterName != null)
            {
               command.Parameters.AddWithValue(  // Or, use SqlParameter class.
                  parameterName,
                  parameterValue);
            }
            int rowsAffected = command.ExecuteNonQuery();
            Console.WriteLine(rowsAffected + " = rows affected.");
         }
      }
   } // EndOfClass
}
```


<a name="cs_8_output"/>
### <a name="c-block-8-actual-test-output-to-the-console"></a>C#8 letiltása: a konzol kimenetét tényleges teszt

- [Előző](#cs_7_executenonquery)

Ebben a szakaszban rögzíti a kimenet a program elküldött a konzolhoz. Csak a guid értékek tesztelések között változhat.


```text
[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>> csharp_db_test.exe

=================================
Now, CreateTables (10)...

=================================
Now, Inserts (20)...

=================================
Now, UpdateJoin (30)...
2 rows affected, by UpdateJoin.

=================================
Now, DeleteJoin (40)...

=================================
Now, SelectEmployees (50)...
0199be49-a2ed-4e35-94b7-e936acf1cd75 , Alison , 20 , acct , Accounting
f0d3d147-64cf-4420-b9f9-76e6e0a32567 , Barbara , 17 , hres , Human Resources
cf4caede-e237-42d2-b61d-72114c7e3afa , Carol , 22 , acct , Accounting
cdde7727-bcfd-4f72-a665-87199c415f8b , Elle , 15 , NULL , NULL

[C:\csharp_db_test\csharp_db_test\bin\Debug\]
>>
```

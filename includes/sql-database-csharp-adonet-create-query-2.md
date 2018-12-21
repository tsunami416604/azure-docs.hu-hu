---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: e30651cb0ed7d74082163a92acbc428c21018255
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53728570"
---
## <a name="c-program-example"></a>C#program példa

A következő szakaszok a jelen cikk egy C# program, amely az SQL database Transact-SQL (T-SQL) utasításokat küldeni az ADO.NET használatával. A C# program mutatja be a következő műveleteket:

- [Csatlakozás SQL Database adatbázishoz az ADO.NET használatával](#cs_1_connect)
- [Metod, které vracejí a T-SQL-utasítások](#cs_2_return)
    - Táblázatok létrehozása
    - Töltse fel az adatokat tartalmazó táblák
    - Frissítési, törlési és kiválasztja az adatokat
- [Küldje el a T-SQL az adatbázishoz](#cs_3_submit)

### <a name="entity-relationship-diagram-erd"></a>Entitás kapcsolati Diagram (helyreállító lemez)

A `CREATE TABLE` utasításokat tartalmaznak a **hivatkozások** kulcsszó használatával hozzon létre egy *idegen kulcs* (FK) kapcsolatnak két táblázat között. Ha használ *tempdb*, tegye megjegyzésbe a `--REFERENCES` kulcsszó használatával vezető kötőjelek párjai.

A helyreállító lemez jeleníti meg a kapcsolat a két táblázat között. Az értékeket a **tabEmployee.DepartmentCode** *gyermek* oszlop értékeit a korlátozódnak a **tabDepartment.DepartmentCode** *szülő*oszlop.

![Helyreállító lemez ábrázoló idegen kulcs](./media/sql-database-csharp-adonet-create-query-2/erd-dept-empl-fky-2.png)

> [!NOTE]
> Lehetősége van módosítani a T-SQL egy bevezető hozzáadandó `#` a táblanevek, amely létrehozza őket az ideiglenes táblák *tempdb*. Ez akkor hasznos, bemutatási célokra, amikor nincs teszt adatbázisa nem érhető el. Minden hivatkozás külső kulcsok használata során nem lépnek érvénybe, és az ideiglenes táblák automatikusan a rendszer törli, ha a kapcsolat bezárása után a program futtatása befejeződik.

### <a name="to-compile-and-run"></a>Fordítása és futtatása

A C# program logikailag egy .cs fájlt, és számos kódblokkok, hogy minden egyes megérteni a fizikailag oszlik. Fordítsa le és futtassa a programot, hajtsa végre a következő lépéseket:

1. Hozzon létre egy C# projektre a Visual Studióban. A projekt típusúnak kell lennie egy *konzol*, alatt található **sablonok** > **Visual C#**   >  **Windows asztali**  >  **Console App (.NET Framework)**.

1. A fájl *Program.cs*, az alapszintű kódsorokat cserélje le az alábbi lépéseket:

    1. Másolás és beillesztés, az alábbi kódblokkok, ugyanabban a sorrendben, megjelenik, lásd: [csatlakozhat az adatbázishoz](#cs_1_connect), [készítése a T-SQL](#cs_2_return), és [küldése adatbázisba](#cs_3_submit).

    1. A következő értékeket módosítsa a `Main` módszer:

        - *CB. Adatforrás*
        - *CB. Felhasználói azonosító*
        - *CB. Jelszó*
        - *CB. InitialCatalog*

1. Ellenőrizze a szerelvény *System.Data.dll* hivatkozik. Annak ellenőrzéséhez, bontsa ki a **hivatkozások** csomópontja a **Megoldáskezelőben** ablaktáblán.

1. Buildjének elkészítéséhez, és futtassa a programot a Visual Studióban, a **Start** gombra. A jelentés kimenete egy program ablakban jelenik meg, ha GUID értékek tesztelések között változhat.

    ```Output
    =================================
    T-SQL to 2 - Create-Tables...
    -1 = rows affected.

    =================================
    T-SQL to 3 - Inserts...
    8 = rows affected.

    =================================
    T-SQL to 4 - Update-Join...
    2 = rows affected.

    =================================
    T-SQL to 5 - Delete-Join...
    2 = rows affected.

    =================================
    Now, SelectEmployees (6)...
    8ddeb8f5-9584-4afe-b7ef-d6bdca02bd35 , Alison , 20 , acct , Accounting
    9ce11981-e674-42f7-928b-6cc004079b03 , Barbara , 17 , hres , Human Resources
    315f5230-ec94-4edd-9b1c-dd45fbb61ee7 , Carol , 22 , acct , Accounting
    fcf4840a-8be3-43f7-a319-52304bf0f48d , Elle , 15 , NULL , NULL
    View the report output here, then press any key to end the program...
    ```

<a name="cs_1_connect"/>

### <a name="connect-to-sql-database-using-adonet"></a>Csatlakozás SQL Database adatbázishoz az ADO.NET használatával

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

                    Submit_Tsql_NonQuery(connection, "2 - Create-Tables", Build_2_Tsql_CreateTables());

                    Submit_Tsql_NonQuery(connection, "3 - Inserts", Build_3_Tsql_Inserts());

                    Submit_Tsql_NonQuery(connection, "4 - Update-Join", Build_4_Tsql_UpdateJoin(),
                        "@csharpParmDepartmentName", "Accounting");

                    Submit_Tsql_NonQuery(connection, "5 - Delete-Join", Build_5_Tsql_DeleteJoin(),
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

<a name="cs_2_return"/>

### <a name="methods-that-return-t-sql-statements"></a>Metod, které vracejí a T-SQL-utasítások

```csharp
static string Build_2_Tsql_CreateTables()
{
    return @"
        DROP TABLE IF EXISTS tabEmployee;
        DROP TABLE IF EXISTS tabDepartment;  -- Drop parent table last.

        CREATE TABLE tabDepartment
        (
            DepartmentCode  nchar(4)          not null    PRIMARY KEY,
            DepartmentName  nvarchar(128)     not null
        );

        CREATE TABLE tabEmployee
        (
            EmployeeGuid    uniqueIdentifier  not null  default NewId()    PRIMARY KEY,
            EmployeeName    nvarchar(128)     not null,
            EmployeeLevel   int               not null,
            DepartmentCode  nchar(4)              null
            REFERENCES tabDepartment (DepartmentCode)  -- (REFERENCES would be disallowed on temporary tables.)
        );
    ";
}

static string Build_3_Tsql_Inserts()
{
    return @"
        -- The company has these departments.
        INSERT INTO tabDepartment (DepartmentCode, DepartmentName)
        VALUES
            ('acct', 'Accounting'),
            ('hres', 'Human Resources'),
            ('legl', 'Legal');

        -- The company has these employees, each in one department.
        INSERT INTO tabEmployee (EmployeeName, EmployeeLevel, DepartmentCode)
        VALUES
            ('Alison'  , 19, 'acct'),
            ('Barbara' , 17, 'hres'),
            ('Carol'   , 21, 'acct'),
            ('Deborah' , 24, 'legl'),
            ('Elle'    , 15, null);
    ";
}

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

<a name="cs_3_submit"/>

### <a name="submit-t-sql-to-the-database"></a>Küldje el a T-SQL az adatbázishoz

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
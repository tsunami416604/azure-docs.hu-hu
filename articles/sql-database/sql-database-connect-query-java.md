---
title: "Csatlakozás az Azure SQL Database-hez Java használatával | Microsoft Docs"
description: "A cikk egy Java-kódmintát mutat be, amellyel csatlakozhat egy Azure SQL Database-adatbázishoz, és lekérdezéseket hajthat végre."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 05/23/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 0df7bd56d3aac462a86e31b5512e48371918bbf2
ms.contentlocale: hu-hu
ms.lasthandoff: 07/04/2017


---
<a id="azure-sql-database-use-java-to-connect-and-query-data" class="xliff"></a>

# Azure SQL Database: Csatlakozás és adatlekérdezés Java használatával

Ez a gyors üzembehelyezési útmutató ismerteti, hogyan használható a [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) az Azure SQL Database-adatbázishoz való csatlakozáshoz, majd hogyan lehet Transact-SQL-utasítások használatával lekérdezni, beszúrni, frissíteni és adatot törölni az adatbázisban Mac OS, Ubuntu Linux és Windows platformokról.

<a id="prerequisites" class="xliff"></a>

## Előfeltételek

Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja kiindulási pontnak:

- [DB létrehozása – portál](sql-database-get-started-portal.md)
- [DB létrehozása – CLI](sql-database-get-started-cli.md)
- [DB létrehozása – PowerShell](sql-database-get-started-powershell.md)

<a id="install-java-software" class="xliff"></a>

## Java szoftver telepítése

A jelen szakaszban ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a Java használatával, az Azure SQL Database használatában pedig még járatlan. Ha még tapasztalatlan a Java-fejlesztésekben, lépjen a [Alkalmazás készítése az SQL Serverrel](https://www.microsoft.com/en-us/sql-server/developer-get-started/) című cikkre, válassza ki a **Java** nyelvet, majd az operációs rendszert.

<a id="mac-os" class="xliff"></a>

### **Mac OS**
Nyissa meg a terminált, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Java projektet. A **brew** és a **Maven** telepítéséhez használja a következő parancsokat: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

<a id="linux-ubuntu" class="xliff"></a>

### **Linux (Ubuntu)**
Nyissa meg a terminált, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Java projektet. A **Maven** telepítéséhez használja a következő parancsokat:

```bash
sudo apt-get install maven
```

<a id="windows" class="xliff"></a>

### **Windows**
Telepítse a [Mavent](https://maven.apache.org/download.cgi) a hivatalos telepítővel. A Maven segít a függőségek kezelésében, valamint a Java-projektek készítésében, tesztelésében és futtatásában. 

<a id="get-connection-information" class="xliff"></a>

## Kapcsolatadatok lekérése

Kérje le az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. A későbbi eljárásokban szüksége lesz a teljes kiszolgálónévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az adatbázis **Áttekintés** lapján tekintse meg a teljes kiszolgálónevet, amint az alábbi képen is látható. Ha a mutatót a kiszolgáló neve fölé viszi, megjelenik a **Kattintson a másoláshoz** lehetőség. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Amennyiben elfelejtette a kiszolgáló bejelentkezési adatait, lépjen az SQL Database-kiszolgáló oldalára, és itt megtudhatja a kiszolgáló rendszergazdájának nevét, valamint szükség esetén visszaállíthatja a jelszót.
5. Kattintson az: **Adatbázis-kapcsolati karakterláncok megjelenítése** elemre.

6. Tekintse át a teljes **JDBC**-kapcsolati karakterláncot.

    ![JDBC-kapcsolati karakterlánc](./media/sql-database-connect-query-jdbc/jdbc-connection-string.png)   

<a id="create-maven-project" class="xliff"></a>

### **Maven-projekt létrehozása**
Hozzon létre új Maven-projektet a terminálról. 
```bash
mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
```

Vegye fel a **Microsoft JDBC Driver for SQL Server**-t (Microsoft JDBC-illesztőprogram SQL Serverhez) a függőségek közé a ***pom.xml*** fájlban. 

```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.2.0.jre8</version>
</dependency>
```

<a id="select-data" class="xliff"></a>

## Adatok kiválasztása

A következő kód használatával lekérdezheti kategóriánként az első 20 terméket a [Connection](https://docs.microsoft.com/sql/connect/jdbc/working-with-a-connection) osztály használatával, egy [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) Transact-SQL-utasítással. A hostHame, dbName, user és password paramétereket írja át az adatbázisnak az AdventureWorksLT mintaadatokkal történt létrehozásakor megadott értékekre. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.Statement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName " 
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";
                
                try (Statement statement = connection.createStatement();
                    ResultSet resultSet = statement.executeQuery(selectSql)) {

                        // Print results from select statement
                        System.out.println("Top 20 categories:");
                        while (resultSet.next())
                        {
                            System.out.println(resultSet.getString(1) + " "
                                + resultSet.getString(2));
                        }
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

<a id="insert-data" class="xliff"></a>

## Adat beszúrása

A következő kód használatával beszúrhat egy új terméket a SalesLT.Product táblába a [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) osztály használatával, egy [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) Transact-SQL-utasítással. A hostHame, dbName, user és password paramétereket írja át az adatbázisnak az AdventureWorksLT mintaadatokkal történt létrehozásakor megadott értékekre. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Insert data example:");
                System.out.println("=========================================");

                // Prepared statement to insert data
                String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, " 
                    + " StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

                java.util.Date date = new java.util.Date();
                java.sql.Timestamp sqlTimeStamp = new java.sql.Timestamp(date.getTime());

                try (PreparedStatement prep = connection.prepareStatement(insertSql)) {
                        prep.setString(1, "BrandNewProduct");
                        prep.setInt(2, 200989);
                        prep.setString(3, "Blue");
                        prep.setDouble(4, 75);
                        prep.setDouble(5, 80);
                        prep.setTimestamp(6, sqlTimeStamp);

                        int count = prep.executeUpdate();
                        System.out.println("Inserted: " + count + " row(s)");
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```
<a id="update-data" class="xliff"></a>

## Adatok frissítése

A következő kód használatával módosíthatja a korábban beszúrt új terméket a [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) osztály használatával, egy [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) Transact-SQL-utasítással. A hostHame, dbName, user és password paramétereket írja át az adatbázisnak az AdventureWorksLT mintaadatokkal történt létrehozásakor megadott értékekre. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Update data example:");
                System.out.println("=========================================");

                // Prepared statement to update data
                String updateSql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(updateSql)) {
                        prep.setString(1, "500");
                        prep.setString(2, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Updated: " + count + " row(s)")
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```



<a id="delete-data" class="xliff"></a>

## Adat törlése

A következő kód használatával törölheti az előzőleg beszúrt új terméket a [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) használatával, egy [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) Transact-SQL-utasítással. A hostHame, dbName, user és password paramétereket írja át az adatbázisnak az AdventureWorksLT mintaadatokkal történt létrehozásakor megadott értékekre. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Delete data example:");
                System.out.println("=========================================");

                // Prepared statement to delete data
                String deleteSql = "DELETE SalesLT.Product WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(deleteSql)) {
                        prep.setString(1, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Deleted: " + count + " row(s)");
                }
        }       
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

<a id="next-steps" class="xliff"></a>

## Következő lépések
- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [Microsoft JDBC-illesztőprogram SQL Serverhez](https://github.com/microsoft/mssql-jdbc)
- [Problémák jelentése/kérdések](https://github.com/microsoft/mssql-jdbc/issues)



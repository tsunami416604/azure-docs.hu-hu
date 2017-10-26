---
title: "Java használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan használhatja a Javát egy Azure SQL Database-adatbázishoz csatlakozó program létrehozásához, és hogyan hajthat végre lekérdezést Transact-SQL-utasításokkal."
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
ms.topic: quickstart
ms.date: 07/10/2017
ms.author: andrela
ms.openlocfilehash: 943e355ffcc6c0670585936ec2e559b7fb592ff5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-java-to-query-an-azure-sql-database"></a>Java használata Azure SQL Database-adatbázis lekérdezéséhez

Ez a gyors üzembehelyezési útmutató ismerteti, hogyan használható a [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) egy Azure SQL Database-adatbázishoz való csatlakozáshoz, és hogyan lehet Transact-SQL-utasítások használatával adatokat lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

A gyors üzembehelyezési útmutató befejezéséhez győződjön meg róla, hogy rendelkezik az alábbi előfeltételekkel:

- Azure SQL Database-adatbázis. Ez a rövid útmutató az alábbi rövid útmutatók egyikében létrehozott erőforrásokat használja: 

   - [DB létrehozása – portál](sql-database-get-started-portal.md)
   - [DB létrehozása – CLI](sql-database-get-started-cli.md)
   - [DB létrehozása – PowerShell](sql-database-get-started-powershell.md)

- A gyors üzembe helyezési útmutatóhoz használt számítógép nyilvános IP-címére vonatkozó [kiszolgálószintű tűzfalszabály](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

- Telepítette a Javát és az operációs rendszerének megfelelő kapcsolódó szoftvereket.

    - **MacOS**: Telepítse a Homebrew-t és a Javát, majd telepítse a Mavent. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).
    - **Ubuntu**: Telepítse a Java-fejlesztői készletet, majd telepítse a Mavent. Lásd az [1.2, 1.3 és 1.4 lépést](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).
    - **Windows**: Telepítse a Java-fejlesztői készletet, majd a Mavent. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).    

## <a name="sql-server-connection-information"></a>Az SQL-kiszolgáló kapcsolatadatai

Kérje le az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolatadatokat. A későbbi eljárásokban szüksége lesz a teljes kiszolgálónévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. 
3. Az adatbázis **Áttekintés** oldalán tekintse meg a teljes kiszolgálónevet az alábbi képen látható módon: Ha a mutatót a kiszolgáló neve fölé viszi, megjelenik a **Kattintson ide a másoláshoz** lehetőség.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Ha elfelejtette a kiszolgálói bejelentkezési adatait, lépjen az SQL Database-kiszolgáló oldalára, ahol megtekintheti a kiszolgáló rendszergazdájának nevét.  Szükség esetén kérjen új jelszót.     

## <a name="create-maven-project-and-dependencies"></a>**Maven-projekt és függőségek létrehozása**
1. A terminálból hozza létre az **sqltest** nevű új Maven-projektet. 

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

2. Amikor a rendszer kéri, írja be az **Y** karaktert.
3. Módosítsa a könyvtárat **sqltest** névre, és nyissa meg a ***pom.xml*** fájlt egy tetszőleges szövegszerkesztőben.  Adja hozzá a projekt függőségeihez az **SQL Serverhez készült Microsoft JDBC-illesztőprogram** elemet a következő kóddal:

   ```xml
   <dependency>
       <groupId>com.microsoft.sqlserver</groupId>
       <artifactId>mssql-jdbc</artifactId>
       <version>6.2.1.jre8</version>
   </dependency>
   ```

4. A ***pom.xml*** fájlban adja hozzá a következő tulajdonságokat a projekthez.  Ha nincs tulajdonságok szakasz, a függőségek után adhatja hozzá őket.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

5. Mentse és zárja be a ***pom.xml*** fájlt.

## <a name="insert-code-to-query-sql-database"></a>Kód beszúrása SQL-adatbázis lekérdezéséhez

1. A Maven-projektben már rendelkezik egy ***App.java*** nevű fájllal a következő helyen: ..\sqltest\src\main\java\com\sqlsamples\App.java

2. Nyissa meg a fájlt, cserélje le a tartalmát a következő kódra, és adja meg a kiszolgáló és az adatbázis megfelelő adatait, valamint a felhasználót és a jelszót.

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
           String hostName = "your_server.database.windows.net";
           String dbName = "your_database";
           String user = "your_username";
           String password = "your_password";
           String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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
                    connection.close();
                   }                   
           }
           catch (Exception e) {
                   e.printStackTrace();
           }
       }
   }
   ```

## <a name="run-the-code"></a>A kód futtatása

1. Futtassa az alábbi parancsokat a parancssorban:

   ```bash
   mvn package
   mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

2. Győződjön meg arról, hogy a parancssori felület visszaadta az első 20 sort, majd zárja be az alkalmazásablakot.


## <a name="next-steps"></a>Következő lépések
- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)
- [Microsoft JDBC-illesztőprogram SQL Serverhez](https://github.com/microsoft/mssql-jdbc)
- [Problémák jelentése/kérdések](https://github.com/microsoft/mssql-jdbc/issues)


---
title: Java használata Azure SQL Database-adatbázis lekérdezéséhez | Microsoft Docs
description: Bemutatja, hogyan használható a Java egy Azure SQL Database-adatbázishoz csatlakozó program létrehozásához, és lekérdezi a T-SQL-utasításokkal.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/20/2018
ms.openlocfilehash: 6b748ebdbdc443c5fa44c82323774cd12c82ba38
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55197500"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Gyors útmutató: Java használata Azure SQL Database-adatbázis lekérdezéséhez

Ez a cikk bemutatja, hogyan használható [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) csatlakozni egy Azure SQL Database-adatbázishoz. Ezután használhatja a T-SQL-utasítások használatával adatokat lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

Ez a minta, győződjön meg arról, hogy rendelkezik a következő előfeltételek vonatkoznak:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- Java-kapcsolódó szoftverek az operációs rendszer telepítve:

  - **MacOS**, telepítse a homebrew-t és a Java, majd telepítse a Mavent. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  - **Ubuntu**, telepítheti a javát, a Java Development Kitet, majd telepítse a Mavent. Lásd az [1.2, 1.3 és 1.4 lépést](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  - **Windows**, telepítheti a javát, majd telepítse a Mavent. Lásd az [1.2 és 1.3 lépést](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

## <a name="get-database-connection"></a>Adatbázis-kapcsolat beolvasása

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="create-the-project"></a>A projekt létrehozása

1. A parancssor használatával hozzon létre egy új nevű Maven-projektet *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. A mappa módosítása *sqltest* , és nyissa meg *pom.xml* a kedvenc szövegszerkesztőjével. Adja hozzá a **Microsoft JDBC-illesztőprogram SQL Serverhez** , a következő kód használatával a projekt függőségeit.

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. A *pom.xml* fájlban adja hozzá a következő tulajdonságokat a projekthez. Ha nincs tulajdonságok szakasz, a függőségek után adhatja hozzá őket.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Mentse és zárja be a *pom.xml* fájlt.

## <a name="add-code-to-query-database"></a>Adja hozzá a kódot az adatbázis lekérdezése

1. Már rendelkezik egy nevű fájlt *App.java* a Maven-projektben található:

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Nyissa meg a fájlt, és cserélje le a tartalmát a következő kódra. Majd adja hozzá a megfelelő értékeket a kiszolgáló, adatbázis, a felhasználó és a jelszavát.

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
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
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

   > [!NOTE]
   > A példakód az **AdventureWorksLT** mintaadatbázist az Azure SQL.

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa az alkalmazást.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Ellenőrizze az első 20 sort adja vissza, és az alkalmazás ablak bezárásához.

## <a name="next-steps"></a>További lépések

- [Az első SQL Database-adatbázis megtervezése](sql-database-design-first-database.md)  

- [Microsoft JDBC-illesztőprogram SQL Serverhez](https://github.com/microsoft/mssql-jdbc)  

- [Problémák jelentése/kérdések](https://github.com/microsoft/mssql-jdbc/issues)  

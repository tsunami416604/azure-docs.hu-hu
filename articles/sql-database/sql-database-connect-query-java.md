---
title: Adatbázis lekérdezése a Java használatával
description: Bemutatja, hogyan használható a Java olyan program létrehozásához, amely egy Azure SQL Database-adatbázishoz csatlakozik, és T-SQL-utasítások használatával kérdezi le azokat.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: 034f92ca3b7552373ae69148d09d58d3a5dd166a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768641"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Rövid útmutató: Java használata Azure SQL-adatbázis lekérdezéséhez

Ebben a rövid útmutatóban a Java használatával csatlakozik egy Azure SQL Database-adatbázishoz, és T-SQL-utasítások használatával kérdez le adatokat.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Egy [Azure SQL Database-adatbázis](sql-database-single-database-get-started.md)
- [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)-hoz kapcsolódó szoftverek

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Telepítse a Homebrew-t és a Java-t, majd telepítse a Mavent a **1,2** -es és a **1,3** -es lépések használatával a [Java-alkalmazások létrehozása SQL Server](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Telepítse a Java, a Java fejlesztői készletet, majd telepítse a Mavent a **1,2**, **1,3**és **1,4** lépések használatával a [Java-alkalmazások létrehozása SQL Server használatával Ubuntu rendszeren](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Telepítse a Java alkalmazást, majd telepítse a Mavent a **1,2** -es és a **1,3** -es lépések használatával a [Java-alkalmazások létrehozása a Windowson SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/java/windows/)

  ---

> [!IMPORTANT]
> A cikkben található parancsfájlok az **Adventure Works** -adatbázis használatára íródnak.

> [!NOTE]
> Dönthet úgy is, hogy egy Azure SQL felügyelt példányt használ.
>
> A létrehozásához és konfigurálásához használja az [Azure Portalt](sql-database-managed-instance-get-started.md), a [PowerShellt](scripts/sql-database-create-configure-managed-instance-powershell.md)vagy a [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)-t, majd a helyszíni vagy a [VM](sql-database-managed-instance-configure-vm.md) [-](sql-database-managed-instance-configure-p2s.md) kapcsolat beállítását.
>
> Az betöltéssel kapcsolatban lásd: [visszaállítás a BACPAC](sql-database-import.md) az [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) -fájllal, vagy lásd: [a Wide World](sql-database-managed-instance-get-started-restore.md)importing-adatbázis visszaállítása.

## <a name="get-sql-server-connection-information"></a>SQL Server-kapcsolatok adatainak beolvasása

Az Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok beolvasása. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Válassza az **SQL-adatbázisok** lehetőséget, vagy nyissa meg az **SQL felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett egyetlen adatbázishoz vagy a felügyelt példányhoz tartozó **gazdagép** melletti teljes kiszolgálónévhez. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont. 

## <a name="create-the-project"></a>A projekt létrehozása

1. Hozzon létre egy új, *sqltest*nevű Maven-projektet a parancssorból.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Módosítsa a mappát *sqltest* , és nyissa meg a *Pom. xml fájlt* a kedvenc szövegszerkesztővel. Adja hozzá a **Microsoft JDBC-illesztőprogramot** a projekt függőségeihez a következő kód használatával SQL Server.

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

## <a name="add-code-to-query-database"></a>Kód hozzáadása az adatbázis lekérdezéséhez

1. A Maven-projektben már van egy *app. Java* nevű fájl, amely a következő helyen található:

   *.. \sqltest\src\main\java\com\sqldbsamples\App.java*

1. Nyissa meg a fájlt, és cserélje le a tartalmát a következő kódra. Ezután adja hozzá a kiszolgáló, az adatbázis, a felhasználó és a jelszó megfelelő értékeit.

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
   > A Code példa az Azure SQL-hoz készült **AdventureWorksLT** mintaadatbázis használatával működik.

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa az alkalmazást.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Ellenőrizze, hogy az első 20 sor vissza lett-e jelenítve, és az alkalmazás ablakának lezárása.

## <a name="next-steps"></a>Következő lépések

- [Az első Azure SQL-adatbázis megtervezése](sql-database-design-first-database.md)  

- [Microsoft JDBC-illesztőprogram SQL Serverhez](https://github.com/microsoft/mssql-jdbc)  

- [Hibák jelentése/kérdések felvetése](https://github.com/microsoft/mssql-jdbc/issues)  

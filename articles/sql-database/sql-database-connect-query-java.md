---
title: Adatbázis lekérdezése java használatával
description: Bemutatja, hogyan hozhat létre java-t egy olyan program létrehozásához, amely egy Azure SQL-adatbázishoz csatlakozik, és t-SQL-utasítások használatával lekérdezi azt.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768641"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Rövid útmutató: Java használata Azure SQL-adatbázis lekérdezéséhez

Ebben a rövid útmutatóban a Java használatával csatlakozhat egy Azure SQL-adatbázishoz, és t-SQL-utasítások használatával adatok lekérdezéséhez.

## <a name="prerequisites"></a>Előfeltételek

- Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure SQL-adatbázis](sql-database-single-database-get-started.md)
- [Java-val](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)kapcsolatos szoftver

  # <a name="macos"></a>[Macos](#tab/macos)

  Telepítse a Homebrew-t és a Java-t, majd telepítse a Maven-t az **1.2-** és **1.3-as** lépésekkel a [Java alkalmazások létrehozása az SQL Server használatával macOS rendszeren](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)című témakörben.

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Telepítse a Java-t, a Java fejlesztői készletet, majd telepítse a Mavent **az 1.2,** **1.3**és **1.4** lépésekkel a [Java alkalmazások létrehozása az SQL Server használatával ubuntuban](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Telepítse a Java-t, majd telepítse a Mavent **az 1.2-** és **1.3-as** lépésekkel a [Java-alkalmazások létrehozása az SQL Server használatával Windows rendszeren](https://www.microsoft.com/sql-server/developer-get-started/java/windows/)című témakörben.

  ---

> [!IMPORTANT]
> A cikkben szereplő parancsfájlok a **Kalandorbolt-adatbázis** használatára íródnak.

> [!NOTE]
> Igény szerint választhat egy Azure SQL felügyelt példány használata.
>
> Létrehozása és konfigurálása, használja az [Azure Portal](sql-database-managed-instance-get-started.md), [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)vagy [CLI,](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)majd állítsa be [a helyszínen](sql-database-managed-instance-configure-p2s.md) vagy [virtuális gép](sql-database-managed-instance-configure-vm.md) kapcsolat.
>
> Az adatok betöltéséhez olvassa el a [VISSZAÁLLÍTÁS a BACPAC fájllal](sql-database-import.md) című témakört a [Kalandorbolt](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) fájllal, vagy olvassa el [a Wide World Importers adatbázis visszaállítása című témakört.](sql-database-managed-instance-get-started-restore.md)

## <a name="get-sql-server-connection-information"></a>SQL-kiszolgálókapcsolati adatok beszerezése

Az Azure SQL-adatbázishoz való csatlakozáshoz szükséges kapcsolati információk beszerezése. A közelgő eljárásokhoz szüksége lesz a teljesen minősített kiszolgáló- vagy állomásnévre, az adatbázis nevére és bejelentkezési adataira.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Válassza az **SQL-adatbázisok lehetőséget,** vagy nyissa meg az **SQL felügyelt példányok lapját.**

3. Az **Áttekintés** lapon tekintse át a **kiszolgáló neve** melletti teljesen minősített kiszolgálónevet egyetlen adatbázishoz, vagy a teljesen minősített kiszolgálónevet a felügyelt példány **gazdagépe** mellett. A kiszolgáló vagy az állomásnév másolásához mutasson rá, és válassza a **Másolás ikont.** 

## <a name="create-the-project"></a>A projekt létrehozása

1. A parancssorból hozzon létre egy új Maven projektet, az *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Módosítsa a mappát *sqltest* formátumra, és nyissa meg a *pom.xml fájlt* a kedvenc szövegszerkesztőjével. Adja hozzá az **SQL Server Microsoft JDBC illesztőprogramját** a projekt függőségeihez a következő kód használatával.

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

## <a name="add-code-to-query-database"></a>Kód hozzáadása a lekérdezési adatbázishoz

1. A Maven-projektben már rendelkeznie kell egy *App.java* nevű fájllal:

   *.. \sqltest\src\main\java\com\sqldbsamples\App.java*

1. Nyissa meg a fájlt, és cserélje le a tartalmát a következő kódra. Ezután adja hozzá a kiszolgálónak, az adatbázisnak, a felhasználónak és a jelszónak megfelelő értékeket.

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
   > A példakód az **AdventureWorksLT** mintaadatbázist használja az Azure SQL-hez.

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa az alkalmazást.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Ellenőrizze, hogy a rendszer visszaadja-e a 20 legfontosabb sort, és zárja be az alkalmazásablakot.

## <a name="next-steps"></a>További lépések

- [Az első Azure SQL-adatbázis megtervezése](sql-database-design-first-database.md)  

- [Microsoft JDBC-illesztőprogram SQL Serverhez](https://github.com/microsoft/mssql-jdbc)  

- [Hibák jelentése/kérdések felvetése](https://github.com/microsoft/mssql-jdbc/issues)  

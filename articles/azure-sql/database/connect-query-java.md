---
title: Adatbázis lekérdezése a Java használatával
description: Bemutatja, hogyan használható a Java egy olyan program létrehozásához, amely Azure SQL Database-adatbázishoz kapcsolódik, és T-SQL-utasítások használatával kérdezi le.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019, sqldbrb=2 
ms.openlocfilehash: 4e5ee26b1cfa686eb501e0c6b6ba7905ea687094
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2020
ms.locfileid: "84189476"
---
# <a name="quickstart-use-java-to-query-a-database-in-azure-sql-database"></a>Gyors útmutató: adatbázis lekérdezése Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben a rövid útmutatóban a Java használatával kapcsolódhat Azure SQL Database adatbázisához, és T-SQL-utasításokkal adatokat lehet lekérdezni.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  || SQL Database | Felügyelt SQL-példány | Azure virtuális gépen futó SQL Server |
  |:--- |:--- |:---|:---|
  | Létrehozás| [Portál](single-database-create-quickstart.md) | [Portál](../managed-instance/instance-create-quickstart.md) | [Portál](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [Parancssori felület](scripts/create-and-configure-database-cli.md) | [Parancssori felület](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Konfigurálás | [Kiszolgálói szintű IP-tűzfalszabály](firewall-create-server-level-portal-quickstart.md)| [Kapcsolódás virtuális gépről](../managed-instance/connect-vm-instance-configure.md)|
  |||[Helyszíni kapcsolat](../managed-instance/point-to-site-p2s-configure.md) | [Kapcsolódás SQL Server-példányhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Adatok betöltése|Adventure Works betöltve|[Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) | [Széles körű globális importőrök visszaállítása](../managed-instance/restore-sample-database-quickstart.md) |
  |||Adventure Works visszaállítása vagy importálása [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Adventure Works visszaállítása vagy importálása [BACPAC](database-import.md) -fájlból a [githubról](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)-hoz kapcsolódó szoftverek

  # <a name="macos"></a>[macOS](#tab/macos)

  Telepítse a Homebrew-t és a Java-t, majd telepítse a Mavent a **1,2** -es és a **1,3** -es lépések használatával a [Java-alkalmazások létrehozása SQL Server MacOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/)

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Telepítse a Java alkalmazást, telepítse a Java fejlesztői készletet, majd telepítse a Mavent a **1,2**, **1,3**és **1,4** lépések használatával a [Java-alkalmazások létrehozása SQL Server használatával Ubuntu rendszeren](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Telepítse a Java-t, majd telepítse a Mavent a **1,2** -es és a **1,3** -es lépések használatával a [Java-alkalmazások létrehozása SQL Server használatával Windows rendszeren](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

  ---

> [!IMPORTANT]
> A cikkben található parancsfájlok az **Adventure Works** -adatbázis használatára íródnak.

> [!NOTE]
> Dönthet úgy is, hogy egy Azure SQL felügyelt példányt használ.
>
> A létrehozásához és konfigurálásához használja a [Azure Portal](../managed-instance/instance-create-quickstart.md), a [PowerShellt](../managed-instance/scripts/create-configure-managed-instance-powershell.md)vagy a [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)-t, majd állítsa be a helyszíni vagy [a](../managed-instance/point-to-site-p2s-configure.md) [virtuális gép](../managed-instance/connect-vm-instance-configure.md) kapcsolatát.
>
> Az betöltéssel kapcsolatban lásd: [visszaállítás a BACPAC](database-import.md) az [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) -fájllal, vagy lásd: [a Wide World](../managed-instance/restore-sample-database-quickstart.md)importing-adatbázis visszaállítása.

## <a name="get-server-connection-information"></a>Kiszolgáló-kapcsolatok adatainak beolvasása

A Azure SQL Database-adatbázishoz való kapcsolódáshoz szükséges kapcsolati adatok lekérése. A közelgő eljárásokhoz szüksége lesz a teljes kiszolgálónévre vagy az állomásnévre, az adatbázis nevére és a bejelentkezési adatokra.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Válassza az **SQL-adatbázisok** lehetőséget, vagy nyissa meg az **SQL felügyelt példányok** lapot.

3. Az **Áttekintés** lapon tekintse át a teljes kiszolgálónevet a **kiszolgáló neve** mellett Azure SQL Database vagy a **gazdagép** melletti teljes kiszolgálónevet (vagy IP-címet) egy Azure SQL felügyelt példányhoz, vagy SQL Server az Azure virtuális gépen. A kiszolgálónév vagy az állomásnév másolásához vigye a kurzort a fölé, és válassza a **Másolás** ikont.

> [!NOTE]
> SQL Server Azure-beli virtuális gépen való kapcsolódásával kapcsolatos információkért lásd: [kapcsolódás SQL Serverhoz](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

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

## <a name="add-code-to-query-the-database"></a>Kód hozzáadása az adatbázis lekérdezéséhez

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
   > A példa a **AdventureWorksLT** minta adatbázisát használja Azure SQL Databaseban.

## <a name="run-the-code"></a>A kód futtatása

1. A parancssorban futtassa az alkalmazást.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Győződjön meg arról, hogy az első 20 sor visszaadva, majd az alkalmazás ablakának bezárásával.

## <a name="next-steps"></a>További lépések

- [Az első adatbázis megtervezése Azure SQL Database](design-first-database-tutorial.md)  
- [Microsoft JDBC-illesztőprogram SQL Serverhez](https://github.com/microsoft/mssql-jdbc)  
- [Hibák jelentése/kérdések felvetése](https://github.com/microsoft/mssql-jdbc/issues)  

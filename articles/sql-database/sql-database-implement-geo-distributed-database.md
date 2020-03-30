---
title: Földrajzilag elosztott megoldás megvalósítása
description: Ismerje meg az Azure SQL-adatbázis és alkalmazás konfigurálása a feladatátvétel egy replikált adatbázisba, és a feladatátvétel tesztelése.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: 58d5bd4a7f3087e11056354f7534c3c9dbebca3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067292"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Oktatóanyag: Földrajzilag elosztott adatbázis megvalósítása

Konfiguráljon egy Azure SQL-adatbázist és -alkalmazást egy távoli régióba való feladatátvételhez, és tesztelje a feladatátvételi tervet. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - [Feladatátvételi csoport](sql-database-auto-failover-group.md) létrehozása
> - Java-alkalmazás futtatása Azure SQL-adatbázis lekérdezéséhez
> - Feladatátvétel tesztelése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

Az oktatóanyag befejezéséhez győződjön meg arról, hogy telepítette a következő elemeket:

- [Azure PowerShell](/powershell/azureps-cmdlets-docs)
- Egyetlen adatbázis az Azure SQL Database-ben. Egyetlen használat létrehozásához
  - [Portál](sql-database-single-database-get-started.md)
  - [parancssori felület](sql-database-cli-samples.md)
  - [Powershell](sql-database-powershell-samples.md)

  > [!NOTE]
  > Az oktatóanyag az *AdventureWorksLT* mintaadatbázist használja.

- Java és Maven, lásd: [Alkalmazás létrehozása az SQL Server használatával,](https://www.microsoft.com/sql-server/developer-get-started/)jelölje ki a **Java,és** válassza ki a környezetet, majd kövesse a lépéseket.

> [!IMPORTANT]
> Győződjön meg arról, hogy tűzfalszabályokat állít be annak a számítógépnek a nyilvános IP-címéhez, amelyen az oktatóanyag lépéseit hajtja végre. Az adatbázis-szintű tűzfalszabályok automatikusan replikálódnak a másodlagos kiszolgálóra.
>
> További információt az [Adatbázis-szintű tűzfalszabály létrehozása](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) vagy a számítógép kiszolgálószintű tűzfalszabályához használt IP-cím meghatározásáról a [Kiszolgálószintű tűzfal létrehozása című](sql-database-server-level-firewall-rule.md)témakörben talál.  

## <a name="create-a-failover-group"></a>Feladatátvételi csoport létrehozása

Az Azure PowerShell használatával [feladatátvételi csoportokat](sql-database-auto-failover-group.md) hozhat létre egy meglévő Azure SQL-kiszolgáló és egy másik régióban lévő új Azure SQL-kiszolgáló között. Ezután adja hozzá a mintaadatbázist a feladatátvételi csoporthoz.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!IMPORTANT]
> [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]

Feladatátvételi csoport létrehozásához futtassa a következő parancsfájlt:

```powershell
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>"
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
New-AzSqlServer -ResourceGroupName $resourceGroup -ServerName $drServer `
    -Location $drLocation -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
    -ArgumentList $admin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))

# create a failover group between the servers
New-AzSqlDatabaseFailoverGroup –ResourceGroupName $resourceGroup -ServerName $server `
    -PartnerServerName $drServer –FailoverGroupName $failoverGroup –FailoverPolicy Automatic -GracePeriodWithDataLossHours 2

# add the database to the failover group
Get-AzSqlDatabase -ResourceGroupName $resourceGroup -ServerName $server -DatabaseName $database | `
    Add-AzSqlDatabaseToFailoverGroup -ResourceGroupName $resourceGroup -ServerName $server -FailoverGroupName $failoverGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

> [!IMPORTANT]
> Futtassa `az login` a bejelentkezéshez az Azure-ba.

```azurecli
$admin = "<adminName>"
$password = "<password>"
$resourceGroup = "<resourceGroupName>"
$location = "<resourceGroupLocation>"
$server = "<serverName>"
$database = "<databaseName>"
$drLocation = "<disasterRecoveryLocation>" # must be different then $location
$drServer = "<disasterRecoveryServerName>"
$failoverGroup = "<globallyUniqueFailoverGroupName>"

# create a backup server in the failover region
az sql server create --admin-password $password --admin-user $admin `
    --name $drServer --resource-group $resourceGroup --location $drLocation

# create a failover group between the servers
az sql failover-group create --name $failoverGroup --partner-server $drServer `
    --resource-group $resourceGroup --server $server --add-db $database `
    --failover-policy Automatic --grace-period 2
```

* * *

A georeplikációs beállítások az Azure Portalon is módosíthatók, ha kiválasztja az adatbázist, majd a Beállítások**georeplikációt.** **Settings** > 

![Georeplikációs beállítások](./media/sql-database-implement-geo-distributed-database/geo-replication.png)

## <a name="run-the-sample-project"></a>A mintaprojekt futtatása

1. A konzolon hozzon létre egy Maven-projektet a következő paranccsal:

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

1. Írja be **az Y** értéket, és nyomja le az **Enter billentyűt.**

1. Könyvtárak módosítása az új projektre.

   ```bash
   cd SqlDbSample
   ```

1. Kedvenc szerkesztőjével nyissa meg a *pom.xml* fájlt a projekt mappájában.

1. Adja hozzá a Microsoft JDBC-illesztőprogramot az `dependency` SQL Server-függőséghez a következő szakasz hozzáadásával. A függőséget a nagyobb `dependencies` szakaszba kell illeszteni.

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

1. Adja meg a Java `properties` verziót `dependencies` a szakasz után a következő szakasz hozzáadásával:

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. A jegyzékfájlok támogatása `build` a `properties` szakasz után a következő szakasz hozzáadásával:

   ```xml
   <build>
     <plugins>
       <plugin>
         <groupId>org.apache.maven.plugins</groupId>
         <artifactId>maven-jar-plugin</artifactId>
         <version>3.0.0</version>
         <configuration>
           <archive>
             <manifest>
               <mainClass>com.sqldbsamples.App</mainClass>
             </manifest>
           </archive>
        </configuration>
       </plugin>
     </plugins>
   </build>
   ```

1. Mentse és zárja be a *pom.xml* fájlt.

1. Nyissa meg *a* .. \SqlDbSample\src\main\java\com\sqldbsamples, és cserélje le a tartalmát a következő kódra:

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String FAILOVER_GROUP_NAME = "<your failover group name>";  // add failover group name
  
      private static final String DB_NAME = "<your database>";  // add database name
      private static final String USER = "<your admin>";  // add database user
      private static final String PASSWORD = "<your password>";  // add database password

      private static final String READ_WRITE_URL = String.format("jdbc:" +
         "sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:" +
         "sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;" +
         "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", +
         FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println("");

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " +
                   (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " +
                   (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name so we can find the product again
      String sql = "INSERT INTO SalesLT.Product " +
         "(Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(READ_ONLY_URL);
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id stored in the table to be able to make unique inserts
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
      try (Connection connection = DriverManager.getConnection(READ_WRITE_URL);
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```

1. Mentse és zárja be az *App.java* fájlt.

1. A parancskonzolon futtassa a következő parancsot:

   ```bash
   mvn package
   ```

1. Indítsa el az alkalmazást, amely körülbelül 1 órán át fog futni, amíg manuálisan le nem áll, így időt a feladatátvételi teszt futtatásához.

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   ```output
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ...
   ```

## <a name="test-failover"></a>Feladatátvétel tesztelése

Futtassa a következő parancsfájlokat a feladatátvétel szimulálásához és az alkalmazás eredményeinek megfigyeléséhez. Figyelje meg, hogy egyes beszúrások és kiválasztók hogyan sikertelenek lesznek az adatbázis áttelepítése során.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

A teszt során a következő paranccsal ellenőrizheti a vész-helyreállítási kiszolgáló szerepét:

```powershell
(Get-AzSqlDatabaseFailoverGroup -FailoverGroupName $failoverGroup `
    -ResourceGroupName $resourceGroup -ServerName $drServer).ReplicationRole
```

Feladatátvétel tesztelése:

1. A feladatátvételi csoport manuális feladatátvételének megkezdése:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $myresourcegroupname `
    -ServerName $drServer -FailoverGroupName $failoverGroup
   ```

1. Feladatátvételi csoport visszaállítása vissza az elsődleges kiszolgálóra:

   ```powershell
   Switch-AzSqlDatabaseFailoverGroup -ResourceGroupName $resourceGroup `
    -ServerName $server -FailoverGroupName $failoverGroup
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A teszt során a következő paranccsal ellenőrizheti a vész-helyreállítási kiszolgáló szerepét:

```azurecli
az sql failover-group show --name $failoverGroup --resource-group $resourceGroup --server $drServer
```

Feladatátvétel tesztelése:

1. A feladatátvételi csoport manuális feladatátvételének megkezdése:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $drServer
   ```

1. Feladatátvételi csoport visszaállítása vissza az elsődleges kiszolgálóra:

   ```azurecli
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   ```

* * *

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban konfigurált egy Azure SQL-adatbázist és alkalmazást egy távoli régióba, és tesztelt egy feladatátvételi tervet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Georeplikációs feladatátvételi csoport létrehozása
> - Java-alkalmazás futtatása Azure SQL-adatbázis lekérdezéséhez
> - Feladatátvétel tesztelése

Tovább a következő oktatóanyaghoz a DMS használatával történő áttelepítésről.

> [!div class="nextstepaction"]
> [SQL Server áttelepítése az Azure SQL-adatbázis felügyelt példányára a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md)

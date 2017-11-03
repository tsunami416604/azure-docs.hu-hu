---
title: "Földrajzilag elosztott Azure SQL adatbázis-megoldás megvalósítása |} Microsoft Docs"
description: "További tudnivalók az Azure SQL Database és a replikált adatbázis feladatátvétel alkalmazás konfigurálása, és a feladatátvételi teszt."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,business continuity
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: On Demand
ms.date: 05/26/2017
ms.author: carlrab
ms.openlocfilehash: 910be8ff5f9a882c7bb8ae875b8bf5fc74d1fb9a
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="implement-a-geo-distributed-database"></a>Egy földrajzilag elosztott adatbázist megvalósítása

Ebben az oktatóanyagban egy Azure SQL-adatbázis és a távoli régióban feladatátvételi alkalmazás konfigurálása, és tesztelje a feladatátvételi tervet. Az alábbiak végrehajtásának módját ismerheti meg: 

> [!div class="checklist"]
> * Adatbázis-felhasználók létrehozása és engedélyekkel rendelkeznek
> * Egy adatbázis-adatbázisszintű tűzfalszabály beállítása
> * Hozzon létre egy [georeplikáció feladatátvételi csoport](sql-database-geo-replication-overview.md)
> * Hozzon létre és lefordítani a Java-alkalmazások Azure SQL-adatbázis lekérdezésére
> * Hajtsa végre a vész-helyreállítási részletezési

Ha nem rendelkezik Azure-előfizetéssel, [ingyenes fiók létrehozását](https://azure.microsoft.com/free/) megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- Legfrissebb telepítve [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs). 
- Azure SQL-adatbázis telepítve. Ez az oktatóanyag használja a AdventureWorksLT mintaadatbázis nevű **mySampleDatabase** a gyors üzembe helyezések egyikét:

   - [DB létrehozása – portál](sql-database-get-started-portal.md)
   - [DB létrehozása – CLI](sql-database-get-started-cli.md)
   - [DB létrehozása – PowerShell](sql-database-get-started-powershell.md)

- Azonosította a metódus SQL-szkriptek használatát az adatbázis, a következő lekérdezés eszközök közül választhat:
   - A Lekérdezésszerkesztő a a [Azure-portálon](https://portal.azure.com). A lekérdezés-szerkesztő használatával az Azure portálon további információkért lásd: [kapcsolódás és lekérdezés lekérdezés-szerkesztő segítségével](sql-database-get-started-portal.md#query-the-sql-database).
   - A legújabb verziójának [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms), amely integrált környezetet bármely SQL infrastruktúra kezelése a Microsoft Windows SQL-adatbázis SQL Server.
   - A legújabb verzióját [Visual Studio Code](https://code.visualstudio.com/docs), amely egy grafikus kód szerkesztése Linux, macOS, és Windows, amely támogatja a bővítményeket, ezek közé tartoznak a [mssql bővítmény](https://aka.ms/mssql-marketplace) Microsoft SQL Server, az Azure SQL Database és az SQL Data Warehouse lekérdezése. Ezzel az eszközzel az Azure SQL Database további információkért lásd: [kapcsolódás és lekérdezés VS kóddal](sql-database-connect-query-vscode.md). 

## <a name="create-database-users-and-grant-permissions"></a>Hozzon létre az adatbázis-felhasználók és engedélyek

Kapcsolódás saját adatbázishoz, és hozzon létre felhasználói fiókokat az alábbi lekérdezés eszközök egyikével:

- A Lekérdezésszerkesztő az Azure-portálon
- SQL Server Management Studio
- Visual Studio Code

Ezek a fiókok automatikusan replikálni a másodlagos kiszolgáló (és szinkronban kell tartani). SQL Server Management Studio vagy Visual Studio Code használatához szükség lehet egy tűzfalszabály konfigurálása, ha egy IP-címen, amelynek Ön még nincs konfigurálva a tűzfal ügyfél csatlakozik. Részletes útmutató: [hozzon létre egy kiszolgálószintű tűzfalszabályt](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).

- A lekérdezési ablakban hajtható végre a következő lekérdezés futtatásával hozzon létre két felhasználói fiókokat az adatbázisban. Engedélyezi ezt a parancsfájlt **db_owner** engedélyeket a **app_admin** fiók és biztosít **kiválasztása** és **frissítés** engedélyeket a **app_user** fiók. 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>Adatbázis-szintű tűzfal létrehozása

Hozzon létre egy [adatbázis-adatbázisszintű tűzfalszabály](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) az SQL-adatbázis számára. Az adatbázis-adatbázisszintű tűzfalszabály automatikusan replikálja az Ön által létrehozott ebben az oktatóanyagban a másodlagos kiszolgáló. Az egyszerűség érdekében (a jelen oktatóanyag) a számítógép, amelyen hajtja végre a lépéseket ebben az oktatóanyagban a nyilvános IP-cím használatára. Az IP-cím, a kiszolgálószintű tűzfalszabály az aktuális számítógépen használt megállapításához lásd: [hozzon létre egy kiszolgálószintű tűzfal](sql-database-get-started-portal.md#create-a-server-level-firewall-rule).  

- Nyissa meg a lekérdezésablakban cserélje le az előző lekérdezés az alábbi lekérdezést, az IP-címek cseréje a környezetének megfelelő IP-címmel.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>Aktív georeplikáció automatikus feladatátvételi csoport létrehozása 

Azure PowerShell használatával hozzon létre egy [aktív georeplikáció automatikus feladatátvételi csoport](sql-database-geo-replication-overview.md) között a meglévő Azure SQL-kiszolgáló és az új Azure SQL server Azure-régióban üres, és adja hozzá a mintaadatbázis a feladatátvételi csoport számára.

> [!IMPORTANT]
> Ezeket a parancsmagokat az Azure PowerShell 4.0 szükséges. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. Feltöltése a PowerShell-parancsfájlok felhasználva a meglévő kiszolgáló és a mintaadatbázis változók, és adjon meg egy globális szinten egyedi érték feladatátvételi csoport neve.

   ```powershell
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   $myresourcegroupname = "<your resource group name>"
   $mylocation = "<your resource group location>"
   $myservername = "<your existing server name>"
   $mydatabasename = "mySampleDatabase"
   $mydrlocation = "<your disaster recovery location>"
   $mydrservername = "<your disaster recovery server name>"
   $myfailovergroupname = "<your unique failover group name>"
   ```

2. Hozzon létre egy üres helykiszolgáló biztonsági mentése a feladatátvételi régióban.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. Hozzon létre egy feladatátvételi csoport a két kiszolgáló között.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup `
      –ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -PartnerServerName $mydrservername  `
      –FailoverGroupName $myfailovergroupname `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $myfailovergroup   
   ```

4. A feladatátvételi csoport hozzáadása az adatbázishoz.

   ```powershell
   $myfailovergroup = Get-AzureRmSqlDatabase `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $myservername `
      -DatabaseName $mydatabasename | `
    Add-AzureRmSqlDatabaseToFailoverGroup `
      -ResourceGroupName $myresourcegroupname ` `
      -ServerName $myservername `
      -FailoverGroupName $myfailovergroupname
   $myfailovergroup   
   ```

## <a name="install-java-software"></a>Java szoftver telepítése

A jelen szakaszban ismertetett lépések feltételezik, hogy Ön rendelkezik fejlesztési tapasztalatokkal a Java használatával, az Azure SQL Database használatában pedig még járatlan. 

### <a name="mac-os"></a>**Mac OS**
Nyissa meg a terminált, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Java projektet. A **brew** és a **Maven** telepítéséhez használja a következő parancsokat: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

Részletes útmutatást telepítése és konfigurálása a Java és Maven környezet, nyissa meg a [hozza létre az SQL Server használatával alkalmazását](https://www.microsoft.com/sql-server/developer-get-started/), jelölje be **Java**, jelölje be **MacOS**, és kövesse a részletes utasításokat beállítja a Java és Maven 1.3 és 1.2-es lépésben.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Nyissa meg a terminált, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Java projektet. A **Maven** telepítéséhez használja a következő parancsokat:

```bash
sudo apt-get install maven
```

Részletes útmutatást telepítése és konfigurálása a Java és Maven környezet, nyissa meg a [hozza létre az SQL Server használatával alkalmazását](https://www.microsoft.com/sql-server/developer-get-started/), jelölje be **Java**, jelölje be **Ubuntu**, és kövesse a részletes utasításokat beállítja a Java és Maven 1.2-es, az 1.3 és az 1.4-es lépésben.

### <a name="windows"></a>**Windows**
Telepítse a [Mavent](https://maven.apache.org/download.cgi) a hivatalos telepítővel. Függőségek kezeléséhez használható Maven build, tesztelése és futtassa a Java-projektet. Részletes útmutatást telepítése és konfigurálása a Java és Maven környezet, nyissa meg a [hozza létre az SQL Server használatával alkalmazását](https://www.microsoft.com/sql-server/developer-get-started/), jelölje be **Java**, válassza a Windows, és hajtsa végre az 1.2-es és az 1.3 lépést a Java és Maven beállításával kapcsolatos részletes utasításokat.

## <a name="create-sqldbsample-project"></a>SqlDbSample projekt létrehozása

1. A parancs-konzolon (például a Bash) Maven-projekt létrehozása. 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. Típus **Y** kattintson **Enter**.
3. Az újonnan létrehozott projektben módosítsa a könyvtárat.

   ```bash
   cd SqlDbSamples
   ```

4. A kedvenc szerkesztő nyissa meg a pom.xml fájlt a projekt mappában. 

5. Adja hozzá a Microsoft SQL Server-függőségének JDBC-illesztőt a Maven projekthez ellenőrzéséhez nyissa meg kedvenc szövegszerkesztőjével és másolása és a következő sorokat beillesztése a pom.xml fájlt. Ne írja felül a meglévő értékeket a fájlban előre feltöltve. A JDBC-függőség kell illeszthető be a "függőségei" szakasz (nagyobb).   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. Adja meg a adja hozzá a következő "Tulajdonságok" szakaszban azokat a pom.xml fájlt a "függőségei" szakasz után a projekt elleni fordítási Java-verziót. 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. Adja hozzá az alábbi "Szerkesztés" szakasz után a "Tulajdonságok" című szakaszt a jegyzékfájlt támogatja a JAR-fájlok kivételével a pom.xml fájlt.       

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
8. Mentse és zárja be a pom.xml fájlt.
9. Nyissa meg a App.java fájlt (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java), és cserélje ki annak tartalmát a következő tartalmakat. Cserélje le a feladatátvételi csoport nevét a feladatátvételi csoport nevét. Ha módosította az értékeket az adatbázis nevét, a felhasználó vagy a jelszó, módosítsa ezeket az értékeket is.

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

      private static final String FAILOVER_GROUP_NAME = "myfailovergroupname";
  
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "app_user";
      private static final String PASSWORD = "ChangeYourPassword1";

      private static final String READ_WRITE_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);
      private static final String READ_ONLY_URL = String.format("jdbc:sqlserver://%s.secondary.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", FAILOVER_GROUP_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1 hour
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

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
      // Query the data that was previously inserted into the primary database from the geo replicated database
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
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
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
6. Mentse és zárja be az App.java fájlt.

## <a name="compile-and-run-the-sqldbsample-project"></a>Fordítsa le és futtassa a SqlDbSample projektet

1. A parancs konzolon hajtható végre a következő parancsot.

   ```bash
   mvn package
   ```
2. Ha elkészült, hajtsa végre a következő parancsot a (futtatás esetén körülbelül 1 óra kivéve, ha manuálisan leállítása) az alkalmazás futtatásához:

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>Hajtsa végre a vész-helyreállítási részletezési

1. Hívja meg a feladatátvételi csoport kézi feladatátvételre. 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. Az alkalmazás eredmények láthatja a feladatátvétel során. Néhány beszúrása sikertelen, a DNS-gyorsítótár frissítése közben.     

3. Megtudhatja, milyen működik-e a vész-helyreállítási kiszolgálói szerepkört.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. Feladat-visszavételre.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. Az alkalmazás eredmények láthatja a feladat-visszavétel során. Néhány beszúrása sikertelen, a DNS-gyorsítótár frissítése közben.     

6. Megtudhatja, milyen működik-e a vész-helyreállítási kiszolgálói szerepkört.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>Következő lépések

További információkért lásd: [aktív georeplikáció és feladatátvételi csoportok](sql-database-geo-replication-overview.md).

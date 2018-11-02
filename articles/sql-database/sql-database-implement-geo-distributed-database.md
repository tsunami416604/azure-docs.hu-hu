---
title: Földrajzilag elosztott Azure SQL Database-megoldás implementálása | Microsoft Docs
description: Megismerheti, hogyan konfigurálhatja az Azure SQL Database-adatbázisa és -alkalmazása feladatátvételét egy replikált adatbázisra, és tesztelheti a feladatátvételt.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 11/01/2018
ms.openlocfilehash: 2508d43e876a7e463d68eed1b1ca93ddf0d1e9d1
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913344"
---
# <a name="tutorial-implement-a-geo-distributed-database"></a>Oktatóanyag: Egy földrajzilag elosztott adatbázis implementálása

Ebben az oktatóanyagban konfigurálni fogja egy Azure SQL Database-adatbázis és -alkalmazás feladatátvételét egy távoli régióba, majd teszteli a feladatátvételi tervet. Az alábbiak végrehajtásának módját ismerheti meg: 

> [!div class="checklist"]
> * Adatbázis-felhasználók létrehozása és az engedélyeik kiosztása
> * Adatbázisszintű tűzfalszabály beállítása
> * [Georeplikációs feladatátvételi csoport](sql-database-geo-replication-overview.md) létrehozása
> * Egy Java-alkalmazás létrehozása az Azure SQL-adatbázis lekérdezéséhez
> * Vészhelyreállítási próba végrehajtása

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- Telepítve van az [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs) legújabb verziója. 
- Telepítve van egy Azure SQL-adatbázis. Ez az oktatóanyag a **mySampleDatabase** nevű AdventureWorksLT mintaadatbázist használja, amely az alábbi rövid útmutatók egyikében lett létrehozva:

   - [DB létrehozása – portál](sql-database-get-started-portal.md)
   - [DB létrehozása – CLI](sql-database-cli-samples.md)
   - [DB létrehozása – PowerShell](sql-database-powershell-samples.md)

- Azonosítani kell egy metódust, amely végrehajtja az SQL-szkripteket az adatbázison. Ezután a következő lekérdezési eszközök használhatók:
   - Az [Azure Portal](https://portal.azure.com) lekérdezésszerkesztője. További információ az Azure Portal lekérdezésszerkesztőjének használatával kapcsolatban: [Csatlakozás és lekérdezés a lekérdezésszerkesztővel](sql-database-get-started-portal.md#query-the-sql-database).
   - Az [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) legújabb verziója. Ez egy bármely SQL-infrastruktúra kezelésére alkalmas integrált környezet az SQL Servertől egészen az SQL Database for Microsoft Windowsig.
   - A [Visual Studio Code](https://code.visualstudio.com/docs) legújabb verziója. Ez egy grafikus kódszerkesztő Linux, macOS és Windows rendszerekre, amely támogatja a bővítményeket, beleértve az [mssql bővítményt](https://aka.ms/mssql-marketplace) a Microsoft SQL Server, az Azure SQL Database és az SQL Data Warehouse lekérdezéséhez. További információ az eszköz és az Azure SQL Database használatával kapcsolatban: [Csatlakozás és lekérdezés a VS Code használatával](sql-database-connect-query-vscode.md). 

## <a name="create-database-users-and-grant-permissions"></a>Adatbázis-felhasználók létrehozása és az engedélyeik kiosztása

Csatlakozzon az adatbázishoz és hozzon létre felhasználói fiókokat a következő lekérdezési eszközök valamelyikével:

- Az Azure Portal lekérdezésszerkesztője.
- SQL Server Management Studio
- Visual Studio Code

Ezeket a fiókokat a rendszer automatikusan replikálja a másodlagos kiszolgálóra (és szinkronban tartja őket). Az SQL Server Management Studio vagy a Visual Studio Code használatához szükség lehet egy tűzfalszabály konfigurálására, ha a csatlakozást egy olyan ügyfélről végzi, amelynek az IP-címéhez még nincs konfigurálva tűzfal. Részletes lépésekért lásd: [Kiszolgálószintű tűzfalszabály létrehozása](sql-database-get-started-portal-firewall.md).

- Egy lekérdezési ablakban hozzon létre két felhasználói fiókot az adatbázisban a következő lekérdezés futtatásával: Ez a szkript **db_owner** engedélyeket ad az **app_admin** fióknak, valamint **SELECT** és **UPDATE** engedélyeket az **app_user** fióknak. 

   ```sql
   CREATE USER app_admin WITH PASSWORD = 'ChangeYourPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'ChangeYourPassword1';
   --grant permission to SalesLT schema
   GRANT SELECT, INSERT, DELETE, UPDATE ON SalesLT.Product TO app_user;
   ```

## <a name="create-database-level-firewall"></a>Adatbázisszintű tűzfal létrehozása

Hozzon létre egy [adatbázisszintű tűzfalszabályt](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) az SQL-adatbázisához. Ez az adatbázisszintű tűzfalszabály automatikusan replikál az oktatóanyagban létrehozott másodlagos kiszolgálóra. Az egyszerűség érdekében (ebben az oktatóanyagban) használja azon számítógép nyilvános IP-címét, amelyen ezt az oktatóanyagot elvégzi. A jelenlegi számítógép kiszolgálószintű tűzfalszabályához használt IP-cím megállapításához lásd: [Kiszolgálószintű tűzfalszabály](sql-database-get-started-portal-firewall.md) létrehozása.  

- A megnyitott lekérdezési ablakban cserélje le az előző lekérdezést a következő lekérdezésre, és cserélje le az IP-címeket a saját környezetének megfelelő IP-címekre.  

   ```sql
   -- Create database-level firewall setting for your public IP address
   EXECUTE sp_set_database_firewall_rule @name = N'myGeoReplicationFirewallRule',@start_ip_address = '0.0.0.0', @end_ip_address = '0.0.0.0';
   ```

## <a name="create-an-active-geo-replication-auto-failover-group"></a>Aktív georeplikációs automatikus feladatátvételi csoport létrehozása 

Az Azure PowerShell használatával hozzon létre egy [aktív georeplikációs automatikus feladatátvételi csoportot](sql-database-geo-replication-overview.md) a meglévő Azure SQL-kiszolgáló és az új Azure SQL-kiszolgáló között egy Azure-régióban, majd adja hozzá a mintaadatbázist a feladatátvételi csoporthoz.

> [!IMPORTANT]
> A parancsmagok futtatásához az Azure PowerShell 4.0-s verziója szükséges. [!INCLUDE [sample-powershell-install](../../includes/sample-powershell-install-no-ssh.md)]
>

1. Töltse fel a PowerShell-szkriptek változóit a meglévő kiszolgáló és a mintaadatbázis értékeivel, és adjon meg egy globális szinten egyedi értéket a feladatátvételi csoport nevének.

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

2. Hozzon létre egy üres kiszolgálót a feladatátvételi régióban.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername `
      -Location $mydrlocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $mydrserver   
   ```

3. Hozzon létre egy feladatátvételi csoportot a két kiszolgáló között.

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

4. Adja hozzá az adatbázist a feladatátvételi csoporthoz.

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

A Java és Maven környezet telepítésével és konfigurálásával kapcsolatos részletes útmutatásért lépjen az [Alkalmazás készítése az SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/) oldalra, válassza a **Java**, majd a **MacOS** lehetőségeket, és kövesse az 1.2-es és 1.3-as lépésekben lévő részletes utasításokat a Java és a Maven beállításához.

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Nyissa meg a terminált, és navigáljon ahhoz a könyvtárhoz, ahol létre szeretné hozni a Java projektet. A **Maven** telepítéséhez használja a következő parancsokat:

```bash
sudo apt-get install maven
```

A Java és Maven környezet telepítésével és konfigurálásával kapcsolatos részletes útmutatásért lépjen az [Alkalmazás készítése az SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/) oldalra, válassza a **Java**, majd az **Ubuntu** lehetőségeket, és kövesse az 1.2-es, 1.3-as és 1.4-es lépésekben lévő részletes utasításokat a Java és a Maven beállításához.

### <a name="windows"></a>**Windows**
Telepítse a [Mavent](https://maven.apache.org/download.cgi) a hivatalos telepítővel. A Maven segít a függőségek kezelésében, valamint a Java-projektek készítésében, tesztelésében és futtatásában. A Java és Maven környezet telepítésével és konfigurálásával kapcsolatos részletes útmutatásért lépjen az [Alkalmazás készítése az SQL Server használatával](https://www.microsoft.com/sql-server/developer-get-started/) oldalra, válassza a **Java**, majd a Windows lehetőségeket, és kövesse az 1.2-es és 1.3-as lépésekben lévő részletes utasításokat a Java és a Maven beállításához.

## <a name="create-sqldbsample-project"></a>Az SqlDbSample projekt létrehozása

1. Hozzon létre egy Maven-projektet a parancskonzol (például Bash) használatával. 
   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```
2. Írja be az **Y** értéket, majd nyomja le az **Enter** billentyűt.
3. Módosítsa a könyvtárakat az újonnan létrehozott projektre.

   ```bash
   cd SqlDbSamples
   ```

4. Nyissa meg a projektmappában található pom.xml fájlt egy tetszőleges szerkesztőben. 

5. Adja hozzá a Maven-projekthez az SQL Server-függőség Microsoft JDBC-illesztőjét úgy, hogy megnyit egy tetszőleges szövegszerkesztőt, és bemásolja a következő sorokat a pom.xml fájlba. Ne írja felül a fájlban található előre megadott értékeket. A JDBC-függőséget a nagyobb „dependencies” szakaszba ( ) kell beilleszteni.   

   ```xml
   <dependency>
     <groupId>com.microsoft.sqlserver</groupId>
     <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
   </dependency>
   ```

6. Adja meg a projekt fordításához használandó Java-verziót úgy, hogy a következő „properties” szakaszt bemásolja a pom.xml fájlba a „dependencies” szakasz után. 

   ```xml
   <properties>
     <maven.compiler.source>1.8</maven.compiler.source>
     <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```
7. Adja hozzá az alábbi „build” szakaszt pom.xml fájlhoz a „properties” szakasz után a jegyzékfájlok támogatásához a JAR-fájlokban.       

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
9. Nyissa meg az App.java fájlt (C:\apache-maven-3.5.0\SqlDbSample\src\main\java\com\sqldbsamples\App.java), majd cserélje ki annak tartalmát a következőre: A feladatátvételi csoport nevét cserélje le a saját feladatátvételi csoportjának nevére. Ha módosította az adatbázis nevét, a felhasználónevet vagy a jelszót, módosítsa ezeket az értékeket is.

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

## <a name="compile-and-run-the-sqldbsample-project"></a>Az SqlDbSample projekt fordítása és futtatása

1. A parancskonzolban hajtsa végre a következő parancsot:

   ```bash
   mvn package
   ```
2. Ha végzett, hajtsa végre a következő parancsot az alkalmazás futtatásához (ez körülbelül egy órán át fut, hacsak manuálisan le nem állítják):

   ```bash
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   
   #######################################
   ## GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful
   ```

## <a name="perform-disaster-recovery-drill"></a>Vészhelyreállítási próba végrehajtása

1. Indítsa el a feladatátvételi csoport manuális feladatátvételét. 

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $mydrservername `
   -FailoverGroupName $myfailovergroupname
   ```

2. Figyelje meg az alkalmazás eredményeit a feladatátvétel során. Néhány beszúrás sikertelen lesz a DNS-gyorsítótár frissítése közben.     

3. Derítse ki, milyen szerepkört tölt be a vészhelyreállítási kiszolgáló.

   ```powershell
   $mydrserver.ReplicationRole
   ```

4. Végezze el a feladat-visszavételt.

   ```powershell
   Switch-AzureRMSqlDatabaseFailoverGroup `
   -ResourceGroupName $myresourcegroupname  `
   -ServerName $myservername `
   -FailoverGroupName $myfailovergroupname
   ```

5. Figyelje meg az alkalmazás eredményeit a feladat-visszavétel során. Néhány beszúrás sikertelen lesz a DNS-gyorsítótár frissítése közben.     

6. Derítse ki, milyen szerepkört tölt be a vészhelyreállítási kiszolgáló.

   ```powershell
   $fileovergroup = Get-AzureRMSqlDatabaseFailoverGroup `
      -FailoverGroupName $myfailovergroupname `
      -ResourceGroupName $myresourcegroupname `
      -ServerName $mydrservername
   $fileovergroup.ReplicationRole
   ```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan konfigurálhatja egy Azure SQL Database-adatbázis és -alkalmazás feladatátvételét egy távoli régióba, majd hogyan tesztelheti a feladatátvételi tervet.  Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * Adatbázis-felhasználók létrehozása és az engedélyeik kiosztása
> * Adatbázisszintű tűzfalszabály beállítása
> * Georeplikációs feladatátvételi csoport létrehozása
> * Egy Java-alkalmazás létrehozása az Azure SQL-adatbázis lekérdezéséhez
> * Vészhelyreállítási próba végrehajtása

Folytassa a következő oktatóanyaggal, SQL Server migrálása az Azure SQL Database felügyelt példány DMS használatával.

> [!div class="nextstepaction"]
>[SQL Server migrálása felügyelt Azure SQL Database-példányra a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md)


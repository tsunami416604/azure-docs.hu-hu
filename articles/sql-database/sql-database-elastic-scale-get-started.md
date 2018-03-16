---
title: "Rugalmas Adatbáziseszközöket - Azure beolvasása használatába |} Microsoft Docs"
description: "A rugalmas adatbázis-eszközök szolgáltatás az Azure SQL Database, beleértve egy egyszerű alkalmazást mintaalkalmazás alapvető ismertetése."
services: sql-database
manager: craigg
author: anumjs
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 11/16/2017
ms.author: anjangsh
ms.openlocfilehash: 28ff3f6eee2316a078badcf29e6780f3844f3a54
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="get-started-with-elastic-database-tools"></a>Ismerkedés a rugalmas adatbázis-eszközök
Ez a dokumentum bemutatja a fejlesztői élményének a [elastic database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md) segíti a minta alkalmazás futtatására. A mintaalkalmazás létrehoz egy egyszerű szilánkos alkalmazást, és felderíti az Azure SQL Database rugalmas Adatbáziseszközöket funkciójának főbb funkciók. Alkalmazási helyzetei összpontosít [shard térkép felügyeleti](sql-database-elastic-scale-shard-map-management.md), [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md), és [több shard lekérdezése](sql-database-elastic-scale-multishard-querying.md). Az ügyféloldali kódtára a .NET, valamint a Java érhető el. 

## <a name="elastic-database-tools-for-java"></a>Rugalmas Adatbáziseszközöket Java
### <a name="prerequisites"></a>Előfeltételek
* A Java fejlesztői készlet (JDK), 1,8 vagy újabb verzió
* [Maven 3](http://maven.apache.org/download.cgi)
* Az Azure rendszerben vagy egy helyi SQL Server-példány egy logikai kiszolgáló

### <a name="download-and-run-the-sample-app"></a>A minta-alkalmazás letöltése és futtatása
A JAR-fájlok létrehozása, és ismerkedjen meg a minta-projekt, tegye a következőket: 
1. Klónozott a [GitHub-tárházban](https://github.com/Microsoft/elastic-db-tools-for-java) az ügyféloldali kódtár és mintaalkalmazás tartalmazó. 

2. Szerkessze a _./sample/src/main/resources/resource.properties_ fájlt állítsa be a következőket:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. A minta projekt létrehozásához a _. / minta_ directory, a következő parancsot:

    ```
    mvn install
    ```
    
4. A minta projekt indításához a _. / minta_ directory, a következő parancsot: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Az ügyfél könyvtár képességeivel kapcsolatos további tudnivalókért kísérletezhet a különböző lehetőségek. Úgy, hogy a kód a minta app megvalósításával kapcsolatos további vizsgálatát.

    ![A java-folyamat][5]
    
Gratulálunk! Sikeresen létrehozva, és az első szilánkos alkalmazás az Azure SQL Database rugalmas Adatbáziseszközöket használatával futtassa. Visual Studio vagy SQL Server Management Studio segítségével csatlakozzon az SQL-adatbázis és a gyors tekintse meg a szilánkok, a minta hozott létre. Megfigyelheti, hogy új minta shard adatbázisok és a shard manager adatbázist a minta létrehozva. 

Az ügyféloldali kódtár hozzáadása a saját Maven project, adja hozzá a POM fájlban a következő függőséget:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Rugalmas Adatbáziseszközöket a .NET-hez 
### <a name="prerequisites"></a>Előfeltételek
* A Visual Studio 2012 vagy újabb a C#. Töltse le a szabad verzió [Visual Studio letöltések](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 vagy újabb. A legújabb verzió letöltéséhez lásd: [NuGet telepítése](http://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>A minta-alkalmazás letöltése és futtatása
Lépjen a szalagtár telepítéséhez [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). A könyvtár a következő szakaszban ismertetett mintaalkalmazás együtt települ.

Töltse le, és futtathatja, kövesse az alábbi lépéseket: 

1. Töltse le a [rugalmas adatbázis-eszközök az Azure SQL - Bevezetés a minta](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) msdn. Bontsa ki a minta egy olyan helyre, választja.

2. A projekt létrehozásához nyissa meg a *ElasticScaleStarterKit.sln* megoldást a *C#* könyvtár.

3. A megoldás a minta-projekt, nyissa meg a *app.config* fájlt. Kövesse az utasításokat a fájl az Azure SQL adatbázis-kiszolgáló nevét és a bejelentkezési adatait (felhasználónév és jelszó).

4. Hozza létre, és futtassa az alkalmazást. Amikor a rendszer kéri, engedélyezze a Visual Studio megoldás NuGet-csomagok visszaállítására. Ez a művelet a Nugetből tölti le a rugalmas adatbázis ügyféloldali kódtár legújabb verzióját.

5. Az ügyfél könyvtár képességeivel kapcsolatos további tudnivalókért kísérletezhet a különböző lehetőségek. Megjegyzés: a lépéseket, amelyek az alkalmazás tart, a konzol kimeneti, nyugodtan felfedezése, mely a kódot a háttérben.
   
    ![Állapot][4]

Gratulálunk! Sikeresen létrehozva, és az első szilánkos alkalmazás SQL Database rugalmas Adatbáziseszközöket használatával futtassa. Visual Studio vagy SQL Server Management Studio segítségével csatlakozzon az SQL-adatbázis és a gyors tekintse meg a szilánkok, a minta hozott létre. Megfigyelheti, hogy új minta shard adatbázisok és a shard manager adatbázist a minta létrehozva.

> [!IMPORTANT]
> Javasoljuk, hogy mindig használjon a Management Studio legújabb verzióját, hogy szinkronizálja a frissítések és az Azure SQL Database maradhat. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="key-pieces-of-the-code-sample"></a>A mintakód kulcsfontosságú
* **Leképezések szilánkok és shard kezelése**: A kód bemutatja, hogyan szilánkok, tartományok és lekérdezéseket a *ShardManagementUtils.cs* fájlt. További információkért lásd: [adatbázisok esetén a shard térkép manager kibővítési](http://go.microsoft.com/?linkid=9862595).  

* **Adatok függő útválasztási**: a megfelelő shard tranzakciók útválasztási látható a *DataDependentRoutingSample.cs* fájl. További információkért lásd: [adatok függő útválasztási](http://go.microsoft.com/?linkid=9862596). 

* **Több szilánkok lekérdezését**: között szilánkok lekérdezését mutatja be a *MultiShardQuerySample.cs* fájlt. További információkért lásd: [több shard lekérdezése](http://go.microsoft.com/?linkid=9862597).

* **Üres szilánkok hozzáadása**: új üres szilánkok iteratív hozzáadása végzi el a kód a *CreateShardSample.cs* fájlt. További információkért lásd: [adatbázisok esetén a shard térkép manager kibővítési](http://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Egyéb rugalmas bővítést műveletek
* **Egy meglévő shard felosztásával**: A funkció a szilánkok felosztására a felosztás egyesítéses eszköz által biztosított. További információkért lásd: [adatok kiterjesztett felhő adatbázisok közötti áthelyezése](sql-database-elastic-scale-overview-split-and-merge.md).

* **Az egyesítés meglévő szilánkok**: Shard összevonása is hajtja végre a felosztás egyesítéses eszközzel. További információkért lásd: [adatok kiterjesztett felhő adatbázisok közötti áthelyezése](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Költségek
A rugalmas Adatbáziseszközöket könyvtárban szabad. Rugalmas Adatbáziseszközöket használhat, amikor Ön többletköltségei nem meghaladja az Azure használatának költségét. 

A mintaalkalmazás például új adatbázisokat hoz létre. Ez a funkció a költségét az SQL Database edition úgy dönt, és az Azure használatát, az alkalmazás függ.

Díjszabási információkért lásd: [díjszabása SQL-adatbázis](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>További lépések
A rugalmas adatbázis-eszközökkel kapcsolatos további információkért tekintse meg a következő cikkeket:

* Kódminták: 
  * Rugalmas Adatbáziseszközöket ([.NET](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Az Azure SQL - entitás Keretrendszeri integrációját rugalmas adatbázis-eszközök](http://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [A parancsfájl-központ shard rugalmassága](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [rugalmas bővítést bejelentés](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Microsoft Virtual Academy Oktatóanyaga: [végrehajtási horizontális kibővített segítségével a rugalmas adatbázis ügyfél könyvtár videó](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554?l=lWyQhF1fC_6306218965) 
* 9. csatornán: [rugalmas bővítést áttekintő videó](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Vitafóruma: [Azure SQL Database-fórum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Teljesítmény mérésére: [shard térkép manager teljesítményszámlálói](sql-database-elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-get-started/newProject.png
[2]: ./media/sql-database-elastic-scale-get-started/click-online.png
[3]: ./media/sql-database-elastic-scale-get-started/click-CSharp.png
[4]: ./media/sql-database-elastic-scale-get-started/output2.png
[5]: ./media/sql-database-elastic-scale-get-started/java-client-library.PNG


---
title: "Ismerkedés a rugalmas adatbáziseszközöket |} Microsoft Docs"
description: "A rugalmas adatbázis eszközök szolgáltatás az Azure SQL Database, beleértve egy egyszerű alkalmazást mintaalkalmazás alapvető ismertetése."
services: sql-database
documentationcenter: 
manager: jstrauss
author: anumjs
editor: CarlRabeler
ms.assetid: b6911f8d-2bae-4d04-9fa8-f79a3db7129d
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2017
ms.author: anjangsh
ms.openlocfilehash: e7e072e310cabc2c4520df7e9f4f9e45b8218998
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2017
---
# <a name="get-started-with-elastic-database-tools"></a>Ismerkedés a rugalmas adatbázis-eszközök
Ez a dokumentum bemutatja a fejlesztők számára a [elastic database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md) segíti a minta alkalmazás futtatására. A mintaalkalmazás létrehoz egy egyszerű szilánkos alkalmazást, és felderíti a rugalmas adatbáziseszközöket főbb képességei. Alkalmazási helyzetei összpontosít [shard térkép felügyeleti](sql-database-elastic-scale-shard-map-management.md), [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md), és [több shard lekérdezése](sql-database-elastic-scale-multishard-querying.md). Az ügyféloldali kódtára a .net, valamint a Java érhető el. 

## <a name="elastic-database-tools-for-java"></a>Rugalmas adatbáziseszközöket Java
### <a name="prerequisites"></a>Előfeltételek
* A Java fejlesztői készlet (JDK), v 1,8 vagy újabb verzió
* [Maven 3](http://maven.apache.org/download.cgi)
* Egy Azure-bA vagy helyi SQL Server logikai kiszolgáló

### <a name="download-and-run-the-sample-app"></a>A minta-alkalmazás letöltése és futtatása
A JAR-fájlok létrehozása, és ismerkedjen meg a minta-projektet az alábbi lépésekkel: 
1. Klónozott a [GitHub-tárházban](https://github.com/Microsoft/elastic-db-tools-for-java) az ügyféloldali kódtár és mintaalkalmazás tartalmazó. 
2. Szerkessze a _./sample/src/main/resources/resource.properties_ állítsa be az alábbi fájlt.
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME
3. Az a _. / minta_ directory, a következő parancsot a minta-projekt létrehozásához.<br>

    ```
    mvn install
    ```
    
4. Az a _. / minta_ directory, a következő parancsot a minta projekt indításához. 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Az ügyfél könyvtár képességeivel kapcsolatos további beállításokról kísérletezhet. Úgy, hogy a kód a minta app megvalósításával kapcsolatos további vizsgálatát.

    ![A java-folyamat][5]
    
Gratulálunk! Sikeresen létrehozva, és az első szilánkos alkalmazás SQL Database rugalmas adatbáziseszközöket használatával futtassa. Visual Studio vagy SQL Server Management Studio segítségével csatlakozzon az SQL-adatbázis és a gyors tekintse meg a szilánkok, a minta hozott létre. Megfigyelheti, hogy új minta shard adatbázisok és a shard manager adatbázist a minta létrehozva. Az ügyféloldali kódtár hozzáadása a saját Maven project, adja hozzá a POM fájlban a következő függőséget.<br>

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Rugalmas adatbáziseszközöket a .NET-hez 
### <a name="prerequisites"></a>Előfeltételek
* A Visual Studio 2012 vagy újabb a C#. Töltse le a szabad verzió [Visual Studio letöltések](http://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 vagy újabb. A legújabb verzió letöltéséhez lásd: [NuGet telepítése](http://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>A minta-alkalmazás letöltése és futtatása
Lépjen a szalagtár telepítéséhez [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). A könyvtár a következő szakaszban ismertetett mintaalkalmazás együtt települ.

Töltse le, és futtathatja, kövesse az alábbi lépéseket: 

1. Töltse le a [rugalmas adatbázis-eszközök az Azure SQL - Bevezetés a minta](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) msdn. Bontsa ki a minta egy olyan helyre, választja.

2. A projekt létrehozásához nyissa meg a **ElasticScaleStarterKit.sln** megoldást a **C#** könyvtár.

3. A megoldás a minta-projekt, nyissa meg a **app.config** fájlt. Kövesse az utasításokat a fájl az Azure SQL adatbázis-kiszolgáló nevét és a bejelentkezési adatait (felhasználónév és jelszó).

4. Hozza létre, és futtassa az alkalmazást. Amikor a rendszer kéri, engedélyezze a Visual Studio megoldás NuGet-csomagok visszaállítására. Ez a rugalmas adatbázis ügyféloldali kódtár legújabb verzióját a Nugetből tölti le.

5. Az ügyfél könyvtár képességeivel kapcsolatos további beállításokról kísérletezhet. Vegye figyelembe a lépéseket az alkalmazás tart, a konzol kimeneti és nyugodtan megismerkedhet a kódot a háttérben.
   
    ![Előrehaladás][4]

Congratulations--sikeresen létrehozva, és az első szilánkos alkalmazás SQL Database rugalmas adatbáziseszközöket használatával futtassa. Visual Studio vagy SQL Server Management Studio segítségével csatlakozzon az SQL-adatbázis és a gyors tekintse meg a szilánkok, a minta hozott létre. Megfigyelheti, hogy új minta shard adatbázisok és a shard manager adatbázist a minta létrehozva.

> [!IMPORTANT]
> Javasoljuk, hogy mindig használjon a Management Studio legújabb verzióját, hogy szinkronizálja a frissítések és az Azure SQL Database maradhat. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="key-pieces-of-the-code-sample"></a>A mintakód kulcsfontosságú
* **Leképezések szilánkok és shard kezelése**: A kód bemutatja, hogyan működnek a szilánkok, a tartományokkal és a leképezéseket a fájlban **ShardManagementUtils.cs**. További információkért lásd: [adatbázisok esetén a shard térkép manager kibővítési](http://go.microsoft.com/?linkid=9862595).  

* **Adatok függő útválasztási**: a megfelelő shard tranzakciók útválasztási megjelenik-e a **DataDependentRoutingSample.cs**. További információkért lásd: [adatok függő útválasztási](http://go.microsoft.com/?linkid=9862596). 

* **Több szilánkok lekérdezését**: között szilánkok lekérdezését mutatja be a fájl **MultiShardQuerySample.cs**. További információkért lásd: [több shard lekérdezése](http://go.microsoft.com/?linkid=9862597).

* **Üres szilánkok hozzáadása**: új üres szilánkok iteratív hozzáadása végzi el a fájlban lévő kódot **CreateShardSample.cs**. További információkért lásd: [adatbázisok esetén a shard térkép manager kibővítési](http://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Egyéb rugalmas bővítést műveletek
* **A felosztás egy meglévő shard**: szilánkok vágási képességét biztosítja a **vegyes egyesítéses eszköz**. További információkért lásd: [adatok kiterjesztett felhő adatbázisok közötti áthelyezése](sql-database-elastic-scale-overview-split-and-merge.md).

* **Az egyesítés meglévő szilánkok**: Shard összevonása is használatával végrehajtott a **vegyes egyesítéses eszköz**. További információkért lásd: [adatok kiterjesztett felhő adatbázisok közötti áthelyezése](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Költségek
A rugalmas adatbáziseszközöket szabadon. Rugalmas adatbáziseszközöket használhat, ha nem érkezik a további díjakat felett az Azure használatának költségét. 

A mintaalkalmazás például új adatbázisokat hoz létre. Ez a költség SQL adatbázis-kiadás úgy dönt, és az Azure használatát, az alkalmazás függ.

Díjszabási információkért lásd: [díjszabása SQL-adatbázis](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Következő lépések
A rugalmas adatbázis-eszközökkel kapcsolatos további információkért tekintse meg a következő lapok:

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


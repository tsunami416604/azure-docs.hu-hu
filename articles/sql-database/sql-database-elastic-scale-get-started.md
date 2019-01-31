---
title: Ismerkedés az Elastic Database-eszközök – Azure |} A Microsoft Docs
description: Alapszintű magyarázata a rugalmas Adatbáziseszközök funkció az Azure SQL Database, egy egyszerű futtatható minta alkalmazást is beleértve.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: dbc3b7e2e013dc53a1e2524c44bd2229a6a1b18d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55462970"
---
# <a name="get-started-with-elastic-database-tools"></a>Ismerkedés az Elastic Database-eszközökkel

Ez a dokumentum bemutatja a fejlesztői felületet a [elastic database-ügyfélkódtár](sql-database-elastic-database-client-library.md) segít egy mintaalkalmazás futtatásához. A mintaalkalmazás egy egyszerű szilánkokra osztott alkalmazás létrehozása, és bemutatja az Azure SQL Database rugalmas Adatbáziseszközöket funkcióját rejlő lehetőségeket. Használati eset foglalkozik [szilánkleképezés-kezelés](sql-database-elastic-scale-shard-map-management.md), [Adatfüggő útválasztásnak](sql-database-elastic-scale-data-dependent-routing.md), és [több szegmensre vonatkozó lekérdezésekkel](sql-database-elastic-scale-multishard-querying.md). Az ügyféloldali kódtára a .NET, Java érhető el. 

## <a name="elastic-database-tools-for-java"></a>A Javához készült elastic Database-eszközökkel

### <a name="prerequisites"></a>Előfeltételek

* Egy Java fejlesztői készlet (JDK), 1.8-as vagy újabb verzió
* [Maven 3](http://maven.apache.org/download.cgi)
* Egy SQL Database-kiszolgáló Azure-ban vagy egy helyi SQL Server-példány

### <a name="download-and-run-the-sample-app"></a>Töltse le és futtassa a mintaalkalmazást

A JAR-fájlok létrehozása, és első lépések a mintaprojektet, tegye a következőket: 
1. Klónozás a [GitHub-adattár](https://github.com/Microsoft/elastic-db-tools-for-java) az ügyféloldali kódtár és mintaalkalmazás tartalmazó. 

2. Szerkessze a _./sample/src/main/resources/resource.properties_ a következő fájlt:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. A mintaprojekt összeállításához a a _. / minta_ könyvtárban futtassa a következő parancsot:

    ```
    mvn install
    ```
    
4. A mintaprojekt a elindításához a _. / minta_ könyvtárban futtassa a következő parancsot: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Ügyféloldali kódtár képességeivel kapcsolatos további tudnivalókért kísérletezhet a különböző lehetőségeit. Nyugodtan a kód a minta app megvalósításával kapcsolatban további információt.

    ![Folyamat – java][5]
    
Gratulálunk! Sikeresen létrehozott és az első szilánkokra osztott alkalmazás futtatása az Azure SQL Database Elastic Database-eszközök használatával. A Visual Studio vagy SQL Server Management Studio használatával csatlakozhat az SQL Database, és gyors tekintse meg a szegmensek, amelyek a minta létrehozása. Láthatja, hogy új minta bontott adatbázisokat és a egy szegmenstérkép-kezelő adatbázis, amely a minta hozott létre. 

Az ügyféloldali kódtár hozzáadása a saját Maven-projektet, adja hozzá a következő függőséget a POM-fájljába:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>.NET-hez készült elastic Database-eszközökkel

### <a name="prerequisites"></a>Előfeltételek

* Visual Studio 2012 vagy újabb a C# használatával. Töltse le az olyan díjmentes verziója, [Visual Studio letölti](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7-es vagy újabb verziója. A legújabb verzió beszerzéséhez tekintse meg a [NuGet telepítése](http://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Töltse le és futtassa a mintaalkalmazást

Telepítse a könyvtárban, lépjen a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). A könyvtár a következő szakaszban ismertetett mintaalkalmazás telepítve van.

Töltse le és futtassa a mintát, kövesse az alábbi lépéseket: 

1. Töltse le a [Elastic DB eszközök az Azure SQL - mintájához](https://code.msdn.microsoft.com/windowsapps/Elastic-Scale-with-Azure-a80d8dc6) MSDN-ről. Csomagolja ki a minta egy Ön által választott helyre.

2. Hozzon létre egy projektet, nyissa meg a *ElasticScaleStarterKit.sln* megoldást a *C#* könyvtár.

3. A mintaprojekt a megoldást, nyissa meg a *app.config* fájlt. Ezután kövesse az utasításokat a fájl az Azure SQL Database-kiszolgáló nevét és a bejelentkezési adatok (felhasználónév és jelszó).

4. Hozhat létre, és futtassa az alkalmazást. Amikor a rendszer kéri, engedélyezze a Visual Studióban, hogy a megoldás NuGet-csomagok visszaállítására. Ez a művelet NuGet tölt le az elastic database-ügyfélkódtár legújabb verzióját.

5. Ügyféloldali kódtár képességeivel kapcsolatos további tudnivalókért kísérletezhet a különböző lehetőségeit. Vegye figyelembe a lépéseket, hogy az alkalmazás a konzol kimenete, és nyugodtan a kód a háttérben.
   
    ![Előrehaladás][4]

Gratulálunk! Sikeresen létrehozott és az első szilánkokra osztott alkalmazás SQL Database Elastic Database-eszközök használatával futtassa. A Visual Studio vagy SQL Server Management Studio használatával csatlakozhat az SQL Database, és gyors tekintse meg a szegmensek, amelyek a minta létrehozása. Láthatja, hogy új minta bontott adatbázisokat és a egy szegmenstérkép-kezelő adatbázis, amely a minta hozott létre.

> [!IMPORTANT]
> Azt javasoljuk, hogy mindig használja a Management Studio legújabb verzióját, hogy az Azure és az SQL Database frissítései által marad. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>A kódminta kulcsfontosságú adatokra

* **Leképezések szegmensek és a szilánkleképezés kezelése**: A kód azt ábrázolja, hogyan-szegmens, tartományok és -hozzárendeléseket a *ShardManagementUtils.cs* fájlt. További információkért lásd: [horizontális felskálázás a szilánkleképezés-kezelővel rendelkező adatbázisok](https://go.microsoft.com/?linkid=9862595).  

* **Adatfüggő útválasztás**: A megfelelő szegmensre tranzakciók útválasztás látható a *DataDependentRoutingSample.cs* fájlt. További információkért lásd: [Adatfüggő útválasztásnak](https://go.microsoft.com/?linkid=9862596). 

* **Lekérdezés a több szegmens**: A szegmensek közötti lekérdezési mutatja be a *MultiShardQuerySample.cs* fájlt. További információkért lásd: [több szegmensre vonatkozó lekérdezésekkel](https://go.microsoft.com/?linkid=9862597).

* **Üres szegmensek hozzáadásával**: Új üres szegmensek iteratív hozzáadása végzi a kódot a *CreateShardSample.cs* fájlt. További információkért lásd: [horizontális felskálázás a szilánkleképezés-kezelővel rendelkező adatbázisok](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Egyéb rugalmas méretezési műveletek

* **Egy meglévő szegmens felosztás**: A funkció szegmensek felosztása a felosztó / egyesítő eszköz biztosítják. További információkért lásd: [adatok mozgatása kiterjesztett felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md).

* **Meglévő szegmensek egyesítése**: Szilánkleképezés összevonása is a felosztási-egyesítési eszközének használatával hajtja végre. További információkért lásd: [adatok mozgatása kiterjesztett felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md).   

## <a name="cost"></a>Költségek

A rugalmas Adatbáziseszközök kódtár használata ingyenes. Elastic Database-eszközök használata esetén nem vonatkozik külön díj az Azure-használat költségeinek túli díjak. 

Ha például a mintaalkalmazás hoz létre új adatbázisokat. Ez a funkció a költsége attól függ, az SQL Database-kiadás választja, és az alkalmazás Azure-használatát.

Díjszabási információkért tekintse meg a [SQL Database szolgáltatás díjszabásával](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>További lépések

Elastic Database-eszközökkel kapcsolatos további információkért lásd a következő cikkeket:

* Kódminták: 
  * Elastic Database-eszközök ([.NET](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-a80d8dc6?SRC=VSIDE), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Elastic Database-eszközök az Azure SQL - Entity Framework-integráció](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [A Script Centerbe a horizontális partíciók rugalmassága](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Rugalmas méretezés bejelentés](https://azure.microsoft.com/blog/2014/10/02/introducing-elastic-scale-preview-for-azure-sql-database/)
* Channel 9-on: [Rugalmas méretezés áttekintő videó](http://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Fórum: [Az Azure SQL Database-fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Teljesítmény mérésére: [Teljesítményszámlálók a szilánkleképezés-kezelőhöz](sql-database-elastic-database-client-library.md)

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


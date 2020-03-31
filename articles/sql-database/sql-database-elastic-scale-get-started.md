---
title: A rugalmas adatbázis-kezelőeszközök – első lépések
description: Az Azure SQL Database Rugalmas adatbázis-eszközök szolgáltatás ának alapvető magyarázata, beleértve egy könnyen futtatható mintaalkalmazást is.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 4577f611287a27366b1168e07565861c16fe0e38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987022"
---
# <a name="get-started-with-elastic-database-tools"></a>A rugalmas adatbázis-kezelőeszközök – első lépések

Ez a dokumentum bemutatja a [rugalmas adatbázis-ügyfélkódtár](sql-database-elastic-database-client-library.md) fejlesztői élményét azáltal, hogy segít egy mintaalkalmazás futtatásában. A mintaalkalmazás létrehoz egy egyszerű szilánkos alkalmazást, és feltárja az Azure SQL Database rugalmas adatbázis-eszközök szolgáltatásának kulcsfontosságú képességeit. A [szegmenstérkép-kezelés,](sql-database-elastic-scale-shard-map-management.md) [az adat-függő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)és a [többszegmenses lekérdezés használati eseteire](sql-database-elastic-scale-multishard-querying.md)összpontosít . Az ügyfélkódtár a .NET és a Java számára is elérhető. 

## <a name="elastic-database-tools-for-java"></a>Rugalmas adatbázis-kezelő eszközök Java-hoz

### <a name="prerequisites"></a>Előfeltételek

* Java Developer Kit (JDK), 1.8-as vagy újabb verzió
* [Maven](https://maven.apache.org/download.cgi)
* SQL Database-kiszolgáló az Azure-ban vagy egy helyi SQL Server-példány

### <a name="download-and-run-the-sample-app"></a>A mintaalkalmazás letöltése és futtatása

A JAR-fájlok létrehozásához és a mintaprojekt megkezdéséhez tegye a következőket: 
1. Klónozza az ügyfélkönyvtárat tartalmazó [GitHub-tárt](https://github.com/Microsoft/elastic-db-tools-for-java) a mintaalkalmazással együtt. 

2. A _./sample/src/main/resources/resource.properties_ fájl szerkesztésével állítsa be a következőket:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. A mintaprojekt létrehozásához futtassa a _./mintakönyvtárban_ a következő parancsot:

    ```
    mvn install
    ```
    
4. A mintaprojekt elindításához a _./mintakönyvtárban_ futtassa a következő parancsot: 
    
    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```
    
5. Ha többet szeretne megtudni az ügyfélkönyvtár képességeiről, kísérletezzen a különböző lehetőségekkel. Nyugodtan vizsgálja meg a kódot, hogy megismerjék a minta alkalmazás végrehajtását.

    ![Haladás-java][5]
    
Gratulálunk! Sikeresen megépítette és futtatta az első szilánkos alkalmazást az Azure SQL Database rugalmas adatbázis-eszközeivel. A Visual Studio vagy az SQL Server Management Studio segítségével csatlakozzon az SQL-adatbázishoz, és tekintse meg gyorsan a minta által létrehozott szilánkokat. Észre fogja venni, új minta shard adatbázisok és a shard map manager adatbázis, amely a minta által létrehozott. 

Ha hozzá szeretné adni az ügyfélkönyvtárat a saját Maven-projektjéhez, adja hozzá a következő függőséget a POM-fájlhoz:

```xml
<dependency> 
    <groupId>com.microsoft.azure</groupId> 
    <artifactId>elastic-db-tools</artifactId> 
    <version>1.0.0</version> 
</dependency> 
```

## <a name="elastic-database-tools-for-net"></a>Rugalmas adatbázis-kezelő eszközök a .NET-hez

### <a name="prerequisites"></a>Előfeltételek

* Visual Studio 2012-es vagy újabb verzió c#-val. Töltsön le egy ingyenes verziót a [Visual Studio Letöltések albumai oldalon.](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* NuGet 2.7 vagy újabb. A legújabb verzió t, [lásd: Telepítése NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>A mintaalkalmazás letöltése és futtatása

A tár telepítéséhez nyissa meg a [Microsoft.Azure.SqlDatabase.ElasticScale.Client című témakört.](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) A könyvtár a következő szakaszban ismertetett mintaalkalmazással van telepítve.

A minta letöltéséhez és futtatásához hajtsa végre az alábbi lépéseket: 

1. Töltse le az [Azure SQL rugalmas DB-eszközeit – Első lépések minta.](https://github.com/Azure/elastic-db-tools) Csomagolja ki a mintát a kiválasztott helyre.

2. Projekt létrehozásához nyissa meg az *ElasticScaleStarterKit.sln* megoldást a *C#* könyvtárból.

3. A mintaprojekt megoldásában nyissa meg az *app.config* fájlt. Ezután kövesse a fájlban található utasításokat az Azure SQL Database-kiszolgáló nevének és a bejelentkezési adatoknak (felhasználónév és jelszó) hozzáadásához.

4. Hozza létre és futtassa az alkalmazást. Amikor a rendszer kéri, engedélyezze a Visual Studio számára a megoldás NuGet-csomagjainak visszaállítását. Ez a művelet letölti a rugalmas adatbázis-ügyfélkódtár legújabb verzióját a NuGet-ből.

5. Ha többet szeretne megtudni az ügyfélkönyvtár képességeiről, kísérletezzen a különböző lehetőségekkel. Vegye figyelembe a lépéseket, hogy az alkalmazás veszi a konzol kimenetét, és nyugodtan tárja fel a kódot a színfalak mögött.
   
    ![Előrehaladás][4]

Gratulálunk! Sikeresen megépítette és futtatta az első szilánkos alkalmazást az SQL Database rugalmas adatbázis-eszközeivel. A Visual Studio vagy az SQL Server Management Studio segítségével csatlakozzon az SQL-adatbázishoz, és tekintse meg gyorsan a minta által létrehozott szilánkokat. Észre fogja venni, új minta shard adatbázisok és a shard map manager adatbázis, amely a minta által létrehozott.

> [!IMPORTANT]
> Azt javasoljuk, hogy mindig a Management Studio legújabb verzióját használja, hogy szinkronizálva maradjon az Azure és az SQL Database frissítéseivel. [Az SQL Server Management Studio frissítése](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>A kódminta legfontosabb darabjai

* **Szilánkok és shard leképezések kezelése:** A kód bemutatja, hogyan működik a szilánkok, tartományok és leképezések a *ShardManagementUtils.cs* fájlban. További információ: [Adatbázisok kiírása a shard térképkezelővel.](https://go.microsoft.com/?linkid=9862595)  

* **Adatfüggő útválasztás:** A tranzakciók útválasztása a megfelelő szegmensben jelenik meg a *DataDependentRoutingSample.cs* fájlban. További információ: [Data-dependent routing](https://go.microsoft.com/?linkid=9862596). 

* **Több szegmensek lekérdezése:** A szegmensek lekérdezése a *MultiShardQuerySample.cs* fájlban látható. További információ: [Multi-shard querying](https://go.microsoft.com/?linkid=9862597).

* **Üres szilánkok hozzáadása:** Az új üres szilánkok iteratív hozzáadását a *CreateShardSample.cs* fájlban lévő kód hajtja végre. További információ: [Adatbázisok kiírása a shard térképkezelővel.](https://go.microsoft.com/?linkid=9862595)

## <a name="other-elastic-scale-operations"></a>Egyéb rugalmas méretezési műveletek

* **Egy meglévő szegmens felosztása:** A szegmensek felosztásának képességét a felosztás-egyesítési eszköz biztosítja. További információt az [Adatok áthelyezése a kibővített felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md)című témakörben talál.

* **Meglévő szegmensek egyesítése:** A szegmensegyesítések is a felosztásos egyesítési eszközzel történnek. További információt az [Adatok áthelyezése a kibővített felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md)című témakörben talál.   

## <a name="cost"></a>Költségek

A rugalmas adatbázis-eszközök tár ingyenes. Rugalmas adatbázis-eszközök használata esetén az Azure-használat költségét meghaladó további költségek nem merülnek fel. 

A mintaalkalmazás például új adatbázisokat hoz létre. A funkció költsége a kiválasztott SQL Database-kiadástól és az alkalmazás Azure-használatától függ.

Az árképzésről az [SQL Database díjszabási részletei című témakörben talál.](https://azure.microsoft.com/pricing/details/sql-database/)

## <a name="next-steps"></a>További lépések

A rugalmas adatbázis-kezelőeszközökről az alábbi cikkekben talál további információt:

* Kódminták: 
  * Rugalmas adatbázis-kezelő eszközök ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Rugalmas adatbázis-eszközök az Azure SQL-hez – Entitáskeretrendszer-integráció](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE)
  * [Szilánk rugalmassága a Script Centeren](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Rugalmas skála bejelentése](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/)
* Channel 9: [Elastic Scale áttekintő videó](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Vitafórum: [Az Azure SQL Database fóruma](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted)
* Teljesítmény mérése: [Teljesítményszámlálók a shard map managerhez](sql-database-elastic-database-client-library.md)

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


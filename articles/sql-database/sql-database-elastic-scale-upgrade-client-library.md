---
title: Frissítés a legújabb elastic database-ügyfélkódtár |} A Microsoft Docs
description: Frissítési elastic database-ügyfélkódtár Nuget használatával.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c91650000400d3564a6d42aecdfb0974c4a64022
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715924"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Alkalmazás használata a legújabb rugalmas adatbázis ügyfélkönyvtárának frissítése
Új verziói az [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md) NuGetand a NuGetPackage Manager felület a Visual Studióban keresztül érhető el. Frissítések hibajavításokat tartalmaz, és új funkciók az ügyféloldali kódtár támogatja.

**A legújabb verzió:** Ugrás [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Az alkalmazás és az új, valamint az új funkciók támogatásához az Azure SQL-adatbázisokban tárolt meglévő Szilánkleképezés-kezelővel-metaadatok módosításához.

Ezek a lépések végrehajtása sorrendben biztosítja, hogy az ügyféloldali kódtár régi verziói már nem találhatók a környezetben metaadat-objektum frissítésekor, ami azt jelenti, hogy a régi verziójú metaadat-objektumokat, nem kell létrehozni a frissítés után.   

## <a name="upgrade-steps"></a>Frissítési lépései
**1. Frissítse az alkalmazások.** A Visual Studióban töltse le, és a ügyféloldali kódtár legújabb hivatkozhat a fejlesztési projektek Library; szalagtárat használó összes Ezután építse újra, és üzembe helyezheti. 

* A Visual studióban, válassza ki **eszközök** --> **NuGet-Csomagkezelő** -->  **NuGet-csomagok kezelése megoldáshoz**. 
* (A visual Studio 2013) A bal oldali panelen válassza ki a **frissítések**, majd válassza ki a **frissítés** gombra a csomag **Azure SQL Database rugalmas méretezési ügyféloldali kódtár** , hogy az ablakban jelenik meg.
* (A visual Studio 2015) Állítsa be a Szűrő mezőbe **frissítése elérhető**. Válassza ki a csomagot, majd kattintson a **frissítése** gombra.
* (A visual Studio 2017) A párbeszédpanel tetején válassza **frissítések**. Válassza ki a csomagot, majd kattintson a **frissítése** gombra.
* Fejleszthet és telepíthet. 

**2. Frissítse a parancsfájlokat.** Ha használ **PowerShell** kezelheti a szegmenseket, parancsfájlok [töltse le az új erőforrástár-verzió](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) , és másolja a könyvtárba, ahonnan a szkriptek végrehajtása. 

**3. A felosztási-egyesítési szolgáltatás frissítése.** Ha az elastic database felosztási-egyesítési eszközének használatával horizontálisan skálázott adatok átrendezése [töltse le és telepítse a legfrissebb verziót az eszköz](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). A szolgáltatás megtalálható a részletes frissítési lépések [Itt](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. Frissítse a Szilánkleképezés-kezelővel adatbázisok**. Frissítse a metaadatokat a Szegmenstérképet támogatása az Azure SQL Database-ben.  Ez elvégezhető, PowerShell vagy a C# használatával két módja van. Mindkét lehetőség alatt jelennek meg.

***1. lehetőség: A frissítési metaadatokat PowerShell-lel***

1. Töltse le a legújabb parancssori segédprogramot a NuGet [Itt](http://nuget.org/nuget.exe) és a egy mappába menti. 
2. Nyisson meg egy parancssort, keresse meg az ugyanabban a mappában, és adja ki a parancsot: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Keresse meg a kívánt almappába, amely tartalmazza az új ügyfél DLL verziója csak letöltötte, például: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Töltse le a szkriptlet frissítési rugalmas adatbázis ügyfél a [Script Center](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9), és mentse a dll-fájlt tartalmazó ugyanabba a mappába.
5. A mappából a parancssorból futtassa a "PowerShell.\upgrade.ps1", és kövesse az utasításokat.

***2. lehetőség: A frissítési metaadatokat C# használatával***

Alternatív megoldásként hozzon létre egy Visual Studio alkalmazás, amely megnyitja a ShardMapManager, minden szegmensre ismétel, és a metaadat-frissítést végez a metódusok meghívásával [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) és [UpgradeGlobalStore ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) ebben a példában látható módon: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Metaadat-frissítéseket a technikák ugyanúgy alkalmazhatók többször kihagyása kárt nélkül. Például ha egy régebbi ügyfélverzió véletlenül szegmensek hoz létre, már frissítése után, frissítést futtathatja újra minden szegmensben, győződjön meg arról, hogy megtalálható-e a legújabb metaadatait az infrastruktúra egészében. 

**Megjegyzés:** új verziói az ügyféloldali kódtár közzétett dátumig továbbra is előzetes verziói a Szilánkleképezés-kezelővel metadata dolgozhat az Azure SQL DB, és fordítva.   Azonban kihasználhassa az új funkciók némelyike a legfrissebb ügyfélszoftvert, metaadatok kell frissíteni.   Vegye figyelembe, hogy metaadat-frissítések nem lesz hatással, bármely felhasználó- és alkalmazás-specifikus adatokat, csak olyan objektumok, a Szilánkleképezés-kezelő által létrehozott és használt.  És alkalmazások továbbra is megfelelően működjenek a frissítési sorrend a fent leírt keresztül. 

## <a name="elastic-database-client-version-history"></a>Rugalmas adatbázis-ügyfél korábbi verziók
Lépjen a korábbi verziók [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png


---
title: Frissítsen a legújabb elastic database ügyféloldali kódtára a |} Microsoft Docs
description: A nugettel frissítési elastic database ügyféloldali kódtárára.
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 5e7702c164a414ee2b9c08ccb276d5d74ad90f19
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>A legújabb elastic database ügyféloldali kódtár használata az alkalmazások frissítése
Az új verzióit a [Elastic Database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md) NuGetand a Visual Studio a NuGetPackage Manager felületén keresztül elérhető. Frissítések hibajavításokat tartalmaz, és új képességeket az ügyféloldali kódtár támogatja.

**A legújabb verzió:** Ugrás [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

Az alkalmazás az új gyűjteménnyel, valamint módosíthatja a meglévő Shard térkép Manager metaadatok új funkciók támogatásához az Azure SQL-adatbázisban tárolja.

Ezeket a lépéseket hajtja végre sorrendben biztosítja, hogy az ügyféloldali kódtár régi verziói már nem telepítve legyen a környezetben metaadat-objektum frissítésekor, ami azt jelenti, hogy a régi verziójú metaadat-objektum nem jön létre a frissítés után.   

## <a name="upgrade-steps"></a>Frissítési lépések
**1. Az alkalmazások frissítése.** A Visual Studio töltse le, és az ügyfél legújabb könyvtárverzió hivatkozik a fejlesztési projektek; a szalagtárat használó összes majd újraépítése, és telepítheti. 

* Válassza ki a Visual Studio megoldás **eszközök** --> **NuGet-Csomagkezelő** -->  **NuGet-csomagok kezelése megoldáshoz**. 
* (A visual Studio 2013) A bal oldali panelen válassza ki a **frissítések**, majd válassza ki a **frissítés** gombra a csomag **Azure SQL Database rugalmas méretezési ügyféloldali kódtár** , amely megjelenik az ablakban.
* (A visual Studio 2015) A Szűrő mezőbe beállítása **elérhető frissítés**. Válassza ki a csomagot, majd kattintson a **frissítése** gombra.
* (A visual Studio 2017) A párbeszédpanel tetején válassza **frissítések**. Válassza ki a csomagot, majd kattintson a **frissítése** gombra.
* Hozza létre és telepítheti. 

**2. Frissítse a parancsfájlokat.** Használata **PowerShell** parancsfájlokat, amelyek kezelik a szilánkok, [töltse le az új szalagtár](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) , és másolja a könyvtárba, amelyből végre parancsfájlokat. 

**3. Frissítse a felosztás egyesítéses szolgáltatást.** Ha eszközzel a rugalmas adatbázis vegyes egyesítéses horizontálisan skálázott adatok átszervezése [töltse le és telepítse a legfrissebb verziót az eszköz](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/). A szolgáltatás található a frissítési lépések részletes [Itt](sql-database-elastic-scale-overview-split-and-merge.md). 

**4. Frissítse a Shard térkép Manager adatbázisainak**. A metaadatok a Shard Maps támogatása az Azure SQL-adatbázis frissítéséhez.  Ez elvégezhető, PowerShell vagy a C# segítségével két módja van. Mindkét lehetőség alább láthatók.

***1. lehetőség: A frissítési metaadatok PowerShell használatával***

1. A legújabb parancssori segédprogramot letölteni a NuGet [Itt](http://nuget.org/nuget.exe) és egy mappába. 
2. Nyisson meg egy parancssort, keresse meg az ugyanabban a mappában, és adja ki a parancsot: `nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Keresse meg az imént letöltött, például új ügyfél DLL-fájl verzióját tartalmazó almappa: `cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. A rugalmas adatbázis ügyfél frissítési szkriptlet a töltse le a [Script Center](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9), és mentse a dll-fájlt tartalmazó ugyanabba a mappába.
5. Ebből a könyvtárból "PowerShell.\upgrade.ps1" futtassa a parancssorból, és kövesse az utasításokat.

***2. lehetőség: A frissítési metaadatok használatával C#***

Alternatív megoldásként hozzon létre egy Visual Studio alkalmazás, amely megnyitja a ShardMapManager elemein végiglépkedve összes szilánkok és a metaadatok frissítést végez a metódusok meghívása [UpgradeLocalStore](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore.aspx) és [UpgradeGlobalStore ](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore.aspx) ebben a példában látható módon: 

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy); 
    smm.UpgradeGlobalStore(); 

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations()) 
    {   
       smm.UpgradeLocalStore(loc); 
    } 

Ezek a technológiák a metaadatok frissítésekre alkalmazható többször kárt nélkül. Például ha egy régebbi ügyfélverzió véletlenül egy shard hoz létre, miután már frissítette, frissítést futtathatja újra közötti összes szilánkok gondoskodjon arról, hogy a legújabb metaadat az infrastruktúra egészében. 

**Megjegyzés:** új az ügyféloldali kódtár közzétett-date folytatja a munkát a Shard térkép Manager metaadatok előzetes verziói a Azure SQL Database, és fordítva.   Azonban az előnyeit néhány új szolgáltatását a legújabb ügyfélprogram, metaadatok frissíteni kell.   Vegye figyelembe, hogy metaadat-frissítések nem érinti a felhasználó-adatokat és vonatkozó adatokat, csak objektumok a Shard térkép Manager által létrehozott és használt.  És alkalmazások folytatják működésüket egészen a frissítési sorrend a fent leírt keresztül. 

## <a name="elastic-database-client-version-history"></a>A rugalmas adatbázis ügyfél verziójának előzményei
Verzióelőzmények, látogasson el [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png


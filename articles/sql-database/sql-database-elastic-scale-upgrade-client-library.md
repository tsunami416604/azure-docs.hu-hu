---
title: Frissítés a legújabb rugalmas adatbázis-ügyféltárra
description: A Nuget segítségével frissítse a rugalmas adatbázis-ügyfélkódtárat.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: a1ab684f13c56698d4359f2bf74826f3dd696c6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823515"
---
# <a name="upgrade-an-app-to-use-the-latest-elastic-database-client-library"></a>Alkalmazás frissítése a legújabb rugalmas adatbázis-ügyféltár használatához

A Rugalmas [adatbázis ügyfélkódtár](sql-database-elastic-database-client-library.md) új verziói a NuGet és a Visual Studio NuGet Package Manager felületén keresztül érhetők el. A frissítések hibajavításokat és az ügyfélkönyvtár új képességeinek támogatását tartalmazzák.

**A legújabb verzió:** Látogasson el a [Microsoft.Azure.SqlDatabase.ElasticScale.Client elemre.](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

Építse újra az alkalmazást az új kódtárral, és módosítsa a Shard Map Manager meglévő metaadatait az Azure SQL-adatbázisaiban az új funkciók támogatásához.

Ezeket a lépéseket úgy hajthatja végre, hogy az ügyfélkódtár régi verziói ne legyenek többé jelen a környezetben a metaadat-objektumok frissítésekor, ami azt jelenti, hogy a régi verziójú metaadat-objektumok nem jönnek létre a frissítés után.

## <a name="upgrade-steps"></a>Frissítési lépések

**1. Frissítse alkalmazásait.** A Visual Studio alkalmazásban töltse le és hivatkozzon a legújabb ügyféltár-verzióra a műsortárat használó összes fejlesztési projektbe; majd újraépítse és telepítse.

* A Visual Studio-megoldásban válassza az **Eszközök** --> **NuGet csomagkezelő** -->  **a Megoldáshoz csomagkezelése lehetőséget.**
* (Visual Studio 2013) A bal oldali panelen válassza a **Frissítések**lehetőséget, majd az **Ablakban** megjelenő Azure SQL Database Elastic Scale Client Library frissítés **gombját.**
* (Visual Studio 2015) Állítsa be a Szűrő jelölőnégyzetet **az elérhető frissítés beállításra.** Jelölje ki a frissíteni kívánt csomagot, és kattintson a **Frissítés** gombra.
* (Visual Studio 2017) A párbeszédpanel tetején válassza a **Frissítések**lehetőséget. Jelölje ki a frissíteni kívánt csomagot, és kattintson a **Frissítés** gombra.
* Build és üzembe helyezés.

**2. Frissítsd a szkripteket.** Ha **PowerShell-parancsfájlok** használatával kezeli a szegmensek, [töltse le az új könyvtárverziót,](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) és másolja be a könyvtárba, amelyből parancsfájlokat hajt végre.

**3. Frissítse a split-merge szolgáltatást.** Ha a rugalmas adatbázis-felosztott egyesítési eszközzel átszervezi a szilánkos adatokat, [töltse le és telepítse az eszköz legújabb verzióját.](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/) A szolgáltatás részletes frissítési lépései [itt](sql-database-elastic-scale-overview-split-and-merge.md)találhatók.

**4. Frissítse a Shard Map Manager adatbázisokat**. Frissítse a szegmenstérképeket támogató metaadatokat az Azure SQL Database-ben.  Ezt kétféleképpen valósíthatja meg a PowerShell vagy a C#használatával. Mindkét lehetőség az alábbiakban látható.

***1. lehetőség: A metaadatok frissítése a PowerShell használatával***

1. Töltse le a NuGet legújabb parancssori segédprogramját [innen,](https://nuget.org/nuget.exe) és mentse egy mappába.
2. Nyisson meg egy parancssort, keresse meg ugyanazt a mappát, és adja ki a parancsot:`nuget install Microsoft.Azure.SqlDatabase.ElasticScale.Client`
3. Keresse meg az új ügyfél DLL-verzióját tartalmazó almappát, például:`cd .\Microsoft.Azure.SqlDatabase.ElasticScale.Client.1.0.0\lib\net45`
4. Töltse le a rugalmas adatbázis-ügyfélfrissítési parancsfájlt a [Parancsfájlközpontból,](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-Database-Elastic-6442e6a9)és mentse ugyanabba a Mappába, amely tartalmazza a DLL-t.
5. Ebből a mappából futtassa a "PowerShell .\upgrade.ps1" parancsot a parancssorból, és kövesse az utasításokat.

***2. lehetőség: A metaadatok frissítése C használatával #***

Másik lehetőségként hozzon létre egy Visual Studio-alkalmazást, amely megnyitja a ShardMapManager-t, végigélaz összes szegmensen, és végrehajtja a metaadat-frissítést az [UpgradeLocalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradelocalstore) és a [UpgradeGlobalStore](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.upgradeglobalstore) metódusok hívásával, mint ebben a példában:

    ShardMapManager smm =
       ShardMapManagerFactory.GetSqlShardMapManager
       (connStr, ShardMapManagerLoadPolicy.Lazy);
    smm.UpgradeGlobalStore();

    foreach (ShardLocation loc in
     smm.GetDistinctShardLocations())
    {
       smm.UpgradeLocalStore(loc);
    }

Ezek a metaadat-frissítésekre szolgáló technikák többször is alkalmazhatók károsodás nélkül. Ha például egy régebbi ügyfélverzió véletlenül létrehoz egy szegmenst, miután már frissítette, újra futtathatja a frissítést az összes szegmensben, hogy a legújabb metaadat-verzió jelen legyen az infrastruktúra egészében.

**Megjegyzés:**  Az ügyfélkódtár eddig közzétett új verziói továbbra is működnek a Shard Map Manager metaadatainak korábbi verzióival az Azure SQL DB-n, és fordítva.   A legújabb ügyfél néhány új funkciójának kihasználásához azonban frissíteni kell a metaadatokat.   Vegye figyelembe, hogy a metaadat-frissítések nem érinti a felhasználói adatok vagy alkalmazás-specifikus adatok, csak a Shard Map Manager által létrehozott és használt objektumok.  És az alkalmazások továbbra is működnek a fent leírt frissítési sorrenden keresztül.

## <a name="elastic-database-client-version-history"></a>Rugalmas adatbázis-ügyfél verzióelőzményei

A verzióelőzményekről a [Microsoft.Azure.SqlDatabase.ElasticScale.Client webhelyen található.](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]:./media/sql-database-elastic-scale-upgrade-client-library/nuget-upgrade.png

---
title: "Kiterjesztett felhő adatbázisok kezelése |} Microsoft Docs"
description: "A rugalmas adatbázis feladat szolgáltatás segítségével hajtható végre a parancsfájl adatbázisok csoportja között."
metakeywords: azure sql database elastic databases
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
ms.assetid: 6fa47cf2-1162-4534-a206-6e2d95b78580
ms.service: sql-database
ms.custom: scale out apps
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: f709cd38a690ba666ca290cc029caa2ce4f9dff0
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="managing-scaled-out-cloud-databases"></a>Kiterjesztett felhő adatbázisok kezelése
Kiterjesztett szilánkos adatbázisok, kezelheti a **rugalmas adatbázis-feladatok** (előzetes verzió) funkciója lehetővé teszi megbízhatóan között egy csoportot az adatbázisok, beleértve a Transact-SQL (T-SQL) parancsprogram végrehajtása:

* (az alábbiakban ismertetett) adatbázisok egyénileg definiált gyűjteménye
* az összes adatbázis egy [rugalmas készlet](sql-database-elastic-pool.md)
* a shard készletének (használatával létrehozott [Elastic Database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md)). 

## <a name="documentation"></a>Dokumentáció
* [A rugalmas adatbázis-feladat összetevők telepítéséhez](sql-database-elastic-jobs-service-installation.md). 
* [Ismerkedés a rugalmas feladatok](sql-database-elastic-jobs-getting-started.md).
* [PowerShell-lel feladatok létrehozásához és kezeléséhez](sql-database-elastic-jobs-powershell.md).
* [Létrehozásához és kezeléséhez kimenő Azure SQL-adatbázisok méretezése](sql-database-elastic-jobs-getting-started.md)

**Rugalmas adatbázis-feladatok** jelenleg egy ügyfél által szolgáltatott, Azure Cloud Service, amely lehetővé teszi az ad hoc és ütemezett felügyeleti feladatokat, melyekhez nevezzük végrehajtása **feladatok**. A feladatok könnyen, és megbízhatóan kezelése az Azure SQL-adatbázisok nagy mennyiségű felügyeleti műveletek elvégzéséhez Transact-SQL-parancsfájlok futtatásával. 

![Rugalmas feladat szolgáltatás][1]

## <a name="why-use-jobs"></a>Miért érdemes használni a feladatokat?
**Kezelés**

Egyszerűen tegye sémamódosítások, hitelesítő adatok kezelése, hivatkozás adatfrissítések, teljesítményadat-gyűjtés vagy bérlő (ügyfél) telemetriai adatok gyűjtése.

**Jelentések**

Egy Azure SQL-adatbázisok gyűjteményét egyetlen táblát összesített adatokat.

**Terhelés csökkentése**

Alapesetben az egyes adatbázis-egymástól függetlenül ahhoz, hogy a Transact-SQL-utasítások futtatása vagy más felügyeleti feladatok kell csatlakoztatni. Egy feladat kezeli a feladat a célcsoportban minden adatbázis történő bejelentkezéshez. Akkor is meghatározását, karbantartása és megőrizni a Transact-SQL-parancsfájlok használatával hajtható végre, az Azure SQL-adatbázisok csoport között.

**Nyilvántartás**

Feladatok futtassa a parancsfájlt, és az egyes adatbázisok végrehajtási állapot. Hiba esetén is kap automatikus próbálkozzon újra.

**Rugalmasság**

Azure SQL-adatbázisok egyéni csoportot, és fut egy feladat ütemezésének meghatározását.

> [!NOTE]
> Az Azure-portálon SQL Azure rugalmas készletek korlátozott funkciók csak korlátozott készlete áll rendelkezésre. A PowerShell API-k segítségével hozzáférés a teljes aktuális funkciót.
> 
> 

## <a name="applications"></a>Alkalmazások
* Felügyeleti feladatok, például egy új séma telepítését.
* Hivatkozás adatok-termékinformációk közös frissítése összes adatbázis között. Vagy az ütemezések automatikus frissítések minden hétköznap, óra múlva.
* Lekérdezés teljesítmény javítása érdekében indexek újraépítése. A újraépítése beállítható úgy, hogy hajtható végre közötti ismétlődő módon, adatbázisok gyűjteménye, mint végezze alacsony forgalmú időszakban.
* Gyűjtése lekérdezés eredményeit az adatbázisok közül egy központi táblába folyamatos jelleggel. Teljesítmény-lekérdezések lehetnek folyamatosan végre és konfigurált eseményindító további feladatokat hajthatnak végre.
* Hajtsa végre a hosszabb futó adatfeldolgozási lekérdezéseket elvégzése nagyobb az adatbázisok, például a telemetriai ügyfél gyűjteménye. Eredmények további elemzés céljára egyetlen céltáblába történő összegyűjtése.

## <a name="elastic-database-jobs-end-to-end"></a>Rugalmas adatbázis-feladatok: végpont
1. Telepítse a **rugalmas adatbázis-feladatok** összetevőket. További információkért lásd: [rugalmas adatbázis telepítése feladatok](sql-database-elastic-jobs-service-installation.md). Ha a telepítés sikertelen lesz, lásd: [eltávolítása](sql-database-elastic-jobs-uninstall.md).
2. A PowerShell API-k használatával további funkciókat, például az adatbázis egyénileg definiált gyűjtemények, ütemezések hozzáadása és/vagy adatgyűjtési eredmények beállítása létrehozásáról hozzáférhetnek. Egyszerű telepítés és a feladatok végrehajtásának korlátozódik létrehozása/figyelésére használni a portált egy **rugalmas készlet**. 
3. A feladat-végrehajtás titkosított hitelesítő adatok létrehozása és [vegye fel a felhasználó (vagy a szerepkör) a csoportban lévő egyes adatbázisok](sql-database-security-overview.md).
4. Hozzon létre egy idempotent T-SQL-parancsfájl, amely a csoport minden adatbázison futtatható. 
5. Kövesse az alábbi lépéseket az Azure portál használatával feladatok létrehozására: [létrehozása és a rugalmas adatbázis-feladatok kezelése](sql-database-elastic-jobs-create-and-manage.md). 
6. Vagy a PowerShell-parancsfájlok használata: [létrehozása a PowerShell (előzetes verzió) használatával SQL Database rugalmas adatbázis-feladatok kezelése és](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Az Idempotent parancsfájlok
A parancsfájlok kell [idempotent](https://en.wikipedia.org/wiki/Idempotence). Egyszerű fogalmazva "idempotent" azt jelenti, hogy a parancsfájl sikeres, és újra fut, ha ugyanazt az eredményt következik be. Átmeneti hálózati probléma miatt sikertelenek lehetnek a parancsfájl. Ebben az esetben a feladat automatikusan megpróbálja a parancsfájl futtatása előtt desisting előre definiált ennyiszer. Az idempotent parancsfájl ugyanazt az eredményt rendelkezik, akkor is, ha korábban már sikeresen futott kétszer. 

Egy egyszerű tactic, hogy azt létrehozása előtt egy objektum létezését teszteléséhez.  

    IF NOT EXIST (some_object)
    -- Create the object 
    -- If it exists, drop the object before recreating it.

Ehhez hasonlóan a parancsfájl sikeresen végrehajtható legyen a logikailag teszteléséhez képesnek kell lennie, és azokat a feltételeket elleni talál.

## <a name="failures-and-logs"></a>Hibák és a naplókat
Ha több próbálkozást követően a parancsfájl futása sikertelen, a feladat naplózza a hibát, és továbbra is fennáll. A feladat befejeződését követően (azaz a csoportban lévő összes adatbázisokhoz futtató), ellenőrizheti a sikertelen bejelentkezési kísérletek listáját. A naplók hibás parancsfájlok hibakeresése részletekkel szolgálnak. 

## <a name="group-types-and-creation"></a>Csoport típusok és létrehozása
A csoportok két fő típusba sorolhatók: 

1. A shard beállítása
2. Egyéni csoportok

A shard set csoportok használatával hozhatók létre a [skálázáshoz rugalmas adatbáziseszközöket](sql-database-elastic-scale-introduction.md). A szilánkok set csoport létrehozásakor adatbázisok hozzáadásakor vagy eltávolításakor automatikusan a csoportból. Például egy új shard lesz automatikusan a csoportban található a szilánkcímtárban leképezés hozzáadásakor. Egy feladat futtathatja a csoporton.

Egyéni, másrészt, szigorúan történik. Meg kell explicit módon adja hozzá, vagy távolítsa el az adatbázisok egyéni csoportokból. Ha egy adatbázist a csoport megszakad, a feladat megpróbálja futtassa a parancsfájlt az adatbázis végleges hibát eredményez. Jelenleg az Azure portál használatával létrehozott csoportok olyan egyéni csoportok. 

## <a name="components-and-pricing"></a>Összetevők és az árképzés terén
A következő összetevők működnek együtt az Azure felhőalapú szolgáltatás, amely lehetővé teszi az alkalmi felügyeleti feladatok végrehajtásának létrehozásához. Az összetevők telepítése és konfigurálása automatikusan történik az előfizetés a telepítés során. A szolgáltatások összes rendelkeznek az automatikusan létrehozott névvel azonosíthatók. A név egyedi, és az előtag "edj" követően 21 véletlenszerűen létrehozott karaktereket tartalmaz.

* **Azure Cloud Service**: rugalmas adatbázis-feladatok (előzetes verzió) ügyfél-kiszolgálón futó Azure felhő alapú szolgáltatásként végrehajtani a kért feladat végrehajtásának kerül. A portálról a szolgáltatás telepített és megtalálható a Microsoft Azure-előfizetése. Az alapértelmezett telepített szolgáltatás fut, a legalább két feldolgozói szerepkörök magas rendelkezésre állásra. Minden egyes feldolgozói szerepkör (ElasticDatabaseJobWorker) alapértelmezett mérete a A0 példánya fut. Díjszabási, lásd: [árképzési felhőszolgáltatások](https://azure.microsoft.com/pricing/details/cloud-services/). 
* **Az Azure SQL Database**: A szolgáltatás használja az Azure SQL Database néven a **feladatvezérlő adatbázishoz** az összes feladat metaadatok tárolására. Az alapértelmezett szolgáltatási réteg egy S0. Díjszabási, lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).
* **Az Azure Service Bus**: az Azure Service Bus van az Azure Cloud Service belül a munka összehangolását. Lásd: [Service Bus árképzési](https://azure.microsoft.com/pricing/details/service-bus/).
* **Az Azure Storage**: az Azure Storage-fiók fogja tárolni a diagnosztikai kimenetet arra az esetre, ha probléma van szükség, további hibakeresési naplózás (lásd: [diagnosztika engedélyezésével az Azure Cloud Services és a virtuális gépek](../cloud-services/cloud-services-dotnet-diagnostics.md)). Díjszabási, lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>A rugalmas adatbázis-feladatok működése
1. Egy Azure SQL Database jelöl ki egy **feladatvezérlő adatbázishoz** amely tárolja az összes metaadat-adatainak és állapotának adatokat.
2. A feladatvezérlő adatbázishoz hozzáfér a **szolgáltatás feladat** indítsa el, és nyomon követéséhez a feladatok végrehajtásához.
3. Két különböző szerepkörök kommunikálnak a feladatvezérlő adatbázishoz: 
   * Tartományvezérlő: Meghatározza, hogy milyen típusú feladatok feladatokat végrehajtani a kért feladat, és az ismételt próbálkozás sikertelen feladatok hozzon létre új feladat feladatok kell.
   * Feladat a feladat a végrehajtás: A feladat feladatokat végzi.

### <a name="job-task-types"></a>Feladattípusok feladat
Több feladat feladatokat hajthat végre feladatok végrehajtásának típusa van:

* ShardMapRefresh: Lekérdezi a shard térkép szilánkok használt összes adatbázist meghatározásához
* ScriptSplit: Felosztja a parancsfájl kötegek "Ugrás" kimutatásait között
* ExpandJob: Gyermek feladatok létrehozása az egyes adatbázisok a feladatból, amelynek célpontja adatbázisok csoportja
* ScriptExecution: Végrehajtja a megadott hitelesítő adatok használatával egy adott adatbázison parancsfájl
* Dacpac: Egy DACPAC vonatkozik egy adott adatbázis adott hitelesítő adatok használatával

## <a name="end-to-end-job-execution-work-flow"></a>Végpontok közötti feladat-végrehajtási-munkafolyamat
1. A portál vagy a PowerShell API használatával, a feladatok bekerülnek a **feladatvezérlő adatbázishoz**. A feladat a megadott hitelesítő adatok használatával adatbázisok csoportja elleni Transact-SQL parancsfájl végrehajtását kéri.
2. A vezérlő azonosítja az új projektbe. Feladat feladatok létrehozása és hajtotta végre a parancsfájl felosztása és a csoporthoz tartozó adatbázisok frissítéséhez. Végül egy új feladatot létrehozni és bontsa ki a feladatot, és hozzon létre feladatokat, ahol minden gyermek feladat van megadva, a csoportban lévő egyes adatbázison a Transact-SQL parancsfájl végrehajtása az új gyermek végrehajtani.
3. A vezérlő azonosítja a létrehozott gyermek feladatok. Minden feladat a vezérlő hoz létre, és elindítja egy feladatot az adatbázis futtassa a parancsfájlt. 
4. Feladataival feladat befejezése után a vezérlő frissíti a feladat kész állapotba. 
   Feladat végrehajtása során bármikor a PowerShell API segítségével megtekintheti a feladat végrehajtása aktuális állapotát. Az összes idő a PowerShell API-k által visszaadott UTC formátumban vannak megadva. Ha szükséges, a lemondási kérelmet leállíthat feladatot is kezdeményezhető. 

## <a name="next-steps"></a>Következő lépések
[Az összetevők telepítéséhez](sql-database-elastic-jobs-service-installation.md), majd [létrehozása és hozzáadása a napló az egyes adatbázis-adatbázisok csoport](sql-database-manage-logins.md). Feladat létrehozása és kezelése további ismertetése: [rugalmas adatbázis-feladatok létrehozását és kezelését](sql-database-elastic-jobs-create-and-manage.md). Lásd még: [Ismerkedés a rugalmas feladatok](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->



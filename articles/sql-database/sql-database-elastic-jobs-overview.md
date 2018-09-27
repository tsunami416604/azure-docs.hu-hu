---
title: Horizontálisan felskálázott felhőalapú adatbázisok kezelése |} A Microsoft Docs
description: A rugalmas feladatokat végző szolgáltatás segítségével egy szkript végrehajtása adatbázisok egy csoportja között.
services: sql-database
ms.service: sql-database
subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 06/14/2018
ms.openlocfilehash: 649a76fad8f54339a6b1e429b3966945410573f7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47166792"
---
# <a name="managing-scaled-out-cloud-databases"></a>Horizontálisan felskálázott felhőalapú adatbázisok kezelése

[!INCLUDE [elastic-database-jobs-deprecation](../../includes/sql-database-elastic-jobs-deprecate.md)]

**Elastic Database-feladatok** van egy ügyfél által üzemeltetett Azure-Felhőszolgáltatás, amely lehetővé teszi az igény szerinti és ütemezett felügyeleti feladatokat, amelyeket a rendszer meghív végrehajtásának **feladatok**. A feladatok is egyszerűen és megbízhatóan kezelése az Azure SQL Database-adatbázisok nagy mennyiségű felügyeleti műveletek végrehajtása a Transact-SQL-szkriptek futtatásával. 

Horizontálisan felskálázott szilánkokra osztott adatbázisok, kezelheti a **rugalmas adatbázis-feladatok** funkció (előzetes verzió) lehetővé teszi megbízható között adatbáziscsoportok, beleértve a Transact-SQL (T-SQL) parancsprogram végrehajtása:

* adatbázisok (alább részletesen) egyénileg definiált gyűjteménye
* az összes adatbázis- [rugalmas készlet](sql-database-elastic-pool.md)
* a szegmens set (használatával létrehozott [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)). 

## <a name="documentation"></a>Dokumentáció
* [Az Elastic Database-feladat összetevőinek telepítése](sql-database-elastic-jobs-service-installation.md). 
* [Ismerkedés a rugalmas adatbázis-feladatok](sql-database-elastic-jobs-getting-started.md).
* [PowerShell-lel feladatok létrehozása és kezelése](sql-database-elastic-jobs-powershell.md).
* [Létrehozása és kezelése az Azure SQL Database-adatbázisok bővítő](sql-database-elastic-jobs-getting-started.md)



![A rugalmas feladatokat végző szolgáltatás][1]

## <a name="why-use-jobs"></a>Miért érdemes használni a feladatok?
**Kezelés**

Egyszerűen tegye sémamódosítások, hitelesítő adatok kezelése, referenciaadatok frissítése, teljesítményadat-gyűjtés vagy bérlő (ügyfél) telemetria gyűjtése.

**Jelentések**

Összesítheti az Azure SQL Database-adatbázisok egy gyűjteményéből származó adatokat egy céltáblában.

**Terhelés csökkentése érdekében**

Általában mindegyik adatbázist függetlenül kell csatlakoztatnia, hogy Transact-SQL utasításokat futtasson vagy egyéb adminisztratív feladatokat hajtson végre. Egy feladat kezeli a bejelentkezéseket egy célcsoport összes adatbázisába. Emellett meghatározhat, fenntarthat és megőrizhet Transact-SQL-szkripteket, amelyeket Azure SQL Database-adatbázisok egy csoportján futtat.

**Számlázás**

Feladatok futtassa a szkriptet, és jelentkezzen az egyes adatbázisok végrehajtási állapotát. Hiba esetén a rendszer automatikusan újrapróbálkozik.

**Rugalmasság**

Meghatározhatja Azure SQL Database-adatbázisok egyéni csoportjait, valamint a feladatok futtatásának ütemezését.

> [!NOTE]
> Az Azure Portalon az SQL Azure rugalmas készletek korlátozott funkciók csak egy csökkentett készletét érhető el. A PowerShell API-k eléréséhez a jelenlegi funkciók teljes készletét használja.
> 
> 

## <a name="applications"></a>Alkalmazások
* Felügyeleti feladatok végrehajtása, például egy új séma üzembe helyezése.
* Common data-termék kapcsolódó információk frissítése az összes adatbázis között. Vagy az ütemezések az automatikus frissítés minden hétköznap, óra múlva.
* Az indexek újraépítésével javíthatja a lekérdezési teljesítményt. Az újraépítési között egy rendszeres, adatbázis-gyűjtemény végrehajtása például csúcsidőn konfigurálható.
* A lekérdezési adatokat az adatbázis-készletekből folyamatosan egy központi táblába gyűjtheti. A teljesítménylekérdezések folyamatosan végrehajthatók, illetve konfigurálhatók úgy, hogy további végrehajtandó feladatokat indítsanak el.
* Olyan hosszabban futó adatfeldolgozási lekérdezéseket hajthat végre nagy adatbáziskészleteken, amilyen például az ügyfél-telemetria gyűjtése. A rendszer az eredményeket egyetlen céltáblában gyűjti össze a további elemzéshez.

## <a name="elastic-database-jobs-end-to-end"></a>Elastic Database-feladatok: teljes körű
1. Telepítse a **rugalmas adatbázis-feladatok** összetevőket. További információkért lásd: [telepítése rugalmas adatbázis-feladatok](sql-database-elastic-jobs-service-installation.md). Ha a telepítés nem sikerül, tekintse meg [eltávolítása](sql-database-elastic-jobs-uninstall.md).
2. A PowerShell API-k használatával hozzáférhetnek további funkciókat, például a egyénileg definiált adatbázis-gyűjtemények, ütemezés hozzáadása és/vagy összegyűjtéséhez eredmények csoportok létrehozása. A portál használata egyszerű telepítése és a futtatást a korlátozott feladatok létrehozása/megfigyelés egy **rugalmas készlet**. 
3. Feladat-végrehajtással kapcsolatos titkosított hitelesítő adatok létrehozása és [a felhasználó (vagy szerepkör) hozzáadása a csoportban lévő minden egyes adatbázishoz](sql-database-security-overview.md).
4. Hozzon létre egy, a csoport minden adatbázison futtatható T-SQL-szkript idempotens. 
5. Feladatok létrehozása az Azure portal használatával a következő lépésekkel: [létrehozása és kezelése a rugalmas adatbázis-feladatok](sql-database-elastic-jobs-create-and-manage.md). 
6. Vagy a PowerShell-parancsfájlok használata: [létrehozása és a egy SQL Database rugalmas adatbázis-feladatok PowerShell (előzetes verzió) használata kezelheti](sql-database-elastic-jobs-powershell.md).

## <a name="idempotent-scripts"></a>Idempotens szkriptek
A parancsfájlok kell [idempotens](https://en.wikipedia.org/wiki/Idempotence). Egyszerűen fogalmazva a "idempotens" azt jelenti, hogy ha a parancsfájl sikeres, és újra fut, ugyanaz az eredmény jelenik meg. A szkript végrehajtása átmeneti hálózati problémák következtében meghiúsulhat. Ebben az esetben a feladat automatikusan újrapróbálkozik, és a megadott számú alkalommal ismételten futtatja a szkriptet, mielőtt feladná azt. Egy idempotens szkriptre ugyanaz az eredmény akkor is, ha sikeresen lefutott kétszer. 

A legegyszerűbb módszer, ha ellenőrzi az objektum meglétét, mielőtt létrehozná.  

    IF NOT EXIST (some_object)
    -- Create the object 
    -- If it exists, drop the object before recreating it.

Ehhez hasonlóan a szkriptnek sikeresen végrehajthatónak kell lennie az észlelt feltételek logikai tesztelése és azokkal ellentétes műveletek révén.

## <a name="failures-and-logs"></a>Hibák és a naplók
Ha a parancsfájl futása sikertelen, több kísérlet után, a feladat naplózza a hibát, és továbbra is. A feladat befejezése után (azaz a Futtatás ellen a csoportban található összes adatbázis), ellenőrizheti a sikertelen bejelentkezési kísérletek listáját. A naplók nyújtanak hibás parancsfájlokban való hibakeresés részleteit. 

## <a name="group-types-and-creation"></a>Csoport típusa és létrehozása
A csoportok két fő típusba sorolhatók: 

1. Szilánkleképezés-csoportok
2. Egyéni csoportok

Szilánkleképezés beállítása csoportok használatával jön létre a [rugalmas adatbáziseszközöket](sql-database-elastic-scale-introduction.md). Amikor létrehoz egy szegmens csoport beállítása, adatbázisok hozzáadásakor vagy automatikusan törlődik a csoportból. Például egy új szegmensen lesz automatikusan a csoport a szegmenstérkép való hozzáadásakor. Egy feladat futtathatja a csoporton.

Egyéni csoportok, másrészt mereven határozzák meg. Meg kell explicit módon hozzáadása, vagy távolítsa el az adatbázisok egyéni csoportokból. Ha egy adatbázist a csoport a rendszer eldobja, a feladat megkísérli a parancsfájl futtatásához az adatbázis végleges hibát eredményez. Jelenleg az Azure portal használatával létrehozott csoportok olyan egyéni csoportok. 

## <a name="components-and-pricing"></a>Összetevők és díjszabás
A következő összetevők közösen hoz létre egy Azure-felhőszolgáltatáshoz, amely lehetővé teszi az alkalmi felügyeleti feladatok végrehajtását. Az összetevők telepítése és konfigurálása automatikusan az előfizetés a telepítés során. A szolgáltatások összes rendelkeznek az automatikusan létrehozott névvel azonosíthatók. A név egyedi, és az előtag "edj" 21 véletlenszerűen generált karakter követ áll.

* **Az Azure Cloud Service**: rugalmas adatbázis-feladatok (előzetes verzió) van szolgáltatásként tesz elérhetővé ügyfél által üzemeltetett Azure-felhő végrehajtani a kért feladat végrehajtását. A portálról a szolgáltatás telepítése és a Microsoft Azure-előfizetésében lévő üzemeltetett. Az alapértelmezett szolgáltatásának futtatásakor a szolgáltatás magas rendelkezésre állás érdekében két feldolgozói szerepkörrel minimális üzembe helyezve. Az alapértelmezett méret az egyes feldolgozói szerepkörök (ElasticDatabaseJobWorker) A0 példányát futtatja. A díjszabással kapcsolatban lásd: [Cloud services-díjszabás](https://azure.microsoft.com/pricing/details/cloud-services/). 
* **Az Azure SQL Database**: A szolgáltatás használja az Azure SQL Database, más néven a **adatbázis vezérlése** az összes feladat-metaadatok tárolására. Az alapértelmezett szolgáltatási szinten egy S0. A díjszabással kapcsolatban lásd: [SQL Database – díjszabás](https://azure.microsoft.com/pricing/details/sql-database/).
* **Az Azure Service Bus**: egy Azure Service Bus van az Azure Felhőszolgáltatáson belül a munka összehangolását. Lásd: [a Service Bus díjszabása](https://azure.microsoft.com/pricing/details/service-bus/).
* **Az Azure Storage**: egy Azure Storage-fiók segítségével tárolja a diagnosztikai kimenetet abban az esetben, ha egy probléma igényel, további hibakeresési célból történő naplózás (lásd: [Diagnosztikának az Azure Cloud Services és Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md)). A díjszabással kapcsolatban lásd: [Azure Storage szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-elastic-database-jobs-work"></a>Hogyan működik a rugalmas adatbázis-feladatok
1. Az Azure SQL Database ki van jelölve egy **adatbázis vezérlése** amely minden metaadatok és az állapot adatait tárolja.
2. Az adatbázis vezérlése hozzáfér a **feladatokat végző szolgáltatás** elindításához, és nyomon követheti a feladatok végrehajtásához.
3. Két különböző szerepkörök kommunikálnak az adatbázis vezérlése: 
   * Tartományvezérlő: Meghatározza, hogy mely feladatok feladatok új feladatok létrehozásával a kért feladat, és az újrapróbálkozások sikertelen feladatok végrehajtásához szükséges.
   * Feladat a feladat a végrehajtás: A feldolgozás feladatokat végez.

### <a name="job-task-types"></a>Feladattípusok feladat
Feladatok végrehajtásának végrehajtott feladatok több típusa is van:

* ShardMapRefresh: Lekérdezi a szegmenstérkép szegmensek használt összes adatbázist meghatározásához
* ScriptSplit: Bontja a parancsfájl kötegekbe "GO" utasítások között
* ExpandJob: Hoz létre gyermek feladatokat az egyes adatbázisok egy adatbáziscsoportok célzó feladatot
* ScriptExecution: A megadott hitelesítő adatok használatával egy adott adatbázison szkriptet hajt végre.
* Adatrétegbeli alkalmazáscsomag: Egy adatrétegbeli ALKALMAZÁSCSOMAGOT vonatkozik egy adott adatbázishoz adott hitelesítő adatokkal

## <a name="end-to-end-job-execution-work-flow"></a>Végpontok közötti feladat-végrehajtási-munkafolyamat
1. A portálon vagy a PowerShell API-t használ, egy feladatot a rendszer beszúrta a **adatbázis vezérlése**. A feladat egy meghatározott hitelesítő adatokat használó adatbázisok csoportjain Transact-SQL parancsfájl végrehajtási kérelmeket.
2. A vezérlő azonosítja az új feladat. Feladatok létrehozása és végrehajtása felosztása a szkriptet, és a csoporthoz tartozó adatbázisok frissítése. Végül egy új feladat létrejött, és bontsa ki a feladatot, és hozzon létre új gyermek ahol mindegyik gyermekfeladat van megadva, az egyes adatbázisok Transact-SQL-szkriptek végrehajtása a csoportban lévő feladatok végrehajtása.
3. A vezérlő a létrehozott gyermekfeladatok azonosítja. Minden egyes feladathoz a vezérlő hoz létre, és elindít egy feladatot a feladat a szkript egy adatbázison. 
4. Miután befejeződött a feldolgozás feladatokat, a vezérlő frissíti a feladatok egy befejezett állapotba. 
   Feladat végrehajtása során bármikor a PowerShell API segítségével megtekintheti a feladat végrehajtása aktuális állapotát. A PowerShell API-k által visszaadott összes hányszor szerepelnek az (UTC). Ha szükséges, egy megszakítási kérelmet leállíthat feladatot kezdeményezhetők. 

## <a name="next-steps"></a>További lépések
[Az összetevők telepítéséhez](sql-database-elastic-jobs-service-installation.md), majd [létrehozása, és adja hozzá a napló az adatbázisok a csoport minden egyes adatbázishoz](sql-database-manage-logins.md). Így jobban megismerheti a feladat létrehozása és kezelése, tekintse meg a [rugalmas adatbázis-feladatok létrehozását és kezelését](sql-database-elastic-jobs-create-and-manage.md). Lásd még: [Ismerkedés a rugalmas adatbázis-feladatok](sql-database-elastic-jobs-getting-started.md).

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->



---
title: Azure Synapse Analytics (korábban SQL DW) architektúra
description: Ismerje meg, hogy az Azure Synapse Analytics (korábbi sql DW) hogyan kombinálja a masszívan párhuzamos feldolgozást (MPP) az Azure Storage-szal a nagy teljesítmény és méretezhetőség elérése érdekében.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: d204477818ce2733d9f6d1e3dcc7455018456bcb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884832"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Azure Synapse Analytics (korábban SQL DW) architektúra

Az Azure Synapse egy korlátok nélküli elemzőszolgáltatás, amely egyesíti a vállalati adattárházakat és a Big Data-elemzéseket. Lehetővé teszi, hogy saját tetszőleges módon kérje le az adatokat, kiszolgáló nélküli igény szerinti vagy kiosztott erőforrásokkal, nagy mennyiségben. Az Azure Synapse egyesíti ezt a két világot egy egységes felhasználói felülettel, amely lenyeli, előkészíti, kezeli és kiszolgálja az adatokat az azonnali BI és gépi tanulási igényekhez.

 Az Azure Synapse négy összetevőből áll:

- SQL Analytics: Teljes T-SQL alapú elemzés

  - SQL-készlet (fizetés kiépített DWU-nként) – Általánosan elérhető
  - SQL on-demand (fizetés feldolgozott TB-nként) – (előzetes verzió)
- Spark: Mélyen integrált Apache Spark (előzetes verzió)
- Adatintegráció: Hibrid adatintegráció (előzetes verzió)
- Stúdió: egységes felhasználói élmény.  (Előzetes verzió)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-mpp-architecture-components"></a>Szinapszis SQL MPP architektúra-összetevők

[A Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) egy kibővített architektúrát használ az adatok számítási feldolgozásának több csomópont közötti elosztásához. A léptékegység a számítási teljesítmény absztrakciója , [amelyet adattárház egységnek neveznek.](what-is-a-data-warehouse-unit-dwu-cdwu.md) A számítás elkülönül a tárolótól, amely lehetővé teszi a számítás méretezését a rendszer adataitól függetlenül.

![Szinapszis SQL architektúra](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Az SQL Analytics csomópontalapú architektúrát használ. Az alkalmazások összekapcsolják és kiadják a T-SQL parancsokat egy vezérlőcsomóponthoz, amely az SQL Analytics egyetlen belépési pontja. A vezérlőcsomópont futtatja az MPP-motort, amely a lekérdezéseket párhuzamos feldolgozásra optimalizálja, majd átadja a műveleteket a számítási csomópontoknak, hogy párhuzamosan végezzék el a munkájukat.

A számítási csomópontok az összes felhasználói adatot az Microsoft Azure Storage-ban tárolják, és futtatják a párhuzamos lekérdezéseket. Az adatáthelyezési szolgáltatás (DMS) egy rendszerszintű belső szolgáltatás, amely szükség szerint áthelyezi az adatokat a csomópontok között a lekérdezések párhuzamos futtatásához és pontos eredmények visszaadásához.

A leválasztott tárolás és a számítás, a Synapse SQL-készlet használatakor a következőket teheti:

- A számítási teljesítmény független méretezése a tárolási igényektől függetlenül.
- Bővítse vagy csökkentse a számítási teljesítményt egy SQL-készleten (adattárházon) belül, adatok áthelyezése nélkül.
- Szüneteltetheti a számítási kapacitást az adatok megőrzésével, hogy csak a tárterületért kelljen fizetnie.
- A működési időn belül folytatni tudja a számítási kapacitást.

### <a name="azure-storage"></a>Azure Storage

A Synapse SQL az Azure Storage-t használja a felhasználói adatok biztonságának megőrzéséhez.  Mivel az adatokat az Azure Storage tárolja és kezeli, a tárhasználatért külön díjat számítunk fel. Az adatok felosztása a rendszer teljesítményének optimalizálása érdekében **a felosztások.** Kiválaszthatja, hogy melyik szilánkolási mintát használja az adatok elosztásához a tábla definiálásakor. Ezek a szilánkolási minták támogatottak:

- Kivonat
- Ciklikus időszeletelés
- Replikálás

### <a name="control-node"></a>Vezérlő csomópont

A Vezérlő csomópont az architektúra agya. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. Az MPP-motor a vezérlő csomóponton fut a párhuzamos lekérdezések optimalizálásához és koordinálásához. Amikor t-SQL-lekérdezést küld, a vezérlőcsomópont olyan lekérdezésekké alakítja át, amelyek párhuzamosan futnak az egyes disztribúciókon.

### <a name="compute-nodes"></a>Számítási csomópontok

A számítási csomópontok biztosítják a számítási teljesítményt. A disztribúciók leképezése számítási csomópontokhoz feldolgozásra. Ahogy több számítási erőforrásért fizet, a disztribúciók a rendelkezésre álló számítási csomópontokhoz lesznek hozzárendelve. A számítási csomópontok száma 1 és 60 között van, és a Synapse SQL szolgáltatási szintje határozza meg.

Minden számítási csomópont rendelkezik egy csomópontazonosítóval, amely a rendszernézetekben látható. A számítási csomópont azonosítóját úgy láthatja, hogy megkeresi a node_id oszlopot a rendszernézetekben, amelyek neve a sys.pdw_nodes...in sys.pdw_nodes. A rendszernézetek listáját az [MPP rendszernézetek ben láthatja.](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="data-movement-service"></a>Adatáthelyezési szolgáltatás

Az adatmozgatási szolgáltatás (DMS) az az adatátviteli technológia, amely koordinálja a számítási csomópontok közötti adatmozgást. Egyes lekérdezések adatáthelyezést igényelnek annak érdekében, hogy a párhuzamos lekérdezések pontos eredményeket adjanak vissza. Ha adatmozgásra van szükség, a DMS biztosítja, hogy a megfelelő adatok a megfelelő helyre jutjanak.

## <a name="distributions"></a>Disztribúciók

A elosztás a tárolás és az elosztott adatokon futtatott párhuzamos lekérdezések feldolgozásának alapegysége. Amikor az SQL Analytics lekérdezést futtat, a munka 60 kisebb, párhuzamosan futó lekérdezésekre oszlik.

A 60 kisebb lekérdezés mindegyike az egyik adatelosztáson fut. Minden számítási csomópont kezeli a 60 disztribúciók egy vagy több. Számítási csomópontonként egy terjesztési sql-készlet rendelkezik a maximális számítási erőforrásokkal. Egy SQL-készlet minimális számítási erőforrások rendelkezik az összes disztribúciók egy számítási csomóponton.  

## <a name="hash-distributed-tables"></a>Kivonat alapján elosztott táblák

A kivonat alapján elosztott tábla nyújtja a legnagyobb lekérdezési teljesítményt az összekapcsolásoknál és aggregációknál nagy táblák esetén.

Ha az adatokat kivonatoló elosztott táblába szeretné smasolni, a rendszer kivonatoló függvényt használ az egyes sorok determinisztikus hozzárendeléséhez egy disztribúcióhoz. A tábla definíciójában az oszlopok egyike elosztási oszlopként van megjelölve. A kivonatolási függvény az elosztási oszlop értékeit használja az egyes sorok elosztáshoz rendeléséhez.

Az alábbi ábra bemutatja, hogyan kerül egy teljes (nem elosztott tábla) kivonatoló elosztott táblaként.

![Elosztott tábla](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Elosztott tábla")  

- Minden sor egy eloszláshoz tartozik.  
- A determinisztikus kivonatoló algoritmus minden sort egy disztribúcióhoz rendel.  
- A táblasorok száma eloszlásonként a különböző méretű táblák szerint változik.

A terjesztési oszlopok kiválasztásához teljesítményszempontok at kell figyelembe venni, például a megkülönböztethetőséget, az adatferdítést és a rendszeren futó lekérdezéstípusokat.

## <a name="round-robin-distributed-tables"></a>Ciklikus időszeleteléssel elosztott táblák

A ciklikus multiplexelési tábla a legegyszerűbb tábla létrehozása és gyors teljesítményt nyújt, ha a terhelések átmeneti táblájaként használják.

Ciklikus időszeleteléses elosztott tábla egyenletesen osztja el az adatokat a táblázatban, de minden további optimalizálás nélkül. A felosztást először véletlenszerűen választják ki, majd a sorok puffereit egymás után rendelik hozzá a felosztásokhoz. Az adatok a ciklikus időszeleteléses táblába gyorsan betölthetők, de a lekérdezési teljesítmény gyakran jobb a kivonatelosztott táblák esetében. Ciklikus multiplexelési táblák illesztései további időt igényelnek az adatok átkeveréséhez.

## <a name="replicated-tables"></a>Replikált táblák

A kisméretű tábláknál a replikált táblák nyújtják a leggyorsabb lekérdezési teljesítményt.

A replikált tábla gyorsítótárazza a tábla teljes másolatát az egyes számítási csomópontokon. Ebből következően a replikál tábla esetében nincs szükség adatadásra a számítási csomópontok között az összekapcsolási vagy aggregációs művelet előtt. A replikált táblákat legjobban kisméretű táblákkal lehet kihasználni. További tárhelyre van szükség, és további többletterhelés merül fel az adatok írásakor, ami a nagy táblákat nem praktikussá teszi.  

Az alábbi ábrán egy replikált tábla látható, amely az egyes számítási csomópontok első disztribúcióján gyorsítótárazódik.  

![Replikált tábla](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Replikált tábla")

## <a name="next-steps"></a>További lépések

Most, hogy már tud egy kicsit az Azure Synapse-ról, ismerje meg, hogyan hozhat létre gyorsan [egy SQL-készletet,](create-data-warehouse-portal.md) és [hogyan tölthet be mintaadatokat.](load-data-from-azure-blob-storage-using-polybase.md) Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), amikor az új fogalmakkal ismerkedik. Vagy tekintse meg ezeket a többi Azure Synapse-erőforrásokat.  

- [Ügyfelek sikertörténetei](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Blogok](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Funkciókérések](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Támogatási jegy létrehozása](sql-data-warehouse-get-started-create-support-ticket.md)
- [MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
- [Stack Overflow-fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)

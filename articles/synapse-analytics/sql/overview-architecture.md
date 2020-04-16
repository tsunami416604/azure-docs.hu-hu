---
title: Azure Synapse SQL architektúra
description: Ismerje meg, hogy az Azure Synapse SQL hogyan kombinálja a masszívan párhuzamos feldolgozást (MPP) az Azure Storage-szal a nagy teljesítmény és méretezhetőség elérése érdekében.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 570e84b3a545736aad6983c7f0d8c0f0296ca589
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431812"
---
# <a name="azure-synapse-sql-architecture"></a>Azure Synapse SQL architektúra 

Ez a cikk a Szinapsze SQL architektúra-összetevőit ismerteti.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Szinapszis SQL architektúra-összetevők

A Synapse SQL egy horizontális felskálázási architektúrát használ az adatok számítási feldolgozásának több csomópont közötti elosztásához. A számítás elkülönül a tárolótól, amely lehetővé teszi a számítás méretezését a rendszer adataitól függetlenül. 

Az SQL-készlet esetében a skálázási egység a számítási teljesítmény absztrakciója, [amelyet adattárház egységnek](resource-consumption-models.md)neveznek. 

Az SQL igény szerinti, kiszolgáló nélküli, méretezés automatikusan történik, hogy megfeleljen a lekérdezési erőforrás-követelményeknek. Ahogy a topológia idővel változik a csomópontok vagy feladatátvételek hozzáadásával, alkalmazkodik a módosításokhoz, és gondoskodik arról, hogy a lekérdezés elegendő erőforrással és sikeres befejezéssel rendelkezik. Az alábbi képen például az SQL igény szerinti, 4 számítási csomópontot használó lekérdezést jelenít meg.

![Szinapszis SQL architektúra](./media//overview-architecture/sql-architecture.png)

A szinapszis SQL csomópontalapú architektúrát használ. Az alkalmazások összekötik és kiadják a T-SQL parancsokat egy vezérlőcsomóponthoz, amely a Synapse SQL egyetlen belépési pontja. 

Az SQL-készlet vezérlő csomópont javunkra használja az MPP-motort a lekérdezések párhuzamos feldolgozásra való optimalizálásához, majd átadja a műveleteket a számítási csomópontoknak, hogy párhuzamosan végezzék munkájukat. 

Az SQL igény szerinti vezérlőcsomópont a Distributed Query Processing (DQP) motort használja a felhasználói lekérdezés elosztott végrehajtásának optimalizálásához és koordinálásához, a számítási csomópontokon végrehajtandó kisebb lekérdezésekre való felosztásával. Minden kis lekérdezés neve feladat, és képviseli az elosztott végrehajtási egység. Beolvassa a fájl(oka)t a tárolóból, egyesíti a más feladatokból, csoportokból vagy rendelésekből származó adatokat. 

A számítási csomópontok az összes felhasználói adatot az Microsoft Azure Storage-ban tárolják, és futtatják a párhuzamos lekérdezéseket. Az adatáthelyezési szolgáltatás (DMS) egy rendszerszintű belső szolgáltatás, amely szükség szerint áthelyezi az adatokat a csomópontok között a lekérdezések párhuzamos futtatásához és pontos eredmények visszaadásához. 

A leválasztott tárolás és a számítás használata esetén a Synapse SQL használata kor a számítási teljesítmény független méretezése előnyeit élvezheti, függetlenül a tárolási igényektől. Az SQL igény szerinti skálázás automatikusan történik, míg az SQL készlet lehet:

* Bővítse vagy csökkentse a számítási teljesítményt egy SQL-készleten (adattárházon) belül, adatok áthelyezése nélkül.
* Szüneteltetheti a számítási kapacitást az adatok megőrzésével, hogy csak a tárterületért kelljen fizetnie.
* A működési időn belül folytatni tudja a számítási kapacitást.

## <a name="azure-storage"></a>Azure Storage

A Synapse SQL az Azure Storage-t használja a felhasználói adatok biztonságának megőrzéséhez. Mivel az adatokat az Azure Storage tárolja és kezeli, a tárhasználatért külön díjat számítunk fel. 

Sql on-demand lehetővé teszi, hogy a fájlok lekérdezése a data lake csak olvasható módon, míg az SQL-készlet lehetővé teszi az adatok betöltése is. Amikor az adatok at sql-készletbe kell bedolgozni, az adatok felosztása a rendszer teljesítményének optimalizálása érdekében **a rendszer felosztása.** Kiválaszthatja, hogy melyik szilánkolási mintát használja az adatok elosztásához a tábla definiálásakor. Ezek a szilánkolási minták támogatottak:

* Kivonat
* Ciklikus időszeletelés
* Replikálás

## <a name="control-node"></a>Vezérlő csomópont

A Vezérlő csomópont az architektúra agya. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. 

Az SQL-készletben az MPP-motor a vezérlőcsomóponton fut a párhuzamos lekérdezések optimalizálása és koordinálása érdekében. Amikor t-SQL-lekérdezést küld az SQL-készletbe, a vezérlőcsomópont olyan lekérdezésekké alakítja át, amelyek párhuzamosan futnak az egyes disztribúciókon.

Az SQL on-demand, DQP motor fut a vezérlő csomóponton, hogy optimalizálja és koordinálja elosztott végrehajtása a felhasználói lekérdezés felosztásával kisebb lekérdezések, amelyek végrehajtása a számítási csomópontokon. Azt is hozzárendeli az egyes csomópontok által feldolgozandó fájlkészleteket.

## <a name="compute-nodes"></a>Számítási csomópontok

A számítási csomópontok biztosítják a számítási teljesítményt. 

Az SQL-készletben a disztribúciók leképezése számítási csomópontok feldolgozásra. Ahogy több számítási erőforrásért fizet, a készlet újraleképezi a disztribúciókat a rendelkezésre álló számítási csomópontokhoz. A számítási csomópontok száma 1 és 60 között van, és az SQL-készlet szolgáltatási szintje határozza meg. Minden számítási csomópont rendelkezik egy csomópontazonosítóval, amely a rendszernézetekben látható. A számítási csomópont azonosítóját úgy láthatja, hogy megkeresi a node_id oszlopot a rendszernézetekben, amelyek neve a sys.pdw_nodes...in sys.pdw_nodes. A rendszernézetek listáját az [MPP rendszernézetek ben láthatja.](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest)

Az SQL igény szerinti rendszerben minden számítási csomóponthoz feladat és fájlkészlet tartozik, amelyeken a feladatok végrehajtása történik. A feladat elosztott lekérdezés-végrehajtási egység, amely valójában a beküldött lekérdezési felhasználó része. Automatikus skálázás van érvényben, győződjön meg arról, elegendő számítási csomópontok használják a felhasználói lekérdezés végrehajtásához.

## <a name="data-movement-service"></a>Adatáthelyezési szolgáltatás

Az adatmozgatási szolgáltatás (DMS) az SQL-készletben lévő adatátviteli technológia, amely koordinálja a számítási csomópontok közötti adatmozgást. Egyes lekérdezések adatáthelyezést igényelnek annak érdekében, hogy a párhuzamos lekérdezések pontos eredményeket adjanak vissza. Ha adatmozgásra van szükség, a DMS biztosítja, hogy a megfelelő adatok a megfelelő helyre jutjanak.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Disztribúciók

A disztribúció az SQL-készletben elosztott adatokon futó párhuzamos lekérdezések alapvető tárolási és feldolgozási egysége. Amikor az SQL-készlet lekérdezést futtat, a munka 60 kisebb, párhuzamosan futó lekérdezésre oszlik. 

A 60 kisebb lekérdezés mindegyike az egyik adatelosztáson fut. Minden számítási csomópont kezeli a 60 disztribúciók egy vagy több. Számítási csomópontonként egy terjesztési sql-készlet rendelkezik a maximális számítási erőforrásokkal. Egy SQL-készlet minimális számítási erőforrások rendelkezik az összes disztribúciók egy számítási csomóponton. 

## <a name="hash-distributed-tables"></a>Kivonat alapján elosztott táblák
A kivonat alapján elosztott tábla nyújtja a legnagyobb lekérdezési teljesítményt az összekapcsolásoknál és aggregációknál nagy táblák esetén. 

Az adatok egy kivonatoló elosztott táblába történő smassá, az SQL-készlet egy kivonatoló függvényt használ az egyes sorok determinisztikus hozzárendeléséhez egy disztribúcióhoz. A tábla definíciójában az oszlopok egyike elosztási oszlopként van megjelölve. A kivonatolási függvény az elosztási oszlop értékeit használja az egyes sorok elosztáshoz rendeléséhez.

Az alábbi ábra bemutatja, hogyan kerül egy teljes (nem elosztott tábla) kivonatoló elosztott táblaként. 

![Elosztott tábla](media//overview-architecture/hash-distributed-table.png "Elosztott tábla") 

* Minden sor egy eloszláshoz tartozik. 
* A determinisztikus kivonatoló algoritmus minden sort egy disztribúcióhoz rendel. 
* A táblasorok száma eloszlásonként a különböző méretű táblák szerint változik.

A terjesztési oszlopok kiválasztásához teljesítményszempontok at kell figyelembe venni, például a megkülönböztethetőséget, az adatferdítést és a rendszeren futó lekérdezéstípusokat.

## <a name="round-robin-distributed-tables"></a>Ciklikus időszeleteléssel elosztott táblák

A ciklikus multiplexelési tábla a legegyszerűbb tábla létrehozása és gyors teljesítményt nyújt, ha a terhelések átmeneti táblájaként használják.

Ciklikus időszeleteléses elosztott tábla egyenletesen osztja el az adatokat a táblázatban, de minden további optimalizálás nélkül. A felosztást először véletlenszerűen választják ki, majd a sorok puffereit egymás után rendelik hozzá a felosztásokhoz. Az adatok a ciklikus időszeleteléses táblába gyorsan betölthetők, de a lekérdezési teljesítmény gyakran jobb a kivonatelosztott táblák esetében. Ciklikus multiplexelési táblák illesztései további időt igényelnek az adatok átkeveréséhez.

## <a name="replicated-tables"></a>Replikált táblák
A kisméretű tábláknál a replikált táblák nyújtják a leggyorsabb lekérdezési teljesítményt.

A replikált tábla gyorsítótárazza a tábla teljes másolatát az egyes számítási csomópontokon. Ebből következően a replikál tábla esetében nincs szükség adatadásra a számítási csomópontok között az összekapcsolási vagy aggregációs művelet előtt. A replikált táblákat legjobban kisméretű táblákkal lehet kihasználni. További tárhelyre van szükség, és további többletterhelés merül fel az adatok írásakor, ami a nagy táblákat nem praktikussá teszi. 

Az alábbi ábrán egy replikált tábla látható, amely az egyes számítási csomópontok első disztribúcióján gyorsítótárazódik. 

![Replikált tábla](media/overview-architecture/replicated-table.png "Replikált tábla") 

## <a name="next-steps"></a>További lépések

Most, hogy tud egy kicsit a Synapse SQL-ről, ismerje meg, hogyan hozhat létre gyorsan [egy SQL-készletet,](../quickstart-create-sql-pool.md) és [hogyan tölthet be mintaadatokat](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./sql-data-warehouse-load-sample-databases.md). Vagy [elkezdi használni az SQL on-demand](../quickstart-sql-on-demand.md). Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét](../../azure-glossary-cloud-terminology.md), amikor az új fogalmakkal ismerkedik. 

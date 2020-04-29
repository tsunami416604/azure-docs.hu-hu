---
title: Azure szinapszis SQL-architektúra
description: Ismerje meg, hogy az Azure szinapszis SQL hogyan ötvözi a nagymértékben párhuzamos feldolgozást (MPP) az Azure Storage szolgáltatással a nagy teljesítmény és méretezhetőség érdekében.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81431812"
---
# <a name="azure-synapse-sql-architecture"></a>Azure szinapszis SQL-architektúra 

Ez a cikk a szinapszis SQL architektúrájának összetevőit ismerteti.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Szinapszis SQL Architecture-összetevők

A szinapszis SQL egy kibővíthető architektúrát használ a számítási folyamatok több csomóponton történő elosztásához. A számítás elkülönül a tárterülettől, ami lehetővé teszi a számítások egymástól független skálázását a rendszeren lévő adatoktól függetlenül. 

Az SQL-készlet esetében a skála egysége az [adattárház-egységként](resource-consumption-models.md)ismert számítási teljesítmény absztrakciója. 

A kiszolgáló nélküli SQL igény esetén a méretezés automatikusan történik a lekérdezési erőforrások követelményeinek kielégítése érdekében. Mivel a topológia az idő múlásával módosul a csomópontok vagy feladatátvételek hozzáadásával, eltávolításával, alkalmazkodik a változásokhoz, és gondoskodik arról, hogy a lekérdezés elegendő erőforrással rendelkezik, és sikeresen befejeződik. Az alábbi képen például a 4 számítási csomópontot használó SQL on-demand használatával végezhető el a lekérdezés végrehajtása.

![A Synapse SQL architektúrája](./media//overview-architecture/sql-architecture.png)

A szinapszis SQL egy node-alapú architektúrát használ. Az alkalmazások a T-SQL-parancsokat egy vezérlő csomóponthoz csatlakoznak, amely a szinapszis SQL beléptetésének egyetlen pontja. 

Az SQL-készlet vezérlő csomópontja MPP-motort használ a párhuzamos feldolgozásra irányuló lekérdezések optimalizálásához, majd a műveleteket a számítási csomópontok számára a párhuzamosan végzett munkához továbbítja. 

Az igény szerinti SQL-vezérlési csomópont elosztott lekérdezés-feldolgozási (DQP) motort használ a felhasználói lekérdezések elosztott végrehajtásának optimalizálására és összehangolására azáltal, hogy a számítási csomópontokon végrehajtandó kisebb lekérdezésekre bontja azt. A rendszer minden kis lekérdezést feladatnak nevez, és elosztott végrehajtási egységet jelöl. Beolvassa a fájl (oka) t a tárolóból, a más feladatokból beolvasott más feladatokból, csoportokból vagy rendelésekből származó adatokat is összekapcsol. 

A számítási csomópontok az összes felhasználói adatot az Microsoft Azure Storage-ban tárolják, és futtatják a párhuzamos lekérdezéseket. Az adatáthelyezési szolgáltatás (DMS) egy rendszerszintű belső szolgáltatás, amely szükség szerint áthelyezi az adatokat a csomópontok között a lekérdezések párhuzamos futtatásához és pontos eredmények visszaadásához. 

A leválasztott tárolással és számítással a szinapszis SQL egyik használata a számítási kapacitás független méretezését is kihasználhatja a tárolási igényektől függetlenül. Az SQL igény szerinti skálázása automatikusan történik, míg az SQL-készlethez a következőket teheti:

* Az adatok áthelyezése nélkül növelheti vagy csökkentheti a számítási teljesítményt egy SQL-készleten (adatraktáron) belül.
* Szüneteltetheti a számítási kapacitást az adatok megőrzésével, hogy csak a tárterületért kelljen fizetnie.
* A működési időn belül folytatni tudja a számítási kapacitást.

## <a name="azure-storage"></a>Azure Storage

A szinapszis SQL kihasználja az Azure Storage-t a felhasználói adatai biztonságának megőrzése érdekében. Mivel az Azure Storage tárolja és kezeli az adatait, külön díjat számítunk fel a tárterület-felhasználásért. 

Az SQL on-demand lehetővé teszi, hogy az adattárban lévő fájlokat csak olvasható módon kérdezze le, az SQL-készlet pedig lehetővé teszi az adatbevitelt is. Az SQL-készletbe való betöltéskor az adat felosztása **eloszlásba** történik a rendszer teljesítményének optimalizálása érdekében. Kiválaszthatja, hogy melyik horizontális Felskálázási mintát kell használnia az adatterjesztéshez a tábla meghatározásakor. Ezek a horizontális skálázási minták támogatottak:

* Kivonat
* Ciklikus időszeletelés
* Replikálás

## <a name="control-node"></a>Vezérlő csomópont

A vezérlő csomópont az architektúra agya. Ez az az előtérbeli rendszer, amely az összes alkalmazással és kapcsolattal együttműködik. 

Az SQL-készletben az MPP-motor a vezérlő csomóponton fut a párhuzamos lekérdezések optimalizálása és koordinálása érdekében. Ha T-SQL-lekérdezést küld az SQL-készletnek, a vezérlési csomópont átalakítja azokat a lekérdezéseket, amelyek párhuzamosan futnak az egyes eloszlásokon.

Az SQL on-demand szolgáltatásban a DQP motor a vezérlési csomóponton futtatja a felhasználói lekérdezések elosztott végrehajtásának optimalizálását és koordinálását úgy, hogy a számítási csomópontokon végrehajtandó kisebb lekérdezésekre osztja fel azokat. Emellett az egyes csomópontok által feldolgozandó fájlok készleteit is hozzárendeli.

## <a name="compute-nodes"></a>Számítási csomópontok

A számítási csomópontok biztosítják a számítási teljesítményt. 

Az SQL-készletben a disztribúciók a számítási csomópontokat dolgozzák fel feldolgozásra. A további számítási erőforrásokért a készlet újraképezi a disztribúciókat a rendelkezésre álló számítási csomópontokra. A számítási csomópontok száma 1-től 60-ig terjed, és az SQL-készlet szolgáltatási szintje határozza meg. Minden számítási csomóponthoz tartozik egy csomópont-azonosító, amely a rendszernézetekben látható. A számítási csomópont AZONOSÍTÓját úgy tekintheti meg, hogy megkeresi a rendszernézetek node_id oszlopát, amelynek neve a sys. pdw_nodes kezdetű. A rendszernézetek listáját a következő témakörben tekintheti meg: [MPP rendszernézetek](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

Az SQL on-demand szolgáltatásban minden számítási csomóponthoz hozzá kell rendelni egy feladatot, valamint a feladat végrehajtásához szükséges fájlokat. A feladat elosztott lekérdezés-végrehajtási egység, amely ténylegesen a lekérdezés felhasználója számára van elküldve. Az automatikus skálázás érvényes annak biztosítására, hogy elegendő számítási csomópont legyen kihasználva a felhasználói lekérdezés végrehajtásához.

## <a name="data-movement-service"></a>Adatáthelyezési szolgáltatás

Az adatátviteli szolgáltatás (DMS) az SQL-készlet adatátviteli technológiája, amely a számítási csomópontok közötti adatáthelyezést koordinálja. Egyes lekérdezések adatáthelyezést igényelnek annak biztosításához, hogy a párhuzamos lekérdezések pontos eredményeket állítsanak vissza. Ha adatáthelyezésre van szükség, a DMS biztosítja a megfelelő adatelérést a megfelelő helyre.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Disztribúciók

A disztribúció az SQL-készletben elosztott adatokon futó párhuzamos lekérdezések tárolási és feldolgozási alapegysége. Ha az SQL-készlet egy lekérdezést futtat, a munka 60 kisebb, párhuzamosan futó lekérdezésre van osztva. 

Az 60-es kisebb lekérdezések az egyik adateloszláson futnak. Minden számítási csomópont egy vagy több 60-disztribúciót kezel. A maximális számítási erőforrásokkal rendelkező SQL-készletekhez számítási csomópontok egyetlen eloszlással rendelkeznek. A minimális számítási erőforrásokkal rendelkező SQL-készletek egy számítási csomóponton lévő összes disztribúcióval rendelkeznek. 

## <a name="hash-distributed-tables"></a>Kivonat alapján elosztott táblák
A kivonat alapján elosztott tábla nyújtja a legnagyobb lekérdezési teljesítményt az összekapcsolásoknál és aggregációknál nagy táblák esetén. 

Az adatokat egy kivonattal elosztott táblába helyezheti el, az SQL-készlet kivonatoló függvényt használ az egyes sorok egyetlen eloszláshoz való hozzárendeléséhez determinisztikus módon. A tábla definíciójában az oszlopok egyike elosztási oszlopként van megjelölve. A kivonatolási függvény az elosztási oszlop értékeit használja az egyes sorok elosztáshoz rendeléséhez.

Az alábbi ábra azt szemlélteti, hogyan történik a teljes (nem elosztott) táblázat tárolása kivonatként elosztott táblaként. 

![Elosztott tábla](media//overview-architecture/hash-distributed-table.png "Elosztott tábla") 

* Minden sor egy eloszláshoz tartozik. 
* A determinisztikus kivonatoló algoritmus az egyes sorokat egyetlen eloszláshoz rendeli. 
* A tábla sorainak száma eloszlása a különböző méretű táblákban látható módon változik.

A terjesztési oszlop kiválasztásához teljesítménnyel kapcsolatos szempontokat kell figyelembe venni, például a különbségtételt, az adattorzítást, valamint a rendszeren futó lekérdezések típusait.

## <a name="round-robin-distributed-tables"></a>Ciklikus időszeleteléssel elosztott táblák

A betöltések előkészítési táblája, amely gyors teljesítményt nyújt, és rövid idő alatt készíti el a táblázatot.

Ciklikus időszeleteléses elosztott tábla egyenletesen osztja el az adatokat a táblázatban, de minden további optimalizálás nélkül. A rendszer először véletlenszerűen választja ki a eloszlást, majd a sorok puffereit egymás utáni eloszláshoz rendeli. Az adatok a ciklikus időszeleteléses táblába gyorsan betölthetők, de a lekérdezési teljesítmény gyakran jobb a kivonatelosztott táblák esetében. A ciklikus időszeletelésű táblákhoz való csatlakozáshoz szükség van az adatelemzésre, ami további időt vesz igénybe.

## <a name="replicated-tables"></a>Replikált táblák
A kisméretű tábláknál a replikált táblák nyújtják a leggyorsabb lekérdezési teljesítményt.

A replikált tábla a tábla teljes másolatát gyorsítótárazza az egyes számítási csomópontokon. Ebből következően a replikál tábla esetében nincs szükség adatadásra a számítási csomópontok között az összekapcsolási vagy aggregációs művelet előtt. A replikált táblákat legjobban kisméretű táblákkal lehet kihasználni. További tárterületre van szükség, és az adatírás során felmerülő további terhelés, amely a nagyméretű táblákat nem praktikus formában hajtja végre. 

Az alábbi ábrán egy olyan replikált tábla látható, amely az első eloszlásban van gyorsítótárazva az egyes számítási csomópontokon. 

![Replikált tábla](media/overview-architecture/replicated-table.png "Replikált tábla") 

## <a name="next-steps"></a>További lépések

Most, hogy már ismeri a szinapszis SQL-t, ismerkedjen meg az [SQL-készlet gyors létrehozásával](../quickstart-create-sql-pool.md) és a [mintaadatok betöltésével](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./SQL-Data-Warehouse-Load-Sample-Databases.MD). Vagy megkezdheti az [SQL igény szerinti használatát](../quickstart-sql-on-demand.md). Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét](../../azure-glossary-cloud-terminology.md), amikor az új fogalmakkal ismerkedik. 

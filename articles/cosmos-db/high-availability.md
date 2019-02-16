---
title: Az Azure Cosmos DB magas rendelkezésre állás
description: Ez a cikk bemutatja, hogyan nyújt az Azure Cosmos DB a magas rendelkezésre állás
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 2/13/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: b5e99b421e66f087a1793f5301736e192ef75c08
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311239"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Az Azure Cosmos DB magas rendelkezésre állás

Az Azure Cosmos DB transzparensen replikálja az adatokat, minden régióban az Azure-Cosmos-fiókjához hozzárendelt. A cosmos DB többrétegű redundanciát biztosít az adatok alkalmaz, az alábbi képen látható módon:

![Fizikai particionálása](./media/high-availability/cosmosdb-data-redundancy.png)

- Cosmos-tárolók belül az adatok horizontális particionálása.

- Minden egyes régióban minden partícióján a replikakészlethez védi az összes írást replikálódnak, és a replikák többségi tartósan véglegesítve lettek. Replikák akár 10 – 20 tartalék tartományok között oszlanak meg.

- Mindegyik partíció minden olyan régióban replikálja a rendszer. Minden egyes régió Cosmos-tároló az összes adat partíciókat tartalmaz is és fogadja el az írási és olvasási szolgálja ki is.  

Ha a Cosmos-fiók N Azure-régió között oszlanak meg, akkor lesz legalább négy példányt minden adatát x N. Kis késleltetésű adathozzáférés biztosítása és az a Cosmos-fiókjához társított régiók közötti írási/olvasási átviteli sebességet skálázás kívül további régiók (magasabb N) kellene is javítja a rendelkezésre állást.  

## <a name="slas-for-availability"></a>A rendelkezésre állási SLA-k

Egy globálisan elosztott adatbázis a Cosmos DB biztosítja a átfogó SLA-k, amely magában foglalja az átviteli sebességet a késés a 99. percentilis, a konzisztencia és a magas rendelkezésre állás. Az alábbi táblázat a magas rendelkezésre állás érdekében egy vagy több régióban fiókok Cosmos DB által nyújtott garanciák. A magas rendelkezésre állás érdekében konfigurálja a Cosmos-fiókoknak, az írási több régióban rendelkezik.

|Művelet típusa  | Egyetlen régió |Többrégiós (egyetlen naplórekordjait)|Többrégiós (többrégiós írás) |
|---------|---------|---------|-------|
|Írások    | 99.99    |99.99   |99.999|
|Olvasások     | 99.99    |99.999  |99.999|

> [!NOTE]
> A gyakorlatban korlátozott frissesség, munkamenet, konzisztens előtag és végleges konzisztencia modellek az aktuális írási rendelkezésre áll jelentősen nagyobb, mint a közzétett SLA-k. Az összes konzisztenciaszintek tényleges olvasási rendelkezésre áll jelentősen nagyobb, mint a közzétett SLA-kat.

## <a name="high-availability-with-cosmos-db-in-the-face-of-regional-outages"></a>Magas rendelkezésre állást a Cosmos DB használatával biztosít a regionális üzemkimaradások utáni helyreállításon

Regionális üzemkimaradások utáni helyreállításon nem ritkák, és az Azure Cosmos DB biztosítja, hogy az adatbázis mindig érhető el. A következő adatokat Cosmos DB-viselkedés szolgáltatáskiesés esetén, attól függően, a Cosmos-fiók konfigurációjának rögzítése:

- A Cosmos DB használatával mielőtt írási művelet arra vonatkozik, az ügyfélnek, az adatok van tartósan véglegesítve lettek szerint a kvórum replikával, amely az írási műveletek a régión belül.

- Többrégiós fiókok több írási régiók konfigurált írások és olvasások magas rendelkezésre állású lesz. Régiónkénti feladatátvétel azonnali és nem igényel módosításokat az alkalmazásból.

- A single-írási régió rendelkező fiókok többrégiós: Egy írási régió kimaradás során ezek a fiókok maradnak magas rendelkezésre állású olvasási. Azonban az írási műveletek "engedélyeznie kell az Automatikus feladatátvétel" a feladatátvétel az érintett régió egy másik régióba tartozó Cosmos-fiókjában. A feladatátvétel megadott régióban prioritás szerinti sorrendben történik. Végül az érintett régió újra online állapotba kerül, amikor az az érintett írási régió a szolgáltatáskimaradás közben nem replikált adatok keresztül teszik elérhetővé a hírcsatorna-ütközéseket. Alkalmazás is olvashat az ütközések hírcsatorna, az alkalmazás-specifikus logika alapján ütközések feloldásához és a frissített adatokat írhat vissza a megfelelő Cosmos-tároló. A korábban érintett írási régió állítja helyre, ha automatikusan elérhetővé válik, az olvasási régióban. Manuális feladatátvétel meghívása, és konfigurálja az érintett régió az írási régió. A manuális feladatátvétel elvégezhető használatával [Azure CLI-vel vagy az Azure Portalon](how-to-manage-database-account.md#manual-failover). Nincs **adatokat vagy a rendelkezésre állási adatvesztés nélkül** előtt, közben vagy a manuális feladatátvétel után. Az alkalmazás továbbra is magas rendelkezésre állású legyen. 

- A single-írási régió rendelkező fiókok többrégiós: Egy olvasási régió kimaradás során ezek a fiókok maradnak magas rendelkezésre állású olvasási és írási. Az érintett terület a rendszer automatikusan leválasztja az írási régió és lesznek megjelölve offline állapotban van. A Cosmos DB SDK átirányítja az olvasási hívásokat a következő rendelkezésre álló terület az elsődleges régió listában. Ha az elsődleges régió lista régiót egyik sem érhető el, hívások automatikusan térhet vissza az aktuális írási régióba. Nincs szükség módosításokra az alkalmazás kódjában olvasási régióban leállás kezelésére. Végül az érintett régió újra online állapotba kerül, ha a korábban érintett olvasási régió automatikusan szinkronizálja az aktuális írási régióba való és újra számára olvasási kérelmek kiszolgálására elérhető lesz. A helyreállított régió további olvasási megnyílik az alkalmazás kódjának módosítása nélkül. Alatt is feladatátvételi és újracsatlakozás egy korábban meghiúsult régió olvasási konzisztenciagaranciákat továbbra is Cosmos DB által veszi figyelembe.

- Egyetlen régióban fiókok elveszítheti a rendelkezésre állás a következő regionális kimaradás. Azt javasoljuk, hogy legalább két régió (lehetőleg, legalább két írási régió), a Cosmos-fiók beállítása mindig a magas rendelkezésre állásának biztosításához.

- Még a rendkívül ritka és kellemetlen esemény véglegesen helyreállíthatatlan, az Azure-régió esetén nincs adatvesztés nem lehetséges a többrégiós Cosmos-fiók van konfigurálva az alapértelmezett konzisztenciaszint erős, ha. A véglegesen helyreállíthatatlan írási régiót, a konfigurált, kötött elavulás konzisztencia, többrégiós Cosmos-fiókok esetén az esetleges adatvesztési időszak korlátozódik a frissesség ablak; munkamenet, konzisztens előtag és végleges konzisztencia szintek, a potenciális adatvesztési időszak korlátozva öt másodpercenként legfeljebb.

## <a name="building-highly-available-applications"></a>Magas rendelkezésre állású alkalmazások létrehozása

- Győződjön meg, hogy nagy írási és olvasási rendelkezésre állás, több írási régiók közül legalább két régió span Cosmos fiók beállítása. Ez a konfiguráció a rendelkezésre állás, a legkisebb késést biztosít, és a méretezhetőség is beolvassa, és SLA-k által. További tudnivalókért lásd: hogyan [több írási régiót a Cosmos-fiók beállítása](tutorial-global-distribution-sql-api.md). Az alkalmazások több főkiszolgálós beállítása: [konfigurálása több főkiszolgálós](how-to-multi-master.md).

- Többrégiós Cosmos-fiókok, egy egyetlen-írási régió konfigurált [automatikus feladatátvétel engedélyezése az Azure CLI-vel vagy az Azure portal használatával](how-to-manage-database-account.md#automatic-failover). Miután engedélyezte az automatikus feladatátvételt egy regionális katasztrófa történik, Cosmos DB automatikusan feladatátvételt fiókját.  

- Akkor is, ha a Cosmos-fiók magas rendelkezésre állású, az alkalmazás lehetséges, hogy nem helyesen kialakítani, hogy továbbra is magas rendelkezésre állású. A végpontok közötti magas rendelkezésre állás az alkalmazás teszteléséhez rendszeres időközönként meghívása a [manuális feladatátvételt az Azure CLI-vel vagy az Azure portal használatával](how-to-manage-database-account.md#manual-failover), az alkalmazás tesztelésének vagy a vészhelyreállítás (DR) részeként gyakorlatokat.

- Egy globálisan elosztott adatbázis-környezeten belül nincs közvetlen kapcsolat folytonosságát egy régióra kiterjedő szolgáltatáskimaradás konzisztencia szint és az adatok tartósságának között. Az üzletmenet folytonosságát biztosító terve kidolgozásakor kell tudni, mielőtt az alkalmazás a zavaró eseményeket követő teljes helyreállításának maximális elfogadható idő. Az alkalmazás teljes helyreállításához szükséges időt a helyreállítási időre vonatkozó célkitűzés (RTO) néven ismert. Emellett ismernie kell a leghosszabb az alkalmazás működését, Adatfrissítés elvesztése zavaró eseményeket követő helyreállítása során. Az adott időszakban, előfordulhat, hogy elfogadható frissítések helyreállításipont-célkitűzés (RPO) néven ismert. Az Azure Cosmos DB az RPO és RTO megtekintéséhez lásd: [konzisztencia szinteket és az adatok tartóssága](consistency-levels-tradeoffs.md#rto)

## <a name="next-steps"></a>További lépések

Ezután megismerheti skálázása az átviteli sebesség a következő cikkben:

* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [Globálisan a kiosztott átviteli kapacitás méretezése](scaling-throughput.md)
* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
* [Az Azure Cosmos DB-ben konzisztenciaszintek](consistency-levels.md)
* [Az alkalmazások több főkiszolgálós konfigurálása](how-to-multi-master.md)

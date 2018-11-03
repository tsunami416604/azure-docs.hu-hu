---
title: Az Azure Cosmos DB magas rendelkezésre állás
description: Ez a cikk bemutatja, hogyan nyújt az Azure Cosmos DB a magas rendelkezésre állás
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: fb7de4a913fa1edb3329e25276f83be2fb6cecd8
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961806"
---
# <a name="high-availability-in-azure-cosmos-db"></a>Az Azure Cosmos DB magas rendelkezésre állás érdekében

Az Azure Cosmos DB transzparensen replikálja az adatokat, minden régióban az Azure-Cosmos-fiókjához hozzárendelt. A cosmos DB többrétegű redundanciát biztosít az adatok alkalmaz, az alábbi képen látható módon:

![Erőforrás-particionálás](./media/high-availability/figure1.png) 

- Cosmos-tárolók belül az adatok horizontális particionálása.

- Minden egyes régióban minden partícióján a replikakészlethez védi az összes írást replikálódnak, és a replikák többségi tartósan véglegesítve lettek. Replikák akár 10 – 20 tartalék tartományok között oszlanak meg.

- Mindegyik partíció minden olyan régióban replikálja a rendszer. Minden egyes régió Cosmos-tároló az összes adat partíciókat tartalmaz is és fogadja el az írási és olvasási szolgálja ki is.  

Ha a Cosmos-fiók N Azure-régió között oszlanak meg, akkor lesz legalább N minden az adatok másolatát 4 x. Kis késleltetésű adathozzáférés biztosítása és az a Cosmos-fiókjához társított régiók közötti írási/olvasási átviteli sebességet skálázás kívül további régiók (magasabb N) kellene is javítja a rendelkezésre állást.  

## <a name="slas-for-availability"></a>A rendelkezésre állási SLA-k   

Egy globálisan elosztott adatbázis a Cosmos DB biztosítja a átfogó SLA-k, amely magában foglalja az átviteli sebességet a késés a 99. percentilis, a konzisztencia és a magas rendelkezésre állás. Az alábbi táblázatban a magas rendelkezésre állás egyetlen és több régióban fiókok Cosmos DB által biztosított vonatkozó garanciákat ismertetünk. A magas rendelkezésre állás érdekében konfigurálja a Cosmos-fiókoknak, az írási több régióban rendelkezik.

|Művelet típusa  | Egyetlen régió |Többrégiós (egyetlen naplórekordjait)|Többrégiós (többrégiós írás) |
|---------|---------|---------|-------|
|Írások    | 99,99    |99,99   |99,999|
|Olvasások     | 99,99    |99,999  |99,999|

> [!NOTE]
> A gyakorlatban korlátozott frissesség, munkamenet, konzisztens előtag és végleges konzisztencia modellek az aktuális írási rendelkezésre áll jelentősen nagyobb, mint a közzétett SLA-k. Az összes konzisztenciaszintek tényleges olvasási rendelkezésre áll jelentősen nagyobb, mint a közzétett SLA-kat.

## <a name="regional-outages"></a>Regionális üzemkimaradások utáni helyreállításon

Regionális üzemkimaradások utáni helyreállításon nem ritkák, és az Azure Cosmos DB biztosítja, hogy az adatbázis mindig érhető el. A következő adatokat Cosmos DB-viselkedés szolgáltatáskiesés esetén, attól függően, a Cosmos-fiók konfigurációjának rögzítése: 

- Többrégiós fiókok több írási régiók konfigurált írások és olvasások magas rendelkezésre állású marad. Régiónkénti feladatátvétel azonnali, és nem szükséges módosítania az alkalmazásból.

- A single-írási régió többrégiós fiókokat: egy írási régió kimaradás során ezek a fiókok marad magas rendelkezésre állású az olvasásokhoz. Azonban az írási műveletek "engedélyeznie kell az Automatikus feladatátvétel" a feladatátvétel az érintett régió egy másik régióba tartozó Cosmos-fiókjában. A feladatátvétel megadott régióban prioritás szerinti sorrendben történik. Végül az érintett régió újra online állapotba kerül, ha az érintett írási régióban leállás során árvává adatok keresztül teszik elérhetővé a hírcsatorna-ütközéseket. Alkalmazás is olvashat az ütközések hírcsatorna, az alkalmazás-specifikus logika alapján ütközések feloldásához és a frissített adatokat írhat vissza a megfelelő Cosmos-tároló. A korábban érintett írási régió állítja helyre, ha automatikusan elérhetővé válik, az olvasási régióban. Manuális feladatátvétel meghívása és a térnek vissza, az írási régió az érintett régióban. A manuális feladatátvétel elvégezhető használatával [Azure CLI-vel vagy az Azure Portalon](how-to-manage-database-account.md#enable-manual-failover-for-your-cosmos-account).  

- A single-írási régió többrégiós fiókokat: egy olvasási régió kimaradás során ezek a fiókok maradnak magas rendelkezésre állású olvasási és írási. Az érintett terület a rendszer automatikusan leválasztja az írási régió és lesznek megjelölve offline állapotban van. A Cosmos DB SDK átirányítja az olvasási hívásokat a következő rendelkezésre álló terület az elsődleges régió listában. Ha az elsődleges régió lista régiót egyik sem érhető el, hívások automatikusan térhet vissza az aktuális írási régióba. Nincs szükség módosításokra az alkalmazás kódjában olvasási régióban leállás kezelésére. Végül az érintett régió újra online állapotba kerül, ha a korábban érintett olvasási régió automatikusan szinkronizálja az aktuális írási régióba való és újra számára olvasási kérelmek kiszolgálására elérhető lesz. A helyreállított régió további olvasási megnyílik az alkalmazás kódjának módosítása nélkül. Alatt is feladatátvételi és újracsatlakozás egy korábban meghiúsult régió olvasási konzisztenciagaranciákat továbbra is Cosmos DB által veszi figyelembe.

- Egyetlen régióban fiókok elveszítheti a rendelkezésre állási regionális kimaradás esetén. Azt javasoljuk, hogy legalább két régióban (lehetőleg, legalább két írási régió) beállítása a magas rendelkezésre állásának biztosításához mindig Cosmos fiókkal.

## <a name="building-highly-available-applications"></a>Magas rendelkezésre állású alkalmazások létrehozása

- Győződjön meg, hogy nagy írási és olvasási rendelkezésre állás, több írási régiók közül legalább két régió span Cosmos fiók beállítása. Ez a konfiguráció biztosítja a rendelkezésre állás, a legkisebb késés, és a méretezhetőség is beolvassa, és SLA-k által. További tudnivalókért lásd: hogyan [több írási régiót a Cosmos-fiók beállítása](tutorial-global-distribution-sql-api.md).

- Többrégiós Cosmos-fiókok, egy egyetlen-írási régió konfigurált [automatikus feladatátvétel engedélyezése az Azure CLI-vel vagy az Azure portal használatával](how-to-manage-database-account.md#enable-automatic-failover-for-your-cosmos-account). Miután engedélyezte az automatikus feladatátvételt egy regionális katasztrófa történik, Cosmos DB automatikusan feladatátvételt fiókját.  

- Akkor is, ha a Cosmos-fiók magas rendelkezésre állású, az alkalmazás lehetséges, hogy nem helyesen kialakítani, hogy továbbra is magas rendelkezésre állású. A végpontok közötti magas rendelkezésre állásának biztosításához az alkalmazáshoz, rendszeres időközönként meghívása a [manuális feladatátvételt az Azure CLI-vel vagy az Azure portal használatával](how-to-manage-database-account.md#enable-manual-failover-for-your-cosmos-account), az alkalmazás tesztelésének vagy a vészhelyreállítás (DR) részeként gyakorlatokat. 

## <a name="next-steps"></a>További lépések

Ezután megismerheti skálázása az átviteli sebesség a következő cikkben:

* [Méretezési átviteli sebesség](scaling-throughput.md)

* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)

* [Átviteli sebesség méretezése globálisan](scaling-throughput.md)

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)

* [Az Azure Cosmos DB-ben konzisztenciaszintek](consistency-levels.md)



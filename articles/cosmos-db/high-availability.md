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
ms.openlocfilehash: fdf1bf227ed21111c1aa0754a8423c1ee6aa151b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244142"
---
# <a name="high-availability-in-azure-cosmos-db"></a>Az Azure Cosmos DB magas rendelkezésre állás érdekében

Az Azure Cosmos DB transzparensen replikálja az adatokat a Cosmos-fiókhoz társított Azure-régiók között.  A cosmos DB többrétegű redundanciát biztosít az adatok alkalmaz. Amint az az alábbi ábrán látható:

- Cosmos-tárolók belül az adatok horizontális particionálása.
- Minden egyes régióban mindegyik partíció egy replikakészlethez védi az összes írást replikálódnak, és a replikák többségi tartósan véglegesítve lettek. Replikák akár 10 – 20 tartalék tartományok között oszlanak meg.
- Minden olyan régióban a rendszer replikálja az összes partíciót. Minden egyes régió tartalmazza az összes Cosmos-tároló az adatok a partíciók is és fogadja el az írási és olvasási szolgálja ki is.  

![Erőforrás-particionálás](./media/high-availability/figure1.png) 

Ha a Cosmos-fiók N Azure-régió között oszlanak meg, akkor lesz legalább N minden az adatok másolatát 4 x. Hozzáférés biztosítása mellett közel valós idejű, adatait és a méretezési írási és olvasási teljesítménye a régióban, a Cosmos-fiókjához társított, további régiók (magasabb N) kellene is javítja a rendelkezésre állást.  

## <a name="availability-slas"></a>Rendelkezésre állási SLA-k  

Egy globálisan elosztott adatbázis a Cosmos DB biztosítja a átfogó SLA-k, amely magában foglalja az átviteli sebességet a késés a 99. percentilis, a konzisztencia és a magas rendelkezésre állás. Az alábbi táblázatban a magas rendelkezésre állás egyetlen és több régióban fiókok Cosmos DB által biztosított vonatkozó garanciákat ismertetünk. A legmagasabb szintű rendelkezésre állás érdekében javasoljuk, hogy szeretné, hogy több írási régiót a Cosmos-fiókok konfigurálása.

|Művelettípus  | Egyetlen régió |Többrégiós (egyetlen naplórekordjait)|Többrégiós (többrégiós írási műveletek|
|---------|---------|---------|-------|
|Írások    | 99,99    |99,99   |99,999|
|Olvasások     | 99,99    |99,999  |99,999|

> [!NOTE]
> A gyakorlatban korlátozott frissesség, munkamenet, konzisztens előtag és végleges konzisztencia modellek az aktuális írási rendelkezésre áll jelentősen nagyobb, mint a közzétett SLA-k. És a gyakorlatban, a tényleges olvasási rendelkezésre az összes konzisztenciaszintek jelentősen nagyobb, mint a közzétett SLA-k.

## <a name="regional-outages"></a>Regionális üzemkimaradások utáni helyreállításon

Regionális üzemkimaradások utáni helyreállításon nem ritkák, és az Azure Cosmos DB biztosítja, hogy az adatbázis mindig érhető el. A következő rögzíti a Cosmos DB-viselkedés szolgáltatáskiesés esetén, a Cosmos-fiók konfigurációtól függően: 

- Többrégiós fiókok több írási régiók konfigurált magas rendelkezésre állású marad, mindkettő ír, és beolvassa egy regionális kimaradás során. Régiónkénti feladatátvétel azonnali, és nem szükséges módosítania az alkalmazásból.

- Többrégiós fiókok egy írási régió kimaradás során egy egyetlen-írási régiót az olvasási magas rendelkezésre állású maradnak. Azonban való írásra "engedélyeznie kell az Automatikus feladatátvétel" Cosmos-fiókja Cosmos DB-hez készült feladatátvétel az érintett régió a Cosmos-fiókjához társított másik régióba. A feladatátvétel megadott régióban prioritás szerinti sorrendben történik. Végül az érintett régió újra online állapotba kerül, amikor az az érintett írási régió a szolgáltatáskimaradás közben nem replikált adatok keresztül teszik elérhetővé a hírcsatorna-ütközéseket. Alkalmazás is olvashat az ütközések hírcsatorna, az alkalmazás-specifikus logika alapján ütközések feloldásához és a frissített adatokat írhat vissza a megfelelő Cosmos-tároló. A korábban érintett írási régió állítja helyre, ha automatikusan elérhetővé válik, az olvasási régióban. Manuális feladatátvétel meghívása és a térnek vissza, az írási régió az érintett régióban. Manuális feladatátvételt az Azure CLI-vel vagy az Azure portal használatával teheti meg.  

- Többrégiós fiókokat egy olvasási régió kimaradás során egy egyetlen-írási régiót az olvasási és írási magas rendelkezésre állású marad. Az érintett terület a rendszer automatikusan leválasztja az írási régió és lesznek megjelölve offline állapotban van. A Cosmos DB SDK átirányítja az olvasási hívásokat a következő rendelkezésre álló terület az elsődleges régió listában. Ha az elsődleges régió lista régiót egyik sem érhető el, hívások automatikusan térhet vissza az aktuális írási régióba. Nincs szükség módosításokra az alkalmazás kódjában olvasási régióban leállás kezelésére. Idővel az érintett régió újra online állapotba kerül, ha a korábban érintett olvasási régió az aktuális írási régióba automatikusan szinkronizálja és újra olvasási számára elérhető lesz. A helyreállított régió további olvasási megnyílik az alkalmazás kódjának módosítása nélkül. Alatt is feladatátvételi és újracsatlakozás egy korábban meghiúsult régió olvasási konzisztenciagaranciákat továbbra is Cosmos DB által veszi figyelembe.

- Egyetlen régióban fiókok elveszítheti a rendelkezésre állási regionális kimaradás esetén. Erősen ajánlott meggyőződni legalább két régióban (lehetőleg, legalább két írási régió) állíthatja be a magas rendelkezésre állásának biztosításához mindig Cosmos fiókkal.

## <a name="building-highly-available-applications"></a>Magas rendelkezésre állású alkalmazások létrehozása

- Győződjön meg, hogy nagy írási és olvasási rendelkezésre állás, több írási régiók közül legalább két régió span Cosmos fiók beállítása. Ez a konfiguráció biztosítja a legjobb rendelkezésre állási legkisebb késés és méretezhetőség az olvasásokat és az írásokat szavatolják. Lásd: hogyan [több írási régiót a Cosmos-fiók beállítása](tutorial-global-distribution-sql-api.md).

- Többrégiós Cosmos-fiókok vannak konfigurálva a single-írási régió engedélyezze a "Automatikus feladatátvétel" Azure CLI-vel vagy az Azure portal használatával.  Miután engedélyezte az automatikus feladatátvételt egy regionális katasztrófa történik, Cosmos DB automatikusan feladatátvételt fiókját.  

- Akkor is, ha a Cosmos-fiók magas rendelkezésre állású, az alkalmazás lehetséges, hogy nem helyesen kialakítani, hogy továbbra is magas rendelkezésre állású. Az egészen a végpontok közötti magas rendelkezésre állásának biztosításához az alkalmazáshoz, rendszeres időközönként meghívása a "manuális feladatátvétel" az Azure CLI-vel vagy az Azure Portalon, az alkalmazás tesztelésének vagy a vészhelyreállítás (DR) részeként. További tudnivalókért lásd a regionális prioritások Cosmos-fiókja módosítása).  

## <a name="next-steps"></a>További lépések

Ezután megismerheti skálázása az átviteli sebesség a következő cikkben:

* [Méretezési átviteli sebesség](scaling-throughput.md)

* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)

* [Átviteli sebesség méretezése globálisan](scaling-throughput.md)

* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)

* [Az Azure Cosmos DB-ben konzisztenciaszintek](consistency-levels.md)



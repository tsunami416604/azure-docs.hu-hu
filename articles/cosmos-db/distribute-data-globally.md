---
title: Az Adatterjesztés globálisan Azure Cosmos DB
description: Ismerje meg a globálisan elosztott, többmodelles adatbázis-szolgáltatásokból származó, a világra kiterjedő geo-replikációt, több főkiszolgálót, feladatátvételt és adathelyreállítást Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 4f17fc7df5aef449c3b0f6dd8d02ae58df959070
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384887"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Globális Adatterjesztés Azure Cosmos DBokkal – áttekintés

A mai alkalmazások nagyon rugalmasak és mindig online állapotban vannak. Az alacsony késés és a magas rendelkezésre állás érdekében ezeknek az alkalmazásoknak a példányait a felhasználókhoz közel lévő adatközpontokban kell központilag telepíteni. Ezeket az alkalmazásokat jellemzően több adatközpontba helyezik üzembe, és globálisan elosztottnak nevezzük őket. A globálisan elosztott alkalmazásoknak globálisan elosztott adatbázisra van szükségük, amely transzparens módon replikálhatja az adatmennyiséget a világ bármely pontján, hogy az alkalmazások a felhasználókhoz közel lévő adatmásolaton működjenek. 

A Azure Cosmos DB egy globálisan elosztott adatbázis-szolgáltatás, amely alacsony késést, rugalmas méretezhetőséget és adatátvitelt, valamint jól definiált szemantikai adatkonzisztenciaet és magas rendelkezésre állást biztosít. Röviden, ha az alkalmazásnak a világ bármely pontján szükség van a gyors válaszidő biztosítására, és ha szükséges, hogy mindig online állapotba kerüljön, és az átviteli sebesség és a tárterület korlátlan és rugalmas méretezhetőségére van szüksége, az alkalmazást Azure Cosmos DB kell létrehoznia.

Az adatbázisokat úgy állíthatja be, hogy globálisan terjeszthetők legyenek, és elérhetők legyenek bármelyik Azure-régióban. Ha csökkenteni szeretné a késést, helyezze az adatait közel oda, ahová a felhasználók tartoznak. A szükséges régiók kiválasztása az alkalmazás globális elérhetősége és a felhasználók helyétől függ. Cosmos DB transzparens módon replikálja az adatait a Cosmos-fiókjához társított összes régióba. Egyetlen rendszerképet biztosít a globálisan elosztott Azure Cosmos-adatbázisról, valamint az alkalmazás által a helyileg olvasható és írható tárolók közül. 

A Azure Cosmos DB segítségével bármikor hozzáadhat vagy eltávolíthat a fiókjához társított régiókat. Régió hozzáadásához vagy eltávolításához nem kell sem felfüggeszteni, sem újra üzembe helyezni az alkalmazást. Továbbra is a szolgáltatás által natív módon biztosított többsoros képességek miatt továbbra is nagyon sok időt vesz igénybe.

![Magasan elérhető üzembe helyezési topológia](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>A globális terjesztés fő előnyei

**Hozzon létre globális aktív-aktív alkalmazásokat.** Az új, több főkiszolgálós replikációs protokollal minden régió támogatja az írásokat és az olvasásokat is. A több főkiszolgálós képesség is lehetővé teszi a következőket:

- Korlátlan rugalmas írási és olvasási méretezhetőség. 
- 99,999%-os olvasási és írási rendelkezésre állás a világ minden tájáról.
- A garantált olvasási és írási műveletek száma kevesebb, mint 10 ezredmásodperc a esetek 99% percentilis esetében.

Az Azure Cosmos DB többsoros API-k használatával az alkalmazás ismeri a legközelebbi régiót, és kéréseket küldhet az adott régiónak. A legközelebbi régió konfiguráció módosítása nélkül azonosítható. Az Azure Cosmos-fiókjához tartozó régiók hozzáadásakor és eltávolításakor az alkalmazásnak nem kell újratelepítenie vagy szüneteltetve lennie, ezért a rendszer mindig nagyon elérhető marad.

**Gyorsan reagáló alkalmazásokat készíthet.** Az alkalmazás közel valós idejű olvasási és írási műveleteket hajt végre az adatbázishoz választott összes régióban. A Azure Cosmos DB belsőleg kezeli az adatreplikációt a kiválasztott szinthez tartozó konzisztencia-szintű garanciával rendelkező régiók között.

**Kiválóan elérhető alkalmazások készítése.** Ha az adatbázist több régióban is futtatja, a világszerte növeli az adatbázisok rendelkezésre állását. Ha az egyik régió nem érhető el, a többi régió automatikusan kezeli az alkalmazás kéréseit. A Azure Cosmos DB 99,999%-os olvasási és írási rendelkezésre állást kínál a többrégiós adatbázisok számára.

**Az üzletmenet folytonosságának fenntartása a regionális kimaradások során.** A Azure Cosmos DB a regionális leállás során támogatja az [automatikus feladatátvételt](how-to-manage-database-account.md#automatic-failover) . Regionális leállás esetén a Azure Cosmos DB továbbra is fenntartja a késést, a rendelkezésre állást, a konzisztenciát és az átviteli sebesség SLA-kat. Annak érdekében, hogy a teljes alkalmazás nagyon elérhető legyen, Cosmos DB manuális feladatátvételi API-t biztosít a regionális kimaradás szimulálása érdekében. Az API használatával rendszeres üzletmenet-folytonossági gyakorlatokat végezhet.

**Az olvasási és írási sebesség méretezése globálisan.** Lehetővé teheti, hogy minden régió írható és rugalmasan méretezhető legyen az olvasás és a világ minden tájáról. Az alkalmazás által az Azure Cosmos-adatbázison vagy tárolón konfigurált átviteli sebesség garantált az Azure Cosmos-fiókhoz társított összes régióban. A kiosztott átviteli sebességet a [pénzügyi támogatással rendelkező SLA](https://aka.ms/acdbsla)-kat garantáljuk.

**Számos jól meghatározott konzisztencia-modell közül választhat.** A Azure Cosmos DB replikációs protokoll öt jól definiált, gyakorlatias és intuitív konzisztencia-modellt kínál. Mindegyik modell kompromisszumot biztosít a konzisztencia és a teljesítmény között. Ezekkel a konzisztencia-modellekkel globálisan elosztott alkalmazásokat hozhat létre könnyedén.

## <a id="Next Steps"></a>Következő lépések

A globális terjesztésről a következő cikkekben olvashat bővebben:

* [Globális terjesztés – a motorháztető alatt](global-dist-under-the-hood.md)
* [Több főkiszolgáló konfigurálása az alkalmazásokban](how-to-multi-master.md)
* [Ügyfelek konfigurálása a többhelyű-hez](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Régiók hozzáadása vagy eltávolítása a Azure Cosmos DB-fiókból](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Egyéni ütközés-feloldási szabályzat létrehozása SQL API-fiókokhoz](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programozható konzisztencia-modellek Cosmos DB](consistency-levels.md)
* [Az alkalmazás a megfelelő konzisztenciaszint kiválasztása](consistency-levels-choosing.md)
* [Konzisztenciaszintek az Azure Cosmos DB API-k között](consistency-levels-across-apis.md)
* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [Egyéni szinkronizálás megvalósítása a magasabb rendelkezésre állás és a teljesítmény optimalizálása érdekében](how-to-custom-synchronization.md)

---
title: A Azure Cosmos DB költségeinek megtervezése és kezelése
description: Megtudhatja, hogyan tervezheti meg és kezelheti Azure Cosmos DB költségeit a Cost Analysis használatával Azure Portalban.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 7f0a8fcb841399eb910f5f043cc75ddad037ee30
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88606854"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>A Azure Cosmos DB költségeinek megtervezése és kezelése

Ez a cikk bemutatja, hogyan tervezheti meg és kezelheti a Azure Cosmos DB költségeit:

- Becsülje meg, mi lesz a költsége a források létrehozása előtt
- A becsült költségek áttekintése az erőforrások használatának megkezdése után
- A Cost Management szolgáltatással megadhatja a költségvetéseket és figyelheti a költségeket
- Tekintse át az előre jelzett költségeket, és azonosítsa a kiadási trendeket, hogy felfedje azokat a területeket, ahol érdemes lehet cselekedni

Ismerje meg, hogy a Azure Cosmos DB költségei csak a havi költségek egy részét jelentik az Azure-számlán. Ha más Azure-szolgáltatásokat használ, akkor az Azure-előfizetésében használt összes Azure-szolgáltatás és erőforrás után, beleértve a harmadik féltől származó szolgáltatásokat is. Ez a cikk a Azure Cosmos DB költségeinek tervezését és kezelését ismerteti. Ha már ismeri a Azure Cosmos DB költségeinek kezelését, hasonló módszerekkel kezelheti az előfizetésében használt összes Azure-szolgáltatás költségeit.

## <a name="prerequisites"></a>Előfeltételek

A költségelemzés különböző Azure-fióktípusokat támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md). A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="provisioned-throughput-or-serverless"></a>Kiosztott átviteli sebesség vagy kiszolgáló nélküli

A Azure Cosmos DB kétféle kapacitási módot támogat: [kiépített átviteli sebességet](set-throughput.md) és [kiszolgáló](serverless.md)nélküli teljesítményt. A Azure Cosmos DB használatának felszámított módja a két mód között sokat változik, ezért fontos kiválasztani a számítási feladathoz legjobban megfelelőt. További útmutatásért és javaslatokért tekintse meg a [kiosztott átviteli sebesség és a kiszolgáló](throughput-serverless.md) nélküli cikk közötti választást ismertető cikket.

## <a name="estimating-provisioned-throughput-costs-with-capacity-calculator"></a>A kiépített átviteli sebesség becsült költsége a kapacitás-kalkulátorral

Ha Azure Cosmos DB kiépített átviteli sebességű módban szeretné használni, akkor az Azure Cosmos-fiók erőforrásainak létrehozása előtt a [Azure Cosmos db kapacitás-kalkulátor](https://cosmos.azure.com/capacitycalculator/) használatával becsülje meg a költségeket. A kapacitás-kalkulátor segítségével megbecsülheti a számítási feladatok szükséges átviteli sebességét és költségeit. Az Azure Cosmos-adatbázisok és-tárolók konfigurálása a számítási feladatokhoz megfelelő mennyiségű kiépített átviteli sebességgel vagy a [kérelmek egységével (ru/s)](request-units.md)a számítási feladatokhoz elengedhetetlen, hogy optimalizálja a költségeket és a teljesítményt. Olyan részleteket kell beírnia, mint például az API-típus, a régiók száma, az elem mérete, az olvasási/írási kérelmek másodpercenkénti száma, a teljes tárolt adat a költségbecslés érdekében. Ha többet szeretne megtudni a kapacitás-kalkulátorról, tekintse meg a [becslést](estimate-ru-with-capacity-planner.md) ismertető cikket.

Az alábbi képernyőfelvételen az átviteli sebesség és a költségbecslés látható a kapacitás-kalkulátor használatával:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Költségbecslés a Azure Cosmos DB Capacity kalkulátorban":::

## <a name="estimating-serverless-costs"></a>Kiszolgáló nélküli költségek becslése

Ha kiszolgáló nélküli módban tervezi használni a Azure Cosmos DBt, meg kell becsülnie, hogy hány [kérelem-egység](request-units.md) és GB tárterületet használ fel havi rendszerességgel. A kért egységek mennyiségét a havonta kiállított adatbázis-műveletek számának kiértékelésével becsülheti meg, és a megfelelő RU-költségeket megszorozva. A következő táblázat az általános adatbázis-műveletek becsült költségét sorolja fel:

| Művelet | Becsült költségek | Jegyzetek |
| --- | --- | --- |
| Elemek létrehozása | 5 RUs | 5 KB-nál kevesebb tulajdonsággal rendelkező 1 KB-os elemek átlagos díja |
| Elem módosítása | 10 RU | 5 KB-nál kevesebb tulajdonsággal rendelkező 1 KB-os elemek átlagos díja |
| Egyedi elemek olvasása az azonosító és a partíciós kulcs (pont – olvasás) alapján | 1 RU | 1 KB-os elemek átlagos díja |
| Elem törlése | 5 RUs | |
| Lekérdezés végrehajtása | 10 RU | Egy olyan lekérdezés átlagos díja, amely teljes mértékben kihasználja az [indexelést](index-overview.md) , és visszaadja a 100 vagy kevesebb eredményt |

> [!IMPORTANT] 
> Figyeljen a fenti táblázat megjegyzéseit. A műveletek tényleges költségeinek pontosabb becslése érdekében használhatja az [Azure Cosmos emulatort](local-emulator.md) , és megbecsülheti a [műveleteinek pontos költségét](find-request-unit-charge.md). Bár az Azure Cosmos Emulator nem támogatja a kiszolgáló nélküli működést, az adatbázis-műveletekhez standard RU-díjat jelent, és ezt a becslést is felhasználhatja.

Miután kiszámította a kérelmek teljes számát és a GB-nyi tárterületet, amelyet valószínűleg egy hónap alatt felhasznál, a következő képlet visszaadja a költségbecslést: **([a kérelmek száma]/1 000 000 * $0,25) + ([GB tárterület] * $0,25)**.

> [!NOTE]
> Az előző példában bemutatott költségek csak demonstrációs célokat szolgálnak. A legfrissebb díjszabási információkért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="review-estimated-costs-from-the-azure-portal"></a>A Azure Portal becsült költségeinek áttekintése

A Azure Portal Azure Cosmos DB erőforrásainak használatának megkezdése után megtekintheti a becsült költségeket. A következő lépésekkel tekintheti át a költségbecslést:

1. Jelentkezzen be a Azure Portalba, és navigáljon az Azure Cosmos-fiókjához.
1. Ugrás az **Áttekintés** szakaszra.
1. A **Cost** diagram alján keresse meg a következőt:. Ez a diagram a jelenlegi költségeket mutatja be egy konfigurálható időszakra vonatkozóan:
1. Hozzon létre egy új tárolót, például egy Graph-tárolót.
1. Adja meg a számítási feladatokhoz szükséges átviteli sebességet, például 400 RU/s. Az átviteli sebesség értékének megadása után a díjszabási becslést a következő képernyőképen látható módon jelenítheti meg:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Költségbecslés Azure Portal":::

## <a name="use-budgets-and-cost-alerts"></a>Költségvetések és költségriasztások használata

A költségek kezeléséhez [költségvetéseket](../cost-management/tutorial-acm-create-budgets.md) és riasztásokat hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. Előfordulhat azonban, hogy az egyes Azure-szolgáltatások költségei korlátozottak, például a Azure Cosmos DB költségei, mivel a költségek magasabb szinten való nyomon követésére szolgálnak.

Ha az Azure-előfizetése költségkerettel rendelkezik, az Azure megakadályozza a kreditek összegének megadását. Az Azure-erőforrások létrehozásakor és használatakor a rendszer felhasználja a krediteket. Ha eléri a hitelkeretét, az üzembe helyezett erőforrások a számlázási időszak további részében le lesznek tiltva. A hitelkeret nem módosítható, de eltávolíthatja. További információ a költségkeretekről: az [Azure](../billing/billing-spending-limit.md)költségkerete.

## <a name="monitor-costs"></a>Költségek figyelése

A Azure Cosmos DBekkel rendelkező erőforrások használatakor költségek merülnek fel. Az erőforrás-használati egység költségei az időintervallumok (másodperc, perc, óra és nap) vagy a kérési egység használata szerint változnak. Amint a Azure Cosmos DB használata megkezdődik, a költségek felmerülnek, és a Azure Portal [Cost Analysis](../cost-management/quick-acm-cost-analysis.md) ablaktáblájában láthatja őket.

A Cost Analysis használatakor különböző időintervallumok esetén megtekintheti a Azure Cosmos DBi költségeket gráfokban és táblázatokban. Néhány példa: nap, aktuális, előző hónap és év. A költségeket a költségvetések és az előre jelzett költségek között is megtekintheti. Ha a hosszabb nézetekre vált, az idő múlásával azonosíthatja a kiadási trendeket, és megtekintheti, hogy hol történt a túltöltés. Ha költségvetéseket hozott létre, azt is megteheti, hogy a megadottak hol vannak túllépve. Azure Cosmos DB költségek megtekintése a Cost Analysis szolgáltatásban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyissa meg a **Cost Management + számlázás** ablakot, válassza ki a menüből a **Cost Management** elemet, majd válassza a **Cost Analysis**lehetőséget. Ezután módosíthatja egy adott előfizetés hatókörét a **hatókör** legördülő menüből.

1. Alapértelmezés szerint az összes szolgáltatás díja megjelenik az első fánk-diagramon. Válassza ki a diagramot a "Azure Cosmos DB" címkével.

1. Egy szolgáltatás, például a Azure Cosmos DB költségeinek szűkítéséhez válassza a **szűrő hozzáadása** , majd a **szolgáltatásnév**lehetőséget. Ezután válassza ki a **Azure Cosmos db** elemet a listából. Íme egy példa, amely csak a Azure Cosmos DBra vonatkozó költségeket mutatja:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Költségek figyelése a Cost Analysis ablaktáblával":::

Az előző példában a február hónapra vonatkozóan a Azure Cosmos DB aktuális díja látható. A diagramok a hely és az erőforráscsoport szerint Azure Cosmos DB költségeket is tartalmazzák.

## <a name="next-steps"></a>Következő lépések

A díjszabással kapcsolatos további információkért tekintse meg a következő cikkeket Azure Cosmos DB:

* [Díjszabási modell az Azure Cosmos DB-ben](how-pricing-works.md)
* [A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben](optimize-cost-throughput.md)
* [A lekérdezési költségek optimalizálása az Azure Cosmos DB-ben](optimize-cost-queries.md)
* [A tárolási díj optimalizálása Azure Cosmos DB](optimize-cost-storage.md)
---
title: A Azure Cosmos DB költségeinek megtervezése és kezelése
description: Megtudhatja, hogyan tervezheti meg és kezelheti Azure Cosmos DB költségeit a Cost Analysis használatával Azure Portalban.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: 42421f745759d9aee75b285c3fbc6ea7217ba5c0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85112701"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>A Azure Cosmos DB költségeinek megtervezése és kezelése

Ez a cikk a Azure Cosmos DB költségeinek megtervezését és kezelését ismerteti. Először használja a Azure Cosmos DB Capacity kalkulátort, hogy az erőforrások hozzáadása előtt elősegítse a költségek megtervezését. Ezután az Azure-erőforrások hozzáadásakor áttekintheti a becsült költségeket. Azure Cosmos DB erőforrások használatának elkezdése után a Cost Management szolgáltatással állíthatja be a költségvetéseket, és figyelheti a költségeket. Emellett áttekintheti az előre jelzett költségeket, és azonosíthatja a kiadási trendeket, és azonosíthatja azokat a területeket, ahol érdemes lehet eljárni.

Ismerje meg, hogy a Azure Cosmos DB költségei csak a havi költségek egy részét jelentik az Azure-számlán. Ha más Azure-szolgáltatásokat használ, akkor az Azure-előfizetésében használt összes Azure-szolgáltatás és erőforrás után, beleértve a harmadik féltől származó szolgáltatásokat is. Ez a cikk a Azure Cosmos DB költségeinek tervezését és kezelését ismerteti. Ha már ismeri a Azure Cosmos DB költségeinek kezelését, hasonló módszerekkel kezelheti az előfizetésében használt összes Azure-szolgáltatás költségeit.

## <a name="prerequisites"></a>Előfeltételek

A költségelemzés különböző Azure-fióktípusokat támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md). A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="review-estimated-costs-with-capacity-calculator"></a>A becsült költségek áttekintése a kapacitás-kalkulátorral

Az erőforrásoknak az Azure Cosmos-fiókban való létrehozása előtt a [Azure Cosmos db kapacitás-kalkulátor](https://cosmos.azure.com/capacitycalculator/) használatával becsülheti meg a költségeket. A kapacitás-kalkulátor segítségével megbecsülheti a számítási feladatok szükséges átviteli sebességét és költségeit. Az Azure Cosmos-adatbázisok és-tárolók konfigurálása a számítási feladatokhoz megfelelő mennyiségű kiépített átviteli sebességgel vagy a [kérelmek egységével (ru/s)](request-units.md)a számítási feladatokhoz elengedhetetlen, hogy optimalizálja a költségeket és a teljesítményt. Olyan részleteket kell beírnia, mint például az API-típus, a régiók száma, az elem mérete, az olvasási/írási kérelmek másodpercenkénti száma, a teljes tárolt adat a költségbecslés érdekében. Ha többet szeretne megtudni a kapacitás-kalkulátorról, tekintse meg a [becslést](estimate-ru-with-capacity-planner.md) ismertető cikket.

Az alábbi képernyőfelvételen az átviteli sebesség és a költségbecslés látható a kapacitás-kalkulátor használatával:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Költségbecslés a Azure Cosmos DB Capacity kalkulátorban":::

## <a name="review-estimated-costs-from-the-azure-portal"></a>A Azure Portal becsült költségeinek áttekintése

Azure Cosmos DB erőforrások Azure Portalból való létrehozásakor a becsült költségek láthatók. A következő lépésekkel tekintheti át a költségbecslést:

1. Jelentkezzen be a Azure Portalba, és navigáljon az Azure Cosmos-fiókjához.
1. Lépjen a **adatkezelő**.
1. Hozzon létre egy új tárolót, például egy Graph-tárolót.
1. Adja meg a számítási feladatokhoz szükséges átviteli sebességet, például 400 RU/s. Az átviteli sebesség értékének megadása után a díjszabási becslést a következő képernyőképen látható módon jelenítheti meg:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Költségbecslés Azure Portal":::

Ha az Azure-előfizetése költségkerettel rendelkezik, az Azure megakadályozza a kreditek összegének megadását. Az Azure-erőforrások létrehozásakor és használatakor a rendszer felhasználja a krediteket. Ha eléri a hitelkeretét, az üzembe helyezett erőforrások a számlázási időszak további részében le lesznek tiltva. A hitelkeret nem módosítható, de eltávolíthatja. További információ a költségkeretekről: az [Azure](../billing/billing-spending-limit.md)költségkerete.

## <a name="use-budgets-and-cost-alerts"></a>Költségvetések és költségriasztások használata

A költségek kezeléséhez [költségvetéseket](../cost-management/tutorial-acm-create-budgets.md) és riasztásokat hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. Előfordulhat azonban, hogy az egyes Azure-szolgáltatások költségei korlátozottak, például a Azure Cosmos DB költségei, mivel a költségek magasabb szinten való nyomon követésére szolgálnak.

## <a name="monitor-costs"></a>Költségek figyelése

A Azure Cosmos DBekkel rendelkező erőforrások használatakor költségek merülnek fel. Az erőforrás-használati egység költségei az időintervallumok (másodperc, perc, óra és nap) vagy a kérési egység használata szerint változnak. Amint a Azure Cosmos DB használata megkezdődik, a költségek felmerülnek, és a Azure Portal [Cost Analysis](../cost-management/quick-acm-cost-analysis.md) ablaktáblájában láthatja őket.

A Cost Analysis használatakor különböző időintervallumok esetén megtekintheti a Azure Cosmos DBi költségeket gráfokban és táblázatokban. Néhány példa: nap, aktuális, előző hónap és év. A költségeket a költségvetések és az előre jelzett költségek között is megtekintheti. Ha a hosszabb nézetekre vált, az idő múlásával azonosíthatja a kiadási trendeket, és megtekintheti, hogy hol történt a túltöltés. Ha költségvetéseket hozott létre, azt is megteheti, hogy a megadottak hol vannak túllépve. Azure Cosmos DB költségek megtekintése a Cost Analysis szolgáltatásban:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com).

1. Nyissa meg a **Cost Management + számlázás** ablakot, válassza ki a menüből a **Cost Management** elemet, majd válassza a **Cost Analysis**lehetőséget. Ezután módosíthatja egy adott előfizetés hatókörét a **hatókör** legördülő menüből.

1. Alapértelmezés szerint az összes szolgáltatás díja megjelenik az első fánk-diagramon. Válassza ki a diagramot a "Azure Cosmos DB" címkével.

1. Egy szolgáltatás, például a Azure Cosmos DB költségeinek szűkítéséhez válassza a **szűrő hozzáadása** , majd a **szolgáltatásnév**lehetőséget. Ezután válassza ki a **Azure Cosmos db** elemet a listából. Íme egy példa, amely csak a Azure Cosmos DBra vonatkozó költségeket mutatja:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Költségek figyelése a Cost Analysis ablaktáblával":::

Az előző példában a február hónapra vonatkozóan a Azure Cosmos DB aktuális díja látható. A diagramok a hely és az erőforráscsoport szerint Azure Cosmos DB költségeket is tartalmazzák.

## <a name="next-steps"></a>További lépések

A díjszabással kapcsolatos további információkért tekintse meg a következő cikkeket Azure Cosmos DB:

* [Díjszabási modell az Azure Cosmos DB-ben](how-pricing-works.md)
* [A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben](optimize-cost-throughput.md)
* [A lekérdezési költségek optimalizálása az Azure Cosmos DB-ben](optimize-cost-queries.md)
* [A tárolási díj optimalizálása Azure Cosmos DB](optimize-cost-storage.md)
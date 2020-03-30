---
title: Az Azure Cosmos DB költségeinek megtervezése és kezelése
description: Megtudhatja, hogyan tervezhet és kezelhet költségeket az Azure Cosmos DB számára az Azure Portal költségelemzésével.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: cf259dfa0b00393cf0fee259b199527569ba1792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152584"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Az Azure Cosmos DB költségeinek megtervezése és kezelése

Ez a cikk ismerteti, hogyan tervezheti és kezelheti az Azure Cosmos DB költségeit. Először használja az Azure Cosmos DB kapacitáskalkulátor segítségével megtervezze a költségeket, mielőtt bármilyen erőforrást hozzá. Ezután az Azure-erőforrások hozzáadásakor áttekintheti a becsült költségeket. Miután elkezdte használni az Azure Cosmos DB-erőforrásokat, használja a költségkezelési funkciókat a költségvetések beállításához és a költségek figyeléséhez. Áttekintheti az előre jelzett költségeket is, és azonosíthatja a költési trendeket, hogy azonosíthassa azokat a területeket, ahol esetleg cselekedni szeretne.

Értse meg, hogy az Azure Cosmos DB költségei csak egy részét teszik ki az Azure-számlán. Ha más Azure-szolgáltatásokat is használ, az Azure-előfizetésében használt összes Azure-szolgáltatásért és erőforrásért díjat kell fizetnie, beleértve a külső szolgáltatásokat is. Ez a cikk bemutatja, hogyan tervezhet és kezelhetők az Azure Cosmos DB költségei. Miután már ismeri az Azure Cosmos DB költségeinek kezelését, hasonló módszereket alkalmazhat az előfizetésben használt összes Azure-szolgáltatás költségeinek kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

A költségelemzés különböző Azure-fióktípusokat támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md). A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="review-estimated-costs-with-capacity-calculator"></a>Becsült költségek áttekintése kapacitáskalkulátorral

Az [Azure Cosmos DB kapacitáskalkulátor](https://cosmos.azure.com/capacitycalculator/) segítségével becsülje meg a költségeket, mielőtt létrehozna egy Azure Cosmos-fiókban lévő erőforrásokat. A kapacitáskalkulátor segítségével becslést kaphat a szükséges átviteli képességről és a számítási feladatok költségéről. Az Azure Cosmos-adatbázisok és -tárolók konfigurálása a megfelelő mennyiségű kiosztott átviteli fokkal, vagy [kérésegységek (RU/ek)](request-units.md)a számítási feladatok elengedhetetlen a költségek és a teljesítmény optimalizálása. Meg kell adnia a részleteket, például az API típusa, a régiók száma, az elem mérete, olvasási/írási kérelmek másodpercenként, a teljes tárolt adatok at a költségbecslés. A kapacitáskalkulátorról a [becslési](estimate-ru-with-capacity-planner.md) cikkben olvashat bővebben.

A következő képernyőkép az átviteli és költségbecslést mutatja a kapacitáskalkulátor használatával:

![Költségbecslés az Azure Cosmos DB kapacitáskalkulátorában](./media/plan-manage-costs/capacity-calculator-cost-estimate.png)

## <a name="review-estimated-costs-from-the-azure-portal"></a>Tekintse át a becsült költségeket az Azure Portalról

Ahogy létrehozza az Azure Cosmos DB-erőforrásokat az Azure Portalról, láthatja a becsült költségeket. A költségbecslés áttekintéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalon, és keresse meg az Azure Cosmos-fiókját.
1. Nyissa meg az **Adatkezelőt**.
1. Hozzon létre egy új tárolót, például egy gráftárolót.
1. Adja meg a számítási feladatokhoz , például 400 RU/s-hoz szükséges átviteli. Az átviteli érték beírása után láthatja az árképzési becslést az alábbi képernyőképen látható módon:

   ![Költségbecslés az Azure Portalon](./media/plan-manage-costs/cost-estimate-portal.png)

Ha az Azure-előfizetés rendelkezik egy költési limit, az Azure megakadályozza, hogy a kiadások több mint a hitel összege. Az Azure-erőforrások létrehozásakor és használatakor a kreditek fellesznek használva. Amikor eléri a hitelkeretet, a telepített erőforrások le vannak tiltva a számlázási időszak hátralévő részében. A hitelkeret nem módosítható, de eltávolíthatja azt. A költési limitekről az [Azure költési limitcímű](../billing/billing-spending-limit.md)témakörben talál további információt.

## <a name="use-budgets-and-cost-alerts"></a>Költségvetések és költségriasztások használata

A költségek kezeléséhez [költségvetéseket](../cost-management/tutorial-acm-create-budgets.md) és riasztásokat hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. A költségvetések és riasztások az Azure-előfizetésekhez és erőforráscsoportokhoz jönnek létre, így hasznosak egy átfogó költségfigyelési stratégia részeként. Előfordulhat azonban, hogy korlátozott funkcionalitással rendelkeznek az egyes Azure-szolgáltatások költségeinek, például az Azure Cosmos DB költségeinek kezeléséhez, mivel a költségek magasabb szintű nyomon követésére szolgálnak.

## <a name="monitor-costs"></a>A költségek figyelése

Az Azure Cosmos DB erőforrások használatával költségek merülnek fel. Az erőforrás-felhasználási egység költségei időintervallumok (másodperc, perc, óra és nap) vagy kérésegység-használat szerint változnak. Amint elindul az Azure Cosmos DB használata, költségek merülnek fel, és láthatja őket az Azure Portal [költségelemzési](../cost-management/quick-acm-cost-analysis.md) ablaktáblájában.

Költségelemzés használatakor megtekintheti az Azure Cosmos DB költségeit grafikonokban és táblázatokban a különböző időintervallumok. Néhány példa a nap, az aktuális, az előző hónap és az év szerint. A költségeket a költségvetések és az előre jelzett költségek alapján is megtekintheti. A hosszabb nézetekre való váltás sal segíthet azonosítani a költési trendeket, és megtekinteni, hogy hol történhetett túlköltekezés. Ha létrehozott költségkereteket, azt is könnyen láthatja, hogy hol lépték túl. Az Azure Cosmos DB költségeinek megtekintése a költségelemzésben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Nyissa meg a **Költségkezelés + Számlázás** ablakot, válassza a menü **Költségkezelés** parancsát, majd válassza a **Költségelemzés**lehetőséget. Ezután módosíthatja egy adott előfizetés hatókörét a **Hatókör legördülő menüből.**

1. Alapértelmezés szerint az összes szolgáltatás költsége az első fánkdiagramon jelenik meg. Válassza ki a területet a diagram "Azure Cosmos DB" címkével ellátott.

1. Egyetlen szolgáltatás, például az Azure Cosmos DB költségeinek szűkítéséhez válassza **a Szűrő hozzáadása** lehetőséget, majd a Szolgáltatás név **lehetőséget.** Ezután válassza az **Azure Cosmos DB-t** a listából. Íme egy példa, amely csak az Azure Cosmos DB költségeit mutatja be:
 
   ![Költségek figyelése a Költségelemzés ablaktáblával](./media/plan-manage-costs/cost-analysis-pane.png)

Az előző példában az Azure Cosmos DB aktuális költségét láthatja a február hónapra. A diagramok is tartalmazzák az Azure Cosmos DB költségek hely és erőforráscsoport szerint.

## <a name="next-steps"></a>További lépések

Az alábbi cikkekből megtudhatja, hogyan működik a díjszabás az Azure Cosmos DB-ben:

* [Díjszabási modell az Azure Cosmos DB-ben](how-pricing-works.md)
* [A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben](optimize-cost-throughput.md)
* [A lekérdezési költségek optimalizálása az Azure Cosmos DB-ben](optimize-cost-queries.md)
* [A tárolási költség optimalizálása az Azure Cosmos DB-ben](optimize-cost-storage.md)
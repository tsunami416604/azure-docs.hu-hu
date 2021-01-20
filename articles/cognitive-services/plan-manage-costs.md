---
title: Az Azure Cognitive Services költségeinek kezelésének megtervezése
description: Ismerje meg, hogyan tervezheti meg és kezelheti az Azure-Cognitive Services költségeit a Azure Portalban a Cost Analysis használatával.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 925a092eb67fa459213a37df0fc6b3f7a1b8a0fb
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602360"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Az Azure Cognitive Services költségeinek megtervezése és kezelése

Ez a cikk bemutatja, hogyan tervezheti meg és kezelheti az Azure-Cognitive Services költségeit. Először is az Azure díjszabási kalkulátor segítségével tervezze meg a Cognitive Services költségeket, mielőtt felveszi a költségek kiszámításához szükséges erőforrásokat a szolgáltatáshoz. Ezután az Azure-erőforrások hozzáadásakor tekintse át a becsült költségeket. A Cognitive Services-erőforrások használatának elkezdése (például beszéd, Computer Vision, LUIS, Text Analytics, Translator stb.) használata után Cost Management funkciókkal állíthatja be a költségvetéseket, és figyelheti a költségeket. Áttekintheti az előre jelzett költségeket, és azonosíthatja a kiadási trendeket, és azonosíthatja azokat a területeket, ahol érdemes lehet eljárni. A Cognitive Services költségei csak a havi költségek egy részét jelentik az Azure-számlán. Bár ez a cikk bemutatja, hogyan tervezheti meg és kezelheti a Cognitive Services költségeit, az Azure-előfizetésében használt összes Azure-szolgáltatás és-erőforrás után, beleértve a külső szolgáltatásokat is.

## <a name="prerequisites"></a>Előfeltételek

A Cost Analysis Cost Management támogatja a legtöbb Azure-fiók típusát, de nem mindegyiket. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). A költségadatok megtekintéséhez legalább olvasási hozzáférésre van szüksége egy Azure-fiókhoz. További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>A költségek becslése a használata előtt Cognitive Services

Az [Azure díjszabási kalkulátorával](https://azure.microsoft.com/pricing/calculator/) a Cognitive Services hozzáadása előtt megbecsülheti a költségeket.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Az Azure díjszabási kalkulátora Cognitive Services" border="true":::

Amikor új erőforrásokat ad hozzá a munkaterülethez, térjen vissza ehhez a kalkulátorhoz, és adja hozzá ugyanezt az erőforrást a költségbecslés frissítéséhez.

További információ: [Azure Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>A Cognitive Services teljes számlázási modelljének megismerése

Cognitive Services az Azure-infrastruktúrán fut, amely az új erőforrás telepítésekor [felmerülő költségekkel jár](https://azure.microsoft.com/pricing/details/cognitive-services/) . Fontos tisztában lenni azzal, hogy további infrastruktúra merülhet fel. Az üzembe helyezett erőforrások módosításakor ezt a költségeket kell kezelnie. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Általában a Cognitive Servicesekkel felmerülő költségek

Az Azure-erőforrások üzembe helyezése után általában a díjszabási szintet és az API-hívásokat a végponton kell meghatározni. Ha az Ön által használt szolgáltatás kötelezettségvállalási szinten van, akkor az adott szinten eljuttatott hívások túllépik a túlterhelést.

A szolgáltatások használatakor további költségek merülhetnek fel:

#### <a name="qna-maker"></a>QnA Maker

Ha QnA Maker erőforrásokat hoz létre, az egyéb Azure-szolgáltatások erőforrásai is létrehozhatók. Ezek közé tartoznak például az alábbiak:

- [Azure App Service (a futtatókörnyezethez)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search (az adatkezeléshez)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Az erőforrás törlése után felmerülhető költségek

#### <a name="qna-maker"></a>QnA Maker

QnA Maker erőforrások törlését követően a következő erőforrások továbbra is létezhetnek. Amíg nem törli őket, továbbra is felmerülnek a költségek.

- [Azure App Service (a futtatókörnyezethez)](https://azure.microsoft.com/pricing/details/app-service/)
- [Azure Cognitive Search (az adatkezeléshez)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Azure-előfizetési kredit használata Cognitive Services

Az Azure-előfizetéssel (korábban pénzügyi kötelezettségvállalásnak nevezett) Kredittel Cognitive Services díjat fizethet. Az Azure-beli előfizetési kreditek azonban nem használhatók fel a harmadik féltől származó termékekhez és szolgáltatásokhoz, például az Azure piactéren fizetendő díjakért.

## <a name="create-budgets"></a>Költségvetések létrehozása

A költségek kezeléséhez [költségvetéseket](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. 

A költségvetések az Azure-ban meghatározott erőforrásokhoz vagy szolgáltatásokhoz szűrőkkel hozhatók létre, ha a figyelésben részletesebb részletességre van szükség. A szűrők segítségével biztosíthatja, hogy véletlenül ne hozzon létre olyan új erőforrásokat, amelyek további pénzbe kerülnek. Ha többet szeretne megtudni a szűrési lehetőségekről, amikor költségvetést hoz létre, tekintse meg a [csoportosítási és szűrési beállítások](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)című témakört.

## <a name="export-cost-data"></a>Költségadatok exportálása

A költségadatok a Storage-fiókba is [exportálhatók](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Ez akkor hasznos, ha szüksége van rá, vagy másoknak további adatelemzést kell végeznie a költségekért. A pénzügyi csapat például az Excelben vagy a Power BI használatával elemezheti az adatforrást. A költségeket napi, heti vagy havi rendszerességgel exportálhatja, és egyéni dátumtartományt is beállíthat. A költségadatok exportálásának ajánlott módja a Cost-adatkészletek beolvasása.

<!--
## Other ways to manage and reduce costs for Cognitive Services

Work with Dean to complete this section in 2021.

-->

## <a name="next-steps"></a>További lépések

- Megtudhatja [, hogyan optimalizálhatja a felhőalapú befektetéseit Azure Cost Managementokkal](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- További információ a költségek a [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)szolgáltatással történő kezeléséről.
- További információ a [váratlan költségek megelőzéséről](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Vegye figyelembe a [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) interaktív tanulás tanfolyamát.

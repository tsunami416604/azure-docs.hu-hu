---
title: Csoportosítási és szűrési lehetőségek az Azure Cost Managementben
description: Ez a cikk a csoportosítási és szűrési lehetőségek Azure Cost Managementben történő használatát mutatja be.
author: bandersmsft
ms.author: banders
ms.date: 06/08/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 82e542eafe1578a969c4348fe7562ba1da106e2c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88683402"
---
# <a name="group-and-filter-options-in-cost-analysis"></a>Csoportosítási és szűrési lehetőségek a költségelemzésben

A költségelemzés számos csoportosítási és szűrési lehetőséget kínál. Ennek a cikknek a segítségével megtudhatja, mikor kell használni őket.

A csoportosítási és szűrési beállításokkal kapcsolatos videó megtekintéséhez tekintse meg a [Cost Management dimenziók és címkék szerinti jelentéskészítésével](https://www.youtube.com/watch?v=2Vx7V17zbmk) foglalkozó videót. További videók megtekintéséhez látogasson el a [Cost Management YouTube-csatornájára](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/2Vx7V17zbmk]

## <a name="group-and-filter-properties"></a>Csoportosítási és szűrési tulajdonságok

Az alábbi táblázat felsorolja a költségelemzésben elérhető leggyakoribb csoportosítási és szűrési lehetőségek némelyikét, valamint ismerteti, hogy mikor érdemes alkalmazni őket.

| Tulajdonság | A következő esetekben használja | Megjegyzések |
| --- | --- | --- |
| **Rendelkezésre állási zónák** | Az AWS-költségek lebontása rendelkezésreállási zóna szerint. | Csak AWS-hatókörök és felügyeleti csoportok esetén alkalmazható. Az Azure-beli adatok nem tartalmazzák a rendelkezésreállási zónát, így ezeknél a **Nem alkalmazható** érték jelenik meg. |
| **Számlázási időszak** | A PAYG-költségek lebontása a számlázási hónap szerint. | A **Számlázási időszak** segítségével pontos képet kaphat a számlázott PAYG-díjakról. Foglaljon bele további 2 napot a számlázási időszak előtt és után, ha egy egyéni dátumtartományra szűr. A pontos számlázási időszakra való korlátozás nem fog egyezni a számlával. A számlázási időszak összes számlájára vonatkozó költségek fognak megjelenni. Egy adott számlára a **Számlaazonosító** segítségével szűrhet. Csak PAYG-előfizetésekre alkalmazható, mivel az EA és az MCA számlázása a naptári hónapok szerint történik. Az EA-/MCA-fiókok esetében ugyanebből a célból naptári hónapok vagy havi részletesség is használható a dátumválasztóban. |
| **Díj típusa** | A használat, a vásárlás, a visszatérítés és a nem használt foglalási költségek lebontása. | A foglalásvásárlások és a visszatérítések csak a tényleges költségek használatakor érhetők el, amortizált költségek esetén nem. A fel nem használt foglalási költségek csak akkor érhetők el, ha az amortizált költségeket jeleníti meg. |
| **Részleg** | Költségek lebontása EA-részlegek szerint. | Csak az EA és a felügyeleti csoportok számára érhető el. A PAYG-előfizetéseknek nincs részlegük, így **Nem alkalmazható** vagy **Nincs hozzárendelve** értéket fognak megjeleníteni. |
| **Regisztrációs fiók** | Költségek lebontása EA-fióktulajdonos szerint. | Csak az EA-számlázási fiókok, -részlegek és felügyeleti csoportok számára érhető el. A PAYG-előfizetések nem rendelkeznek EA-regisztrációs fiókokkal, így **Nem alkalmazható** vagy **Nincs hozzárendelve** értéket fognak megjeleníteni. |
| **Gyakoriság** | A használaton alapuló, az egyszeri és az ismétlődő költségek lebontása. | |
| **Számlaazonosító** | Költségek lebontása kiállított számla alapján. | A ki nem számlázott díjak még nem rendelkeznek számlaazonosítóval, így az EA-költségek nem tartalmazzák a számla részleteit, valamint **Nem alkalmazható** értéket fognak megjeleníteni.  |
| **Fogyasztásmérő** | Költségek lebontása használatmérés alapján. | A vásárlások és a Marketplace-használat **Nem alkalmazható** értékként jelennek meg. A vásárlások azonosításához tekintse meg a **Díj típusa** értéket, a Marketplace-díjak azonosításához pedig a **Kiadó típusa** értéket. |
| **Művelet** | Az AWS-költségek lebontása műveletek szerint. | Csak AWS-hatókörök és felügyeleti csoportok esetén alkalmazható. Az Azure-beli adatok nem tartalmaznak műveletet, így ezeknél a **Nem alkalmazható** érték jelenik meg – használja helyette a **Fogyasztásmérő** lehetőséget. |
| **Díjszabási modell** | A költségek igény, foglalás vagy spot-használat szerinti lebontása. | A vásárlások **OnDemand** értékként jelennek meg. Ha **Nem alkalmazható** értéket lát, a **Foglalás** szerinti csoportosítással megállapíthatja, hogy foglalásról vagy igény szerinti használatról van-e szó, a **Díj típusa** szerintivel pedig azonosíthatja a vásárlásokat.
| **Szolgáltató** | Költségek lebontása AWS és Azure szerint. | Csak felügyeleti csoportok számára érhető el. |
| **Kiadó típusa** | Az AWS, az Azure és a Marketplace költségeinek lebontása. |  |
| **Foglalás** | Költségek lebontása foglalás szerint. | A foglaláshoz nem társított használat vagy vásárlások **Nem alkalmazható** értékként jelennek meg. A **Kiadó típusa** szerinti csoportosítással azonosíthatja az egyéb Azure-, AWS- vagy Marketplace-vásárlásokat. |
| **Erőforrás** | Költségek lebontása erőforrás szerint. | A vásárlások **Nem alkalmazható** értékként jelennek meg, mert egy EA-/PAYG-számlázási fiók vagy egy MCA-számlázási profil szintjén vannak alkalmazva, és nem egy adott erőforráshoz vannak társítva. A **Kiadó típusa** szerinti csoportosítással azonosíthatja az egyéb Azure-, AWS- vagy Marketplace-vásárlásokat. |
| **Erőforráscsoport** | Költségek lebontása erőforráscsoport szerint. | A vásárlások, az előfizetéshez nem társított bérlői erőforrások, az erőforráscsoporthoz nem üzembe helyezett előfizetési erőforrások, illetve a klasszikus erőforrások nem rendelkeznek erőforráscsoporttal, így **egyebek**, **klasszikus szolgáltatások**, **$system** vagy **nem alkalmazható** értéket jelenítenek meg. |
| **Erőforrás típusa** | Költségek lebontása erőforrástípus szerint. | A vásárlások és a klasszikus szolgáltatások nem rendelkeznek Azure Resource Manager-erőforrástípussal, így **egyebek**, **klasszikus szolgáltatások** vagy **nem alkalmazható** értéket jelenítenek meg. |
| **Erőforrás helye** | Költségek lebontása hely vagy régió szerint. | A vásárlások és a Marketplace-használat **nem hozzárendelt**, **ismeretlen**, **nem leképezett** vagy **nem alkalmazható** értékként jelenhetnek meg. |
| **Szolgáltatásnév** vagy **Fogyasztásmérő kategóriája** | Költségek lebontása az Azure-szolgáltatás szerint. | A vásárlások és a Marketplace-használat **nem alkalmazható** vagy **nem hozzárendelt** értékként jelennek meg. |
| **Szolgáltatásszint** vagy **Fogyasztásmérő alkategóriája** | A költségek lebontása az Azure fogyasztásmérőjének osztályozási alkategóriái alapján. | A vásárlások és a Marketplace-használat **nem alkalmazható** vagy **nem hozzárendelt** értékként jelennek meg. |
| **Előfizetés** | Költségek lebontása az Azure-előfizetés és az AWS csatolt fiókja alapján. | A vásárlások és a bérlői erőforrások **nem alkalmazható** értékként jelenhetnek meg. |
| **Tag** | Költségek lebontása egy adott címkekulcs címkeértékei alapján. | A címkék nem érhetők el a vásárlásokhoz, az előfizetésekhez nem társított bérlői erőforrásokhoz, az erőforráscsoporthoz nem üzembe helyezett előfizetési erőforrásokhoz vagy a klasszikus erőforrásokhoz. Egyes szolgáltatások nem tartalmaznak címkéket a használati adatokban. További információ [a címkék támogatásáról az egyes erőforrástípusokra vonatkozóan](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support). |

A kifejezésekkel kapcsolatos további információ: [Az Azure használati adatait és díjait tartalmazó fájl kifejezéseinek ismertetése](../understand/understand-usage.md).

## <a name="next-steps"></a>További lépések

- [Indítsa el a költségelemzést](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis).

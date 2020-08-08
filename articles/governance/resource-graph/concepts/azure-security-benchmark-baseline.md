---
title: Az Azure Resource Graph biztonsági alapterve az Azure biztonsági teljesítményteszthez
description: Az Azure Resource Graph biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: resource-graph
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: d2ef76a054642807f0d72a758ae084a19557caf8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009001"
---
# <a name="azure-resource-graph-security-baseline-for-azure-security-benchmark"></a>Az Azure Resource Graph biztonsági alapterve az Azure biztonsági teljesítményteszthez

Ez a biztonsági alapkonfiguráció az Azure-beli [biztonsági teljesítményteszttel](../../../security/benchmarks/overview.md) kapcsolatos útmutatót alkalmazza az Azure Resource Graph-ra. Az Azure biztonsági teljesítményteszt az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint az Azure Resource Graph-ra vonatkozó kapcsolódó útmutatás. Az Azure Resource Graph-ra nem alkalmazható **vezérlők** ki vannak zárva. Ha szeretné megtekinteni, hogy az Azure Resource Graph hogyan teljes mértékben leképezi az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes azure Virtual Network biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)



## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](../../../security/benchmarks/security-control-identity-access-control.md).*

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az Azure Resource Graph az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) alapján biztosít hozzáférést az erőforrás-típusokhoz és a tulajdonságokhoz. Rendszeresen naplózza és áttekintheti a rendszerbiztonsági tag (felhasználók, csoportok és szolgáltatásfiókok) számára biztosított hozzáférést, így biztosítva, hogy a lekérdezések a megfelelő erőforrások eredményét adják vissza.

* [Engedélyek az Azure Resource Graphban](../overview.md#permissions-in-azure-resource-graph)

* [Az Azure Identity hozzáférési felülvizsgálatok használata](../../../active-directory/governance/access-reviews-overview.md)


**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](../../../security/benchmarks/security-control-data-protection.md)adatvédelem.*

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: szerepköralapú hozzáférés-vezérlés (RBAC) használata az adat-és erőforrásokhoz való hozzáférés szabályozásához. Az Azure Resource Graph használatához megfelelő hozzáféréssel kell rendelkeznie a lekérdezni kívánt erőforrásokhoz is. Ennek a hozzáférésnek csak olvashatónak kell lennie, és csak a szükséges személyzet számára adható meg.

* [Engedélyek az Azure Resource Graphban](../overview.md#permissions-in-azure-resource-graph)

* [A RBAC konfigurálása az Azure-ban](../../../role-based-access-control/role-assignments-rest.md)


**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](../../../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetések, a felügyeleti csoportok és a bérlők által támogatott összes erőforrást. Győződjön meg arról, hogy rendelkezik megfelelő engedélyekkel a bérlőben, és képes az összes Azure-előfizetés, valamint az előfizetésében lévő erőforrások számbavételére.

* [Lekérdezések létrehozása az Azure Resource Graph használatával](../first-query-portal.md)

* [Az Azure RBAC ismertetése](../../../role-based-access-control/overview.md)


**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása a szervezeti igényeknek megfelelően. Az Azure Resource Graph segítségével lekérdezheti a jóváhagyott Azure-erőforrásokat, és megtekintheti az előzményeket (előzetes verzió) a pillanatképek áttekintéséhez és a változásokról.

* [Azure-erőforrások lekérdezése az Azure Resource Graphtal](../first-query-portal.md)

* [Erőforrás-módosítások lekérése](../how-to/get-resource-changes.md)


**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetések, a felügyeleti csoportok és a bérlők erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

* [Azure-erőforrások lekérdezése az Azure Resource Graphtal](../first-query-portal.md)

* [Minták: az Azure Resource Graph kezdő lekérdezései](../samples/starter.md)


**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="next-steps"></a>További lépések

- Lásd az [Azure biztonsági teljesítménytesztét](../../../security/benchmarks/overview.md)
- További információ az [Azure biztonsági alaptervekről](../../../security/benchmarks/security-baselines-overview.md)

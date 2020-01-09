---
title: Az Azure-előfizetések működési Uram javítása Azure Advisor
description: Az Advisor használatával optimalizálhatja és kihasználhatja az Azure-előfizetések működési kiválóságát
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443079"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Az operatív kiválóság elérése Azure Advisor

Azure Advisor működési kiválósági javaslatok segítenek az ügyfeleknek a folyamat-és munkafolyamat-hatékonysággal, az erőforrás-kezelhetőséggel és az üzembe helyezéssel kapcsolatos ajánlott eljárásokkal Ezeket a javaslatokat az Advisor az Advisor irányítópultjának **működési kiválóság** lapján érheti el.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure Service Health riasztások létrehozása, amelyekről értesítést kap, ha az Azure-problémák érintik Önt

Javasoljuk, hogy Azure Service Health riasztások beállításával értesítést kapjon, ha az Azure-szolgáltatással kapcsolatos problémák érintik Önt. A [Azure Service Health](https://azure.microsoft.com/features/service-health/) egy ingyenes szolgáltatás, amely személyre szabott útmutatást és támogatást nyújt, ha egy Azure-szolgáltatással kapcsolatos probléma jelentkezik. Az Advisor olyan előfizetéseket azonosít, amelyek nincsenek konfigurálva riasztások, és azt javasolja, hogy hozzon létre egyet.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Tervezze meg a Storage-fiókokat, hogy megakadályozza a maximális előfizetési korlátot

Egy Azure-régió legfeljebb 250 Storage-fiókot tud támogatni előfizetésre. Ha elérte a korlátot, nem fog tudni további Storage-fiókokat létrehozni az adott régióban/előfizetések kombinációjában. Az Advisor megkeresi az előfizetéseket és a felületre vonatkozó javaslatokat, hogy kevesebb Storage-fiókot tervezzen meg, amelyek közel állnak a maximális korlát eléréséhez.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ellenőrizze, hogy van-e hozzáférése az Azure Cloud experthez, amikor szüksége van rá

Üzleti szempontból kritikus fontosságú számítási feladatok futtatásakor fontos, hogy szükség esetén hozzáférhessen a technikai támogatáshoz. Az Advisor olyan potenciális üzleti szempontból kritikus előfizetéseket azonosít, amelyek nem rendelkeznek technikai támogatással a támogatási csomagban, és javaslatot tesz arra, hogy a technikai támogatást tartalmazó lehetőségre frissítsen.

## <a name="repair-invalid-log-alert-rules"></a>Érvénytelen naplózási riasztási szabályok javítása

Azure Advisor felismeri azokat a riasztási szabályokat, amelyek érvénytelen lekérdezésekkel rendelkeznek a feltételek szakaszban. A naplóriasztási szabályok az Azure Monitorban hozhatók létre, és elemzési lekérdezések adott időközönkénti futtatására szolgálnak. A lekérdezés eredményei határozzák meg, hogy egy riasztást aktiválni kell-e. Az elemzési lekérdezések idővel érvénytelenné válhatnak a hivatkozott erőforrásokban, táblákban vagy parancsokban bekövetkező változások miatt. Az Advisor azt ajánlja, hogy javítsa ki a lekérdezést a riasztási szabályban, hogy megakadályozza az automatikus letiltását, és biztosítsa az Azure-ban lévő erőforrások lefedettségének figyelését. [További információ a riasztási szabályok hibaelhárításáról](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Az ajánlott eljárások követése Azure Policy használatával

Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatokat hozhat létre, rendelhet hozzá és kezelhet. Ezek a szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon. Az alábbi Azure Policy-javaslatok segítenek a működési Uram elérésében: 
1. Címkék kezelése Azure Policy használatával: Ez a házirend minden erőforrás létrehozásakor vagy frissítésekor hozzáadja vagy lecseréli a megadott címkét és értéket. A meglévő erőforrások egy javítási feladat indításával javíthatók. Emellett ez nem módosítja az erőforráscsoportok címkéit.
2. Geo-megfelelőségi követelmények betartatása Azure Policy használatával: a szabályzat lehetővé teszi, hogy korlátozza a szervezet által megadható helyet az erőforrások telepítésekor. 
3. Az engedélyezett virtuálisgép-SKU-ket adja meg a központi telepítésekhez: Ez a szabályzat lehetővé teszi, hogy a szervezet által üzembe helyezhető virtuálisgép-SKU-készletet határozzon meg.
4. Felügyelt lemezeket nem használó virtuális gépek naplózása az Azure Policy használatával
5. A "címke öröklése az erőforrás-csoportokból" az Azure Policy használatával: a házirend hozzáadja vagy lecseréli a megadott címkét és értéket a szülő erőforráscsoporthoz bármely erőforrás létrehozásakor vagy frissítésekor. A meglévő erőforrások egy javítási feladat indításával javíthatók.

## <a name="next-steps"></a>Következő lépések

Az Advisor ajánlásaival kapcsolatos további tudnivalókért tekintse meg a következő témakört:
* [Az Advisor bemutatása](advisor-overview.md)
* [Első lépések](advisor-get-started.md)
* [Advisor – Cost-javaslatok](advisor-cost-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor – magas rendelkezésre állási javaslatok](advisor-high-availability-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)

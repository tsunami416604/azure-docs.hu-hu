---
title: Az operatív Uram fejlesztése az Advisor szolgáltatással
description: Az Azure-előfizetések működési kiválóságának optimalizálása Azure Advisor használatával.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 2b4c4726400134e4eec3868e155da47cb8c515b5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87057643"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Az operatív kiválóság elérése Azure Advisor használatával

A Azure Advisor működési kiválósági javaslatai a következőket segítik: 
- Folyamatok és munkafolyamatok hatékonysága.
- Erőforrás-kezelhetőség.
- Ajánlott eljárások az üzembe helyezéshez. 

Ezeket a javaslatokat az Advisor irányítópultjának **működési kiválóság** lapján érheti el.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Azure Service Health riasztások létrehozása, amelyekről értesítést kap, ha az Azure-problémák érintik Önt

Javasoljuk, hogy állítson be Azure Service Health riasztásokat, hogy értesítést kapjon, ha az Azure-szolgáltatással kapcsolatos problémák érintik Önt. A [Azure Service Health](https://azure.microsoft.com/features/service-health/) egy ingyenes szolgáltatás, amely személyre szabott útmutatást és támogatást nyújt, ha az Azure-szolgáltatással kapcsolatos problémát érint. Az Advisor olyan előfizetéseket azonosít, amelyek nem rendelkeznek konfigurált riasztásokkal, és azt javasolja, hogy konfigurálja azokat.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Tervezze meg a Storage-fiókokat, hogy megakadályozza a maximális előfizetési korlát elérését

Egy Azure-régió legfeljebb 250 Storage-fiókot tud támogatni előfizetésre. A korlát elérése után nem hozhat létre Storage-fiókokat az adott régióban/előfizetési kombinációban. Az Advisor ellenőrzi az előfizetéseket, és javaslatokat tesz arra, hogy kevesebb Storage-fiókot tervezzen meg a korlát eléréséhez közel álló bármely régióhoz vagy előfizetéshez.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Ellenőrizze, hogy van-e hozzáférése az Azure Cloud experthez, amikor szüksége van rá

Üzleti szempontból kritikus fontosságú számítási feladatok futtatásakor fontos a technikai támogatáshoz való hozzáférés, amikor szüksége van rá. Az Advisor olyan potenciális üzleti szempontból kritikus előfizetéseket azonosít, amelyek nem rendelkeznek technikai támogatással a támogatási csomagban. Javasoljuk, hogy frissítsen egy olyan lehetőségre, amely technikai támogatást is tartalmaz.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>A készlet törlése és újbóli létrehozása egy elavult belső összetevő eltávolításához

Ha a készlet elavult belső összetevőt használ, törölje, majd hozza létre újra a készletet a jobb stabilitás és teljesítmény érdekében.

## <a name="repair-invalid-log-alert-rules"></a>Érvénytelen naplózási riasztási szabályok javítása

Azure Advisor észleli azokat a riasztási szabályokat, amelyekben a feltételek szakaszban megadott lekérdezések érvénytelenek. A naplózási riasztási szabályokat a Azure Monitorban hozhatja létre, és a segítségével meghatározott időközönként futtathatja az elemzési lekérdezéseket. A lekérdezés eredményei határozzák meg, hogy egy riasztást aktiválni kell-e. Az elemzési lekérdezések a hivatkozott erőforrások, táblák vagy parancsok változásai miatt változhatnak az idő múlásával. Az Advisor azt javasolja, hogy javítsa ki a lekérdezést a riasztási szabályban, hogy megakadályozza, hogy a rendszer automatikusan letiltsa, és biztosítsa az Azure-beli erőforrások lefedettségét. [További információ a riasztási szabályok hibaelhárításáról.](https://aka.ms/aa_logalerts_queryrepair)

## <a name="use-azure-policy-recommendations"></a>Azure Policy javaslatok használata

Azure Policy egy Azure-szolgáltatás, amely szabályzatok létrehozására, hozzárendelésére és kezelésére használható. Ezek a szabályzatok az erőforrásokra vonatkozó szabályokat és hatásokat kényszerítik ki. A következő Azure Policy javaslatok segíthetnek a működési Uram elérésében: 

**Címkék kezelése.** Ez a szabályzat az erőforrások minden létrehozásakor vagy frissítésekor hozzáadja vagy lecseréli a megadott címkét és értéket. A meglévő erőforrásokat szervizelési feladat aktiválásával javíthatja. Ez a szabályzat nem módosítja az erőforráscsoportok címkéit.

**A Geo-megfelelőségi követelmények betartatása.** Ezzel a szabályzattal korlátozható azon helyek köre, amelyeket a szervezet megadhat az erőforrások üzembe helyezésekor. 

**Az engedélyezett virtuálisgép-SKU-ket adhatja meg a központi telepítésekhez.** Ezzel a szabályzattal megadhatók a virtuális gépek azon termékváltozatai, amelyeket a szervezet üzembe helyezhet.

***Felügyelt lemezeket nem használó virtuális gépek naplózása*.**

**Engedélyezi *a címke öröklését az erőforráscsoportok közül*.** Ez a szabályzat az erőforrások minden létrehozásakor vagy frissítésekor hozzáadja vagy lecseréli a megadott címkét és értéket a fölérendelt erőforráscsoportból. A meglévő erőforrásokat szervizelési feladat aktiválásával javíthatja.

## <a name="next-steps"></a>További lépések

Az Advisor ajánlásaival kapcsolatos további tudnivalókért tekintse meg a következő témakört:
* [Az Advisor bemutatása](advisor-overview.md)
* [Első lépések](advisor-get-started.md)
* [Az Advisor költségekkel kapcsolatos javaslatai](advisor-cost-recommendations.md)
* [Az Advisor teljesítményével kapcsolatos javaslatok](advisor-performance-recommendations.md)
* [Advisor megbízhatósági javaslatok](advisor-high-availability-recommendations.md)
* [Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor REST API](/rest/api/advisor/)

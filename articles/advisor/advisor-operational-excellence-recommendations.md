---
title: Az Azure Advisorral javíthatja azure-előfizetései működési kiválóságát
description: Az Advisor használatával optimalizálhatja és éretté tesz azure-előfizetései működési kiválóságát
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443079"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Az Azure Advisor rali-kiválóság a működési kiválóságban

Az Azure Advisor működési kiválósági javaslatai segítenek az ügyfeleknek a folyamat- és munkafolyamat-hatékonysággal, az erőforrások kezelésével és a telepítési gyakorlati tanácsokban. Ezeket az ajánlásokat az Advisor **irányítópultjának Működési kiválóság** lapján kaphatja meg.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure Service Health-riasztások létrehozása, amelyek értesítést kapnak, ha az Azure-problémák érintik Önt

Azt javasoljuk, hogy az Azure Service Health-riasztások beállítása értesítést kapjon, ha az Azure-szolgáltatási problémák érintik Önt. [Az Azure Service Health](https://azure.microsoft.com/features/service-health/) egy ingyenes szolgáltatás, amely személyre szabott útmutatást és támogatást nyújt, ha egy Azure-szolgáltatás problémája érinti. Az Advisor azonosítja azokat az előfizetéseket, amelyeknem rendelkeznek konfigurált riasztásokkal, és javasolja, hogy hozzon létre egyet.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Tervezze meg tárfiókjait, hogy ne üsse meg a maximális előfizetési korlátot

Egy Azure-régió előfizetésenként legfeljebb 250 tárfiókot támogathat. A korlát elérése után nem hozhat létre további tárfiókokat az adott régióban/előfizetési kombinációban. Az Advisor ellenőrzi az előfizetéseket és a felületi javaslatokat, hogy kevesebb tárfiókot tervezhessen olyan fiókokhoz, amelyek közel vannak a maximális korlát eléréséhez.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Annak biztosítása, hogy szükség esetén hozzáférhessen az Azure felhőszakértőihez

Üzleti legkritikusabb számítási feladatok futtatásakor fontos, hogy szükség esetén hozzáférjen a technikai támogatáshoz. Az Advisor azonosítja azokat a potenciális, üzleti legkritikusabb előfizetéseket, amelyek nem tartalmaznak technikai támogatást a támogatási csomagjukban, és javasolja a technikai támogatást is tartalmazó beállításra való frissítést.

## <a name="repair-invalid-log-alert-rules"></a>Érvénytelen naplóriasztási szabályok javítása

Az Azure Advisor észleli azokat a riasztási szabályokat, amelyek érvénytelen lekérdezéseket a feltétel szakaszban megadott. A naplóriasztási szabályok az Azure Monitorban hozhatók létre, és elemzési lekérdezések adott időközönkénti futtatására szolgálnak. A lekérdezés eredményei határozzák meg, hogy egy riasztást aktiválni kell-e. Az elemzési lekérdezések idővel érvénytelenné válhatnak a hivatkozott erőforrásokban, táblákban vagy parancsokban bekövetkező változások miatt. Az Advisor azt javasolja, hogy javítsa ki a lekérdezést a riasztási szabályban, hogy megakadályozza az automatikus letiltását, és biztosítsa az erőforrások figyelésének az Azure-ban való lefedettségét. [További információ a riasztási szabályok hibaelhárításáról](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Kövesse az ajánlott eljárásokat az Azure Policy használatával

Az Azure Policy az Azure egy szolgáltatása, amelynek használatával szabályzatokat hozhat létre, rendelhet hozzá és kezelhet. Ezek a szabályzatok különböző szabályokat és hatásokat kényszerítenek ki az erőforrásokon. Az alábbiakban az Azure-szabályzat javaslatait olvashatja, amelyek segítenek a működési kiválóság elérésében: 
1. Címkék kezelése az Azure-szabályzat használatával: Ez a szabályzat hozzáadja vagy lecseréli a megadott címkét és értéket, amikor bármely erőforrást hoz létre vagy frissít. A meglévő erőforrások egy javítási feladat indításával javíthatók. Ez nem módosítja az erőforráscsoportok címkéit sem.
2. Földrajzi megfelelőségi követelmények kényszerítése az Azure-szabályzat használatával: A szabályzat lehetővé teszi, hogy korlátozza azokat a helyeket, amelyeket a szervezet az erőforrások üzembe helyezésekor adhat meg. 
3. Adja meg az engedélyezett virtuálisgép-skus-ok a központi telepítések: Ez a házirend lehetővé teszi, hogy adja meg a virtuális gép skus, amely a szervezet telepítheti.
4. Az Azure-szabályzat használatával "Felügyelt lemezeket nem használó virtuális gépek naplózása" kényszerítése
5. Használja a "Címke öröklése erőforráscsoportokból" az Azure-szabályzat használatával: A szabályzat hozzáadja vagy lecseréli a megadott címkét és értéket a szülő erőforráscsoportból, amikor bármely erőforrást létrehoz vagy frissít. A meglévő erőforrások egy javítási feladat indításával javíthatók.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Advisor-ajánlásokról, olvassa el a következő témakört:
* [Bevezetés a tanácsadó](advisor-overview.md)
* [Induláshoz](advisor-get-started.md)
* [Tanácsadói költségajánlások](advisor-cost-recommendations.md)
* [Tanácsadó teljesítményre vonatkozó ajánlásai](advisor-performance-recommendations.md)
* [Tanácsadó magas rendelkezésre állásra vonatkozó ajánlásai](advisor-high-availability-recommendations.md)
* [Tanácsadó biztonsági ajánlásai](advisor-security-recommendations.md)

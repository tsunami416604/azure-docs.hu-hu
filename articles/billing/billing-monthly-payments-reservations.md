---
title: Azure-foglalások vásárlása előzetes vagy havi fizetéssel
description: Ismerje meg, hogyan vásárolhat Azure-foglalásokat előzetes vagy havi fizetéssel.
services: billing
author: bandersmsft
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: banders
ms.openlocfilehash: d211334ad2aa760cd63b98c6827fb2512811a1d3
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806947"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Vásárlási foglalások havi fizetéssel

Eddig az Azure-foglalások kötelező befizetést igényelnek. Mostantól a havi kifizetéssel rendelkező foglalásokat is kifizetheti. Az előzetes vásárlástól eltérően, ahol a teljes összeget kifizeti, a havi fizetési lehetőség a foglalás teljes költségét egyenlően osztja el a kifejezés minden hónapjában. A kezdeti és a havi foglalások teljes költsége megegyezik, és a havi fizetés után nem számítunk fel további díjakat.

A havi fizetési összeg az aktuális havi piaci árfolyamtól függően változhat a helyi pénznemben.

A következő havi fizetések érhetők el:

- Virtual machines (Virtuális gépek)
- SQL Database
- SQL Data Warehouse
- Cosmos DB
- App Service Stamp díja

Foglalások vásárlása a [Azure Portal](https://portal.azure.com/?Microsoft_Azure_Reservations_EnableMultiCart=true&amp;paymentPlan=true#blade/Microsoft_Azure_Reservations/CreateBlade).

![A foglalások vásárlását bemutató példa](./media/billing-monthly-payments-reservations/purchase-reservation.png)

A foglalás megvásárlásakor megtekintheti a fizetési ütemtervet. Kattintson a **teljes fizetési ütemterv megtekintése**elemre.

![A foglalási fizetési ütemtervet bemutató példa](./media/billing-monthly-payments-reservations/prepurchase-schedule.png)

Ha a vásárlás után szeretné megtekinteni a fizetési ütemtervet, válasszon ki egy foglalást, kattintson a **foglalási rendelés azonosítóra**, majd kattintson a **fizetések** fülre.

## <a name="view-payments-made"></a>Kifizetések megtekintése

Megtekintheti az API-k, a használati adatok és a Cost Analysis szolgáltatásban végrehajtott kifizetéseket. A havonta fizetett foglalások esetében a gyakoriság értéke **ismétlődőként** jelenik meg a használati adatok és a foglalási díjak API-ban. Az előre befizetett foglalások esetében az érték az **egyszeri**módon jelenik meg.

A Cost Analysis az alapértelmezett nézetben mutatja be a havi vásárlásokat. Az összes vásárlás megjelenítéséhez alkalmazza a **beszerzési** szűrőt a **Charge Type** és az **ismétlődő** **értékekre** . Csak a foglalások megtekintéséhez alkalmazzon szűrőt a **foglaláshoz**.

![Példa a foglalások vásárlásának költségeire a Cost Analysis szolgáltatásban](./media/billing-monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Váltás a havi fizetésekre a Megújításkor

A foglalás megújításakor a számlázási gyakoriságot havi értékre állíthatja.

## <a name="exchange-and-refunds"></a>Exchange-és visszatérítések

A többi foglaláshoz hasonlóan a havi számlázással megvásárolt visszatérítési vagy Exchange-foglalások is megvásárolhatók. A havi számlázással megvásárolt foglalások esetében jelenleg egy támogatási kérelmet is benyújthat, amely lehetővé teszi az Exchange vagy a visszatérítés elindítását.

Ha havonta fizetett foglalást cserél ki, akkor az új vásárlás teljes élettartama nagyobbnak kell lennie, mint a visszaadott foglalásnál megszakított fizetési díj. Nincs más korlátozás vagy díj a cseréhez. Az előfizetéshez fizetős foglalásokat a havonta számlázott új foglalás megvásárlása céljából lehet cserélni. Az új foglalás élettartamának azonban nagyobbnak kell lennie, mint a visszaadott foglalás arányos értéke.

Ha a havonta fizetett foglalást szakítja meg, a Microsoft lemondási díjat alkalmazhat a megszakított jövőbeli befizetésekre. A fennmaradó véglegesített kifizetések a $50 000 USD-os visszatérítési korláttal szemben esedékesek.

További információ az Exchange-ről és a visszatérítésekről: [önkiszolgáló cserék és visszatérítések Azure Reservations](billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="faq"></a>GYIK

K. Az Azure felajánlja a "részleges kezdeti foglalásokat?"<br>
A. Nem. Mivel az előzetes és a havi foglalás költségei megegyeznek, a Microsoft nem támogatja a részleges kezdeti befizetéseket.

K. Elérhetőek-e a Microsoft Cloud Solution Provider (CSP) program havi kifizetései?<br>
A. Igen, a partnerek a Azure Portal vásárolhatják meg a CSP-ügyfelek foglalását. A foglalások havi számlázással történő megvásárlásának lehetősége nem érhető el a partner Centerben.

K. Azure Government ügyfél vagyok, fizethetek havonta a foglalások vásárlására?<br>
A. Jelenleg nem.

K. Mikor lehet a támogatási jegy létrehozása helyett saját magamat cserélni vagy visszatéríteni a Azure Portal?<br>
A. Jelenleg nem. Az Exchange-re és a visszatérítésre vonatkozó kérelmeket az Azure-támogatás kezeli.

## <a name="next-steps"></a>További lépések

- További információ a foglalásokról: [Mi a Azure Reservations?](billing-save-compute-costs-reservations.md)
- Ha szeretne többet megtudni a foglalás megvásárlása előtt elvégzendő feladatokról, olvassa el [a virtuális gép megfelelő méretének meghatározása a vásárlás előtt](../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy) című témakört.

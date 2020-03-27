---
title: Azure-foglalások vásárlása előre fizetéssel vagy havi kifizetésekkel
description: Ismerje meg, hogyan vásárolhat Azure-foglalásokat előre fizetéssel vagy havi kifizetésekkel.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: banders
ms.openlocfilehash: ede60adc13dadc38e18ee5ade468e01b16523f4f
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2020
ms.locfileid: "80235742"
---
# <a name="purchase-reservations-with-monthly-payments"></a>Foglalások vásárlása havi kifizetésekkel

Havi kifizetésekkel is fizethet a foglalásokért. Az előre fizetéses vásárlástól eltérően, amikor kifizeti a teljes összeget, a havi fizetéses lehetőség egyenlően osztja el a foglalás teljes költségét az időszak minden hónapjára. A foglalások előre vagy havonta fizetett összege megegyezik, és ha a havi fizetést választja, nem kell extra díjakat fizetnie.

Ha foglalását Microsoft Ügyfélszerződés (Microsoft Customer Agreement, MCA) keretén belül vásárolta, a havonta fizetendő összeg a helyi pénznem aktuális hónapban érvényes átváltási árfolyamától függően változhat.

A havi kifizetések az alábbiakhoz nem érhetőek el: Databricks, SUSE Linux-foglalások, Red Hat-csomagok és Azure Red Hat OpenShift-számítások.

Foglalások vásárlása az [Azure Portalon](https://ms.portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Docs).

![Példa a foglalásvásárlásra](./media/monthly-payments-reservations/purchase-reservation.png)

A foglalás megvásárlásakor megtekintheti a fizetési ütemezést. Kattintson a **Teljes kifizetési ütemezés megtekintése** lehetőségre.

![Példa a foglalás fizetési ütemezésére](./media/monthly-payments-reservations/prepurchase-schedule.png)

A fizetési ütemezés vásárlás utáni megtekintéséhez válasszon ki egy foglalást, kattintson a **Foglalás rendelési azonosítója** lehetőségre, majd a **Kifizetések** lapra.

## <a name="view-payments-made"></a>A megtörtént kifizetések megtekintése

A megtörtént kifizetéseket az API-k és a használati adatok használatával, valamint a költségelemzésben tekintheti meg. A havonta kifizetett foglalások esetében a gyakoriság értéke **ismétlődőként** jelenik meg a használati adatokban és a Reservation Charges API-ban. Az előre kifizetett foglalások esetében az érték **egyszeriként** jelenik meg.

A költségelemzés az alapértelmezett nézetben jeleníti meg a havi vásárlásokat. A vásárlások megtekintéséhez alkalmazza a **vásárlás** szűrőt a **Költségtípus** elemre és az **ismétlődő** szűrőt a **Gyakoriság** elemre. Ha csak a foglalásokat szeretné megtekinteni, a **Foglalások** esetében alkalmazzon szűrőt.

![Példa a foglalásvásárlási költségekre a költségelemzésben](./media/monthly-payments-reservations/cost-analysis.png)

## <a name="switch-to-monthly-payments-at-renewal"></a>Váltás havi kifizetésekre megújításkor

A foglalás megújításakor lehetősége van a számlázás gyakoriságát havira módosítani.

## <a name="exchange-and-refunds"></a>Csere és visszatérítés

A többi foglaláshoz hasonlóan a havi számlázással vásárolt foglalások esetében is lehetőség van visszatérítésre és cserére. 

Ha egy havi kifizetéses foglalást cserél le, akkor az új vásárlás teljes élettartamra vetített költségének nagyobbnak kell lennie, mint a lecserélni kívánt foglalás lemondott, fennmaradó kifizetései. Csere esetén nincsenek más korlátozások vagy díjak. Cserére akkor van lehetősége, ha egy előre kifizetett foglalás helyett vásárol egy havi számlázású, új foglalást. Az új foglalás élettartamra vetített értékének azonban nagyobbnak kell lennie, mint a lecserélni kívánt foglalás időarányos értéke.

Ha lemond egy havonta fizetett foglalást, a lemondott későbbi kifizetések le lesznek vonva az 50 000 USD értékű visszatérítési korlátból.

A cserével és a visszatérítéssel kapcsolatos további információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](exchange-and-refund-azure-reservations.md).

## <a name="next-steps"></a>További lépések

- A foglalásokkal kapcsolatos további információkért tekintse meg a [Mi az az Azure Reservations?](save-compute-costs-reservations.md) című cikket.
- A foglalásvásárlás előtt elvégzendő feladatok megismeréséhez tekintse meg [a megfelelő virtuálisgép-méret vásárlási előtti megállapítását](../../virtual-machines/windows/prepay-reserved-vm-instances.md#determine-the-right-vm-size-before-you-buy) ismertető cikket

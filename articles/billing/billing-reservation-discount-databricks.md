---
title: Hogyan kell alkalmazni az Azure Databricks előre történő megvásárlása kedvezmény
description: Ismerje meg, hogyan a használat egy Azure Databricks előre történő megvásárlása kedvezmény érvényes.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 7c1855b587ab1d603e9c6ac24a67b0f50065361f
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827641"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Az Azure Databricks hogyan vásárolhat kedvezmény alkalmazása

A vásárlás során bármikor előre megvásárolt Azure Databricks végrehajtási egységek (DBCU) is használhatja. Bármely Azure Databricks használata az előre megvásárolt DBCUs a levonja az automatikusan.

Virtuális gépek, eltérően előre megvásárolt egységek óránként nem lejár. A vásárlás során bármikor használhatja őket. Az előre történő megvásárlása engedmények lekéréséhez nem kell újból üzembe helyeznie vagy egy előre megvásárolt csomag hozzárendelése az Azure Databricks-munkaterületek a használatra.

Az előre történő megvásárlása engedményt csak az Azure Databricks-egységek (DBU) használati vonatkozik. Egyéb, például számítási, tárolási és hálózati díjak külön-külön számítjuk fel.

## <a name="pre-purchase-discount-application"></a>Előre történő megvásárlása vonatkozó kedvezmény alkalmazása

Előre történő megvásárlása Databricks a Databricks számítási feladatok és a szint vonatkozik. Előre fizetett Databricks végrehajtási egységek készletét, mint a előre történő megvásárlása is felfoghatók. Használati függetlenül a számítási feladat vagy a csomag, a készletbe vonni. Használati levonásra kerül a következő arány:

| **Számítási feladat** | **DBU-alkalmazás arány – Standard csomag** | **DBU-alkalmazás arány – prémium szint** |
| --- | --- | --- |
| Adatelemzés | 0.4 | 0.55 |
| Adatfeldolgozás | 0.15 | 0,30 |
| Adatfeldolgozás (egyszerű) | 0.07 | 0.22 |

Ha például Data Analytics – Standard szintű mennyiséget használja fel, ha a Databricks-véglegesítési előre megvásárolt egységek levonása 0,4 egységek által. Adatok mérnöki világos – mennyiségű Standard csomagot használja, a Databricks-véglegesítési előre megvásárolt egység levonása 0.07 egységek

## <a name="determine-plan-use"></a>Határozza meg a terv használata

Annak megállapításához, a DBCU tervet használja, nyissa meg az Azure portal > **foglalások** , és kattintson a megvásárolt Databricks tervet. A kihasználtság elejétől számított összes többi egység mellett látható. További információt, amely meghatározza, hogy a Foglalás használja, tekintse meg a [foglalás használati](billing-reservation-apis.md#see-reservation-usage) cikk.

## <a name="how-discount-application-shows-in-usage-data"></a>Hogyan vonatkozó kedvezmény alkalmazása jeleníti meg, a használati adatok

Az előre történő megvásárlása engedmény a Databricks-használat vonatkozik, ha igény szerinti költségek a használati adatok nullaként jelennek meg. A fenntartási költségeket, és használatával kapcsolatos további információkért lásd: [első nagyvállalati szerződés fenntartási költségeket és a használatot](billing-understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan kezelheti a foglalást, lásd: [kezelése az Azure-foglalások](billing-manage-reserved-vm-instance.md).
- Pénzt takaríthat meg az Azure Databricks előzetes megvásárlásával kapcsolatos további tudnivalókért lásd: [előre történő megvásárlása költségek optimalizálása az Azure Databricks](billing-prepay-databricks-reserved-capacity.md).
- Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
  - [Az Azure-ban foglalások kezelése](billing-manage-reserved-vm-instance.md)
  - [Foglalás utólagos elszámolású díjszabás szerint az előfizetés használati adatai](billing-understand-reserved-instance-usage.md)
  - [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)

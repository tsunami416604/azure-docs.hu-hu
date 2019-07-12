---
title: Optimalizálhatók a költségek az Azure Databricks egy előre történő megvásárlása
description: Ismerje meg, hogyan, is fizessen elő az Azure Databricks-díjak a lefoglalt kapacitás pénzt takaríthat meg.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: banders
ms.openlocfilehash: 99eb4de86aa227d558bec54d011a0b1548d27cf0
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811257"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Optimalizálhatók a költségek az Azure Databricks egy előre történő megvásárlása

Az Azure Databricks is mentheti egységek (DBU) költségeit, ha előre megvásárolja az Azure Databricks véglegesítése egységek (DBCU) egy vagy három évig. Használhatja az előre megvásárolt DBCUs bármikor a vásárlás során. Ellentétben a virtuális gépek az előre megvásárolt egységek óránként nem jár le, és azokat a vásárlás során bármikor használhatja.

Az Azure Databricks használata az előre kell megvásárolni a Dbu levonja az automatikusan. Nem kell újból üzembe helyeznie vagy egy előre megvásárolt csomag hozzárendelése az Azure Databricks-munkaterületek, az előre történő megvásárlása kedvezmények DBU használatra.

Az előre történő megvásárlása engedményt csak a DBU használati vonatkozik. Egyéb, például számítási, tárolási és hálózati díjak külön-külön számítjuk fel.

## <a name="determine-the-right-size-to-buy"></a>Határozza meg a megfelelő méretű megvásárlása

Előre történő megvásárlása Databricks a Databricks számítási feladatok és a szint vonatkozik. Előre fizetett Databricks végrehajtási egységek készletét, mint a előre történő megvásárlása is felfoghatók. Használati függetlenül a számítási feladat vagy a csomag, a készletbe vonni. Használati levonásra kerül a következő arány:

| **Számítási feladat** | **DBU alkalmazás arány – Standard csomag** | **DBU-alkalmazás arány – prémium szint** |
| --- | --- | --- |
| Adatelemzés | 0.4 | 0.55 |
| Adatfeldolgozás | 0.15 | 0,30 |
| Adatfeldolgozás (egyszerű) | 0.07 | 0.22 |

Például Data Analytics – mennyiségű Standard szintű használja fel, a Databricks-véglegesítési előre megvásárolt egységek levonása 0,4 egységek.

Vásárlás, előtt kiszámítása az összes DBU-mennyiség, a különböző számítási feladatok és csomagok felhasznált. Az előző arányok használatával való DBCU normalizálása, és futtassa a teljes felhasználás vetülete ezután egy vagy három év során.

## <a name="purchase-databricks-commit-units"></a>Databricks-végrehajtási egység megvásárlása

Databricks-csomagokat is elérhetőek a [az Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Lefoglalt kapacitás vásárlásához ki legalább egy vállalati előfizetés a tulajdonosi szerepkörrel kell rendelkeznie.

- Jelenleg előre történő kifizetését csak nagyvállalati szerződéssel rendelkező ügyfelek számára érhető el.
- A vállalati legalább egy előfizetés-tulajdonosi szerepkör kell lennie.
- Vállalati előfizetés esetén **fenntartott példányok hozzáadása** engedélyezve kell lennie a [a nagyvállalati szerződések portáljának](https://ea.azure.com/). Vagy, ha a beállítás le van tiltva, az előfizetés egy nagyvállalati szerződés rendszergazdájának kell lennie.

**Beszerzés:**

1. Nyissa meg az [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Válasszon egy előfizetést. Használja a **előfizetés** listát használva jelölje ki az előfizetést, amellyel a lefoglalt kapacitás kell fizetnie. A fizetési módot, az előfizetés az előzetes költségek, a szolgáltatás számára fenntartott kapacitás után kell fizetni. Díjak a regisztráció pénzügyi kötelezettségvállalási egyenleg kell vonni vagy számlázva.
1. Válassza ki a hatókört. Használja a **hatókör** lista segítségével válassza ki egy előfizetési hatókört:
    - **Erőforrás-csoport hatóköre egyetlen** – vonatkozik a foglalási kedvezményt a megfelelő erőforrások csak a kiválasztott erőforráscsoportban.
    - **Egyetlen előfizetéses hatókört** – alkalmazza a foglalási kedvezményt a megfelelő erőforrások a kijelölt előfizetésben.
    - **A megosztott hatókör** – alkalmazza a foglalási kedvezményt támogatásra jogosult előfizetések, a számlázási környezetben lévő erőforrások megfelelő. Nagyvállalati szerződéssel rendelkező ügyfeleknek a számlázási környezetben a regisztrációt.
1. Válassza ki az Azure Databricks véglegesítési hány egység megvásárlása és véglegesítse a vásárlást.


![Az Azure Portalon az Azure Databricks-beszerzési bemutató példa](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Hatókör módosítása és tulajdonosi viszonyainak

Egy foglalás megvásárlása után a következő típusú módosításokat végezhet:

- Frissítse a Foglalás hatóköre
- Szerepköralapú hozzáférés

Nem felosztása és egyesítése a Databricks véglegesítési egység előre történő megvásárlása. Foglalások kezelésével kapcsolatos további információkért lásd: [foglalások kezelése a vásárlás után](billing-manage-reserved-vm-instance.md).

## <a name="cancellations-and-exchanges"></a>Lemondás és cseréje

Megszakítás és az exchange Databricks előre történő megvásárlása tervek esetében nem támogatott. Minden vásárláshoz végső.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mik az Azure-foglalásokat?](billing-save-compute-costs-reservations.md)
  - [Megismerheti, hogyan kell alkalmazni az Azure Databricks előre történő megvásárlása DBCU kedvezmény](billing-reservation-discount-databricks.md)
  - [A nagyvállalati beléptetés foglalás használati adatai](billing-understand-reserved-instance-usage-ea.md)

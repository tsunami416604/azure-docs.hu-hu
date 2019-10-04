---
title: Azure Databricks-költségek optimalizálása előzetes vásárlással
description: Megtudhatja, hogyan fizetheti előre az Azure Databricks költségeit fenntartott kapacitással, így pénzt takarítva meg.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 5ceef76bd5a5f6a2904d3619f310020e90cbec0e
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719692"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>Azure Databricks-költségek optimalizálása előzetes vásárlással

Pénzt takaríthat meg az Azure Databricks-egységeinek (DBU) költségein, ha kötött Azure Databricks-egységeket (DBCU) vásárol előzetesen egy vagy három évre. Az előre megvásárolt DBCU-kat a vásárlási időtartam során bármikor felhasználhatja. A virtuális gépektől eltérően az előre megvásárolt egységek nem járnak le óránként, és a vásárlási időtartam alatt bármikor felhasználhatók.

Az előre megvásárolt DBU-kból való levonás automatikusan megtörténik az Azure Databricks használata esetén. Az előzetes vásárlásért járó kedvezmények igénybevételéhez nem szükséges újból üzembe helyeznie vagy hozzárendelnie egy előzetesen vásárolt csomagot az Azure Databricks-munkaterületeihez a DBU-használathoz.

Az előzetes vásárlásért járó kedvezmény csak a DBU-használatra vonatkozik. Az egyéb – például számítási, tárolási és hálózati – díjakat külön számítjuk fel.

## <a name="determine-the-right-size-to-buy"></a>A vásárolni kívánt megfelelő méret meghatározása

Az előzetesen vásárolt Databricks-egységek minden Databricks-számításifeladathoz és -szinthez felhasználhatók. Az előzetes vásárlást előre kifizetett kötött Databricks-egységek készletének lehet tekinteni. A rendszer a készletből vonja le a használatot, a számítási feladattól és a szinttől függetlenül. A használat levonása a következő arányban történik:

| **Számítási feladat** | **DBU-alkalmazási arány – Standard szint** | **DBU-alkalmazási arány – Prémium szint** |
| --- | --- | --- |
| Adatelemzés | 0,4 | 0,55 |
| Adatfeldolgozás | 0,15 | 0,30 |
| Adatfeldolgozás (egyszerű) | 0,07 | 0,22 |

Ha például bizonyos mennyiségű Standard szintű Adatelemzést használ fel, az előzetesen vásárolt kötött Databricks-egységekből 0,4 egység lesz levonva.

A vásárlás előtt számítsa ki a különböző számítási feladatokhoz és szintekhez felhasznált teljes DBU-mennyiséget. Használja ezeket az arányokat a DBCU-ra való normalizáláshoz, majd futtassa le a teljes használat következő egy vagy három évre szóló előrejelzését.

## <a name="purchase-databricks-commit-units"></a>Kötött Databricks-egységek vásárlása

Databricks-csomagokat az [Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D) vásárolhat. Lefoglalt kapacitás vásárlásához legalább egy nagyvállalati előfizetés tulajdonosi szerepkörével kell rendelkeznie.

- Az előzetes vásárlás jelenleg csak a Nagyvállalati Szerződéssel rendelkező ügyfelek számára érhető el.
- Legalább egy nagyvállalati előfizetés tulajdonosi szerepkörével kell rendelkeznie.
- Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor Önnek az előfizetés EA-rendszergazdájának kell lennie.

**A vásárláshoz:**

1. Nyissa meg az [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Válasszon egy előfizetést. Az **Előfizetés** lista használatával válassza ki a lefoglalt kapacitás kifizetéséhez használt előfizetést. Az előfizetés fizetési módjának használatával megtörténik a lefoglalt kapacitás után fizetett előzetes költségek levonása. A díjak a regisztrációhoz tartozó pénzügyi keretek egyenlegeiből lesznek levonva, illetve kerettúllépésként lesznek számlázva.
1. Válassza ki a hatókört. A **Hatókör** lista használatával válassza ki az előfizetés hatókörét.
    - **Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza.
    - **Egy előfizetésre kiterjedő hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található, egyező erőforrásokra alkalmazza.
    - **Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található, egyező erőforrásokra alkalmazza. A Nagyvállalati Szerződéssel rendelkező ügyfelek esetében a számlázási környezet a regisztráció.
1. Válassza ki a megvásárolni kívánt kötött Azure Databricks-egységek számát, és véglegesítse a vásárlást.


![Azure Databricks-egységek Azure Portalon történő vásárlását bemutató példa](./media/billing-prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>A hatókör és a tulajdonjog módosítása

A vásárlás után a következő típusú módosításokat hajthatja végre a foglalásokon:

- Foglalás hatókörének frissítése
- Szerepköralapú hozzáférés

Előzetes vásárlás esetén nem oszthat fel vagy egyesíthet kötött Databricks-egységeket. A foglalások kezelésével kapcsolatos további információkért olvassa el [a foglalások vásárlás utáni kezelését](billing-manage-reserved-vm-instance.md) ismertető cikket.

## <a name="cancellations-and-exchanges"></a>Lemondások és cserék

A lemondás és a csere az előzetesen vásárolt Databricks-csomagok esetében nem támogatott. Minden vásárlás végleges.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mi az az Azure Reservations?](billing-save-compute-costs-reservations.md)
  - [Az előzetes Azure Databricks-vásárlásért járó DBCU-kedvezmény alkalmazásának ismertetése](billing-reservation-discount-databricks.md)
  - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](billing-understand-reserved-instance-usage-ea.md)

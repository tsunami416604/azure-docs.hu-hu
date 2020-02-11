---
title: Gyakran használt költségelemzési megoldások az Azure Cost Managementben
description: Ez a cikk bemutatja, hogyan kérhetők le az eredmények a gyakori költségelemzési feladatok esetében az Azure Cost Managementben.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: adwise
ms.custom: ''
ms.openlocfilehash: 756e2f275a92a31a99604aaf7ef880bfdce8149b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "75988512"
---
# <a name="common-cost-analysis-uses"></a>Gyakran használt költségelemzési megoldások

Az Azure Cost Management felhasználói gyakran keresnek válaszokat olyan kérdésekre, amelyeket sok más felhasználó is feltesz. Ez a cikk bemutatja, hogyan kérhetők le az eredmények a gyakori költségelemzési feladatok esetében a Cost Managementben.

## <a name="view-cost-breakdown-by-azure-service"></a>Költséglebontás megtekintése Azure-szolgáltatások szerint

A költségek Azure-szolgáltatások szerinti megjelenítése révén jobban átláthatja infrastruktúrájának azon részeit, amelyek a legmagasabb költségekkel járnak. Előfordulhat például, hogy a virtuális gépekhez kapcsolódó számítási költségek alacsonyak. A virtuális gépekből származó információmennyiség miatt azonban jelentős hálózati költségek merülhetnek fel. Az Azure-szolgáltatások elsődleges költségtényezőinek megértése elengedhetetlen ahhoz, hogy igény szerint módosíthassa saját szolgáltatáshasználatát.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza a **Díj szolgáltatás szerint** elemet, majd végezze el a csoportosítást **Szolgáltatásszint** szerint.
1. Módosítsa a nézetet a következőre: **Táblázat**.

![Költségek lebontása Azure-szolgáltatások szerint](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>Költséglebontás megtekintése Azure-erőforrások szerint

A szolgáltatások létrehozása Azure-erőforrásokkal történik. A költségek erőforrás-alapú áttekintése segítséget nyújthat az elsődleges költségelemek gyors azonosításában. Ha egy szolgáltatás túlságosan drága erőforrásokkal rendelkezik, érdemes lehet módosításokat eszközölni a költségek csökkentése érdekében.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza a **Díjak erőforrások szerint** elemet.
1. Módosítsa a nézetet a következőre: **Táblázat**.

![Költséglebontás megtekintése Azure-erőforrások szerint](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Költséglebontás megtekintése a kiválasztott dimenziók szerint

A dimenziók lehetővé teszik, hogy költségeit a díjtételekben szereplő különböző metaadatértékek alapján rendszerezhesse. Csoportosíthatja például a költségeit hely szerint.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza a **Csoportosítási szempont** szűrőt.  
    ![Csoport kiválasztása tétel szerint](./media/cost-analysis-common-uses/group-by.png)
1. Igény szerint mentheti a nézetet későbbi felhasználásra.
1. A részletesebb adatok megtekintéséhez kattintson a grafikon alatti tortadiagramra.  
    ![Költséglebontás megtekintése a kiválasztott dimenziók szerint](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Napi vagy havi költségek megtekintése

A napi és havi költségek megtekintése révén jobban megismerheti, hogy van-e olyan időszak a hét vagy év során, amikor a költségei magasabbak. Ha nagyobb ügyfélforgalma van egy adott munkaszüneti időszakban, Azure-költségei annak megfelelően nőnek? A péntek drágább nap, mint a hétfő?

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. A **Részletesség** értéke legyen **Havi** vagy **Napi**.

![Napi költségek megtekintése](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Adott címkéhez tartozó költségek megtekintése

A díjtételek hatékonyabb kategorizálásához számos Azure-felhasználó alkalmaz címkéket az erőforrásai esetében, ami lehet például költséghely vagy (éles és tesztelési) fejlesztési környezet. A címkék dimenziókként jelennek meg a költségelemzésben. A dimenziók segítségével a felhasználó betekintést nyerhet saját egyéni címkézési kategóriáiba.

A címkék támogatása a címke erőforráson való alkalmazása *utáni* jelentett használatra vonatkozik. A címkék nem jelennek meg visszamenőlegesen a költségösszesítések esetében.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza ki a címkéhez tartozó **Csoportosítás szempont** elemet.

![Adott címkéhez tartozó költségek megtekintése](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>Használati adatok letöltése

A használati adatokat tartalmazó, CSV formátumú jelentésfájl tartalmazza az adott számlán szereplő összes díjtétel lebontását. A jobb megértés érdekében a jelentést összehasonlíthatja a számlájával. A számlán szereplő díjtételek mindegyike egy-egy lebontott díjtételnek felel meg a használati jelentésben.

1. Az Azure Portalon keresse meg a számlázási fiókhoz vagy előfizetéshez tartozó **Használat és díjak** lapot. Például: **Költségkezelés + Számlázás** > **Számlázás** > **Felhasználás + díjak**.
1. Válassza ki a letöltési forrásként használni kívánt elemet, majd kattintson a letöltési szimbólumra.  
    ![Használati adatok és díjak letöltése](./media/cost-analysis-common-uses/download1.png)
1.  Válassza ki a letölteni kívánt használatiadat-fájlt.  
    ![Letölteni kívánt használatiadat-fájl kiválasztása](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Havi EA-költséglebontás megtekintése

EA-regisztrációja a teljes szervezet költségeit magában foglalja. A költségek felhalmozódásának és időbeli számlázásának megértése segít a megfelelő érdekelt felek bevonásában a költségek felelősségteljes kezelésének biztosításához.

1. Az Azure Portalon lépjen a **Költségkezelés + Számlázás** > **Áttekintése** területre.
1. Kattintson az aktuális hónaphoz tartozó **Lebontás** elemre, és tekintse meg a pénzügyi kerete részleteit.  
    ![EA-költségek áttekintése – a költséglebontás összefoglalása](./media/cost-analysis-common-uses/breakdown1.png)
1.  Kattintson a **Használat és díjak** lapra, és tekintse meg az előző hónap költséglebontását a kiválasztott időtartományra vonatkozóan.  
    ![Használat és díjak lap](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>Havi regisztrációs költség megtekintése időszakonként

A regisztráció havi költségeinek grafikus nézete segítségével megismerheti az adott időszakra vonatkozó költségtrendeket és kiszámlázott összegeket.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza ki a regisztrációt, és állítsa be a regisztrációs időszakot.
1. Állítsa be a részletességet havi értékre, majd állítsa be a nézetet a következőre: **Oszlop (halmozott)** .

A részletesebb elemzéshez csoportosíthatja és szűrheti is az adatait.

![Havi regisztrációs költség időszakonként](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Az EA-regisztráció halmozott költségeinek megtekintése

Tekintse meg az idővel felhalmozott nettó díjtételeket, hogy átfogó képet kaphasson a szervezete kiadásairól az adott időszakra vonatkozóan.

1. Az Azure Portalon keresse meg a saját hatóköréhez tartozó költségelemzést. Például: **Költségkezelés + Számlázás** > **Költségkezelés** > **Költségelemzés**.
1. Válassza ki a regisztrációt, majd tekintse meg az aktuális halmozott költségeket.

![Halmozott regisztrációs költségek](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>További lépések
- Ha még nem végezte el a Cost Management (Költségkezelés) első gyorsútmutatóját, itt megtekintheti: [Költségelemzés indítása](quick-acm-cost-analysis.md).
- Olvassa el a [Cost Management dokumentációját](../index.yml).

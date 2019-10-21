---
title: Az általános Cost Analysis a Azure Cost Managementban használatos
description: Ez a cikk azt ismerteti, hogyan kérheti le a Azure Cost Management gyakori Cost Analysis feladatainak eredményeit.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/16/2019
ms.topic: conceptual
ms.service: cost-management
manager: adwise
ms.custom: ''
ms.openlocfilehash: cc133a75506dcbe552d380e2a6de219a47aca3d8
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72600188"
---
# <a name="common-cost-analysis-uses"></a>Közös Cost Analysis-használat

Azure Cost Management a felhasználók gyakran választ adnak olyan kérdésekre, amelyekre sok más kér. Ez a cikk végigvezeti a közös Cost Analysis feladatainak Cost Managementban való beszerzésének eredményein.

## <a name="view-cost-breakdown-by-azure-service"></a>A költségcsökkentés megtekintése az Azure szolgáltatásban

Az Azure-szolgáltatások költségeinek megtekintésével jobban megismerheti az infrastruktúra azon részeit, amelyek a legjobban teszik ki a költségeket. Előfordulhat például, hogy a virtuális gépek számítási költségei kis méretűek. A virtuális gépekről kibocsátott információk mennyisége miatt azonban jelentős hálózati költségek merülhetnek fel. Az Azure-szolgáltatások elsődleges díjszabási illesztőprogramjainak megismerése elengedhetetlen ahhoz, hogy igény szerint lehessen módosítani a szolgáltatások használatát.

1. A Azure Portal navigáljon a hatókör Cost Analysis eleméhez. Például: **Cost Management + számlázási**  > **Cost Management**  > **Cost Analysis**.
1. Válassza **a Cost by Service** lehetőséget, majd csoportosítsa a **szolgáltatási szintet**.
1. Módosítsa a nézetet **táblázatra**.

![Költségbecslés az Azure-szolgáltatással](./media/cost-analysis-common-uses/breakdown-by-service.png)

## <a name="view-cost-breakdown-by-azure-resource"></a>A költségcsökkentés megtekintése az Azure-erőforrás alapján

A szolgáltatások Azure-erőforrásokkal készültek. Az erőforrásokon alapuló költségek áttekintésével gyorsan azonosíthatja az elsődleges költségekkel foglalkozó közreműködőket. Ha egy szolgáltatás túl drága erőforrásokkal rendelkezik, érdemes lehet módosítani a költségek csökkentését.

1. A Azure Portal navigáljon a hatókör Cost Analysis eleméhez. Például: **Cost Management + számlázási**  > **Cost Management**  > **Cost Analysis**.
1. Válassza **a Cost by Resource**lehetőséget.
1. Módosítsa a nézetet **táblázatra**.

![A költségcsökkentés megtekintése az Azure-erőforrás alapján](./media/cost-analysis-common-uses/cost-by-resource.png)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>A kiválasztott dimenziók szerinti költségcsökkentés megtekintése

A méretek lehetővé teszik a költségek rendszerezését a díjakban bemutatott különböző metaadat-értékek alapján. Például a költségeket a hely alapján csoportosíthatja.

1. A Azure Portal navigáljon a hatókör Cost Analysis eleméhez. Például: **Cost Management + számlázási**  > **Cost Management**  > **Cost Analysis**.
1. Válassza ki a **Csoportosítás** szűréssel lehetőséget.  
    Csoportosítás ![Select tétel alapján ](./media/cost-analysis-common-uses/group-by.png)
1. A nézetet később is elmentheti.
1. A diagram alatti tortadiagramra kattintva részletesebb információt jeleníthet meg.  
    ![A kiválasztott dimenziók szerinti költségcsökkentés megtekintése](./media/cost-analysis-common-uses/drill-down.png)

## <a name="view-costs-per-day-or-by-month"></a>Napi/havi megtekintés

A napi és havi költségek megvizsgálása révén jobban megismerheti, hogy van-e idő a hét vagy az év között, ahol a költségek magasabbak. Ha több ügyfél-forgalom van a munkaszüneti időszakban, az az Azure-költségek megfelelő növelését eredményezi? A hétfő drágább, mint a hétfő?

1. A Azure Portal navigáljon a hatókör Cost Analysis eleméhez. Például: **Cost Management + számlázási**  > **Cost Management**  > **Cost Analysis**.
1. Állítsa a **részletességet** **havi** vagy **napi**értékre.

![Költségek napi megtekintése](./media/cost-analysis-common-uses/daily-granularity.png)

## <a name="view-costs-for-a-specific-tag"></a>Adott címke költségeinek megtekintése

Számos Azure-felhasználó címkét alkalmaz az erőforrásokra, például egy költséghely-vagy fejlesztői környezetre (éles és tesztelés), hogy jobban kategorizálja a díjakat. A címkék dimenzióként jelennek meg a Cost Analysis szolgáltatásban. A dimenzió használatával betekintést nyerhet az egyéni címkézési kategorizálásokra.

1. A Azure Portal navigáljon a hatókör Cost Analysis eleméhez. Például: **Cost Management + számlázási**  > **Cost Management**  > **Cost Analysis**.
1. A címke alapján válassza a **Csoportosítás** lehetőséget.

![Adott címke költségeinek megtekintése](./media/cost-analysis-common-uses/tag.png)

## <a name="download-your-usage-details"></a>A használati adatok letöltése

A használati adatok jelentési fájlja CSV formátumban a számlán felhalmozott összes díj részletezését tartalmazza. A jelentés segítségével összehasonlíthatja azt, és jobban megismerheti a számláját. A számlán szereplő összes számlázott díj a használati jelentésben lebontott díjaknak felel meg.

1. A Azure Portalban navigáljon a Számlázási fiók vagy előfizetés **használati és díjszabási** lapjára. Például: **Cost Management + számlázási**  > **számlázás**  > **használat + díj**.
1. Válassza ki a letöltendő sort, majd kattintson a letöltés szimbólumra.  
    ![Használat és díjak letöltése](./media/cost-analysis-common-uses/download1.png)
1.  Válassza ki a letölteni kívánt használati fájlt.  
    ![Válassza ki a letölteni kívánt használati fájlt](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>A havi nagyvállalati költség részletezésének megtekintése

Az EA-regisztráció a teljes szervezet költségeit felhalmozza. A költségek felmerülésének és az idő múlásával történő számlázásának megértése segít a megfelelő felek bevonásában a költségek felelősségteljes felügyeletének biztosításához.

1. A Azure Portal navigáljon a **Cost Management + számlázási**  > **áttekintése**elemre.
1. Kattintson az aktuális hónap **részletezése** lehetőségre, és tekintse meg a pénzügyi kötelezettségvállalását.  
    ![EA költségek áttekintése – összefoglalás ](./media/cost-analysis-common-uses/breakdown1.png)
1.  Kattintson a **használat és díjak** lapra, és tekintse meg az előző hónap bontását a kiválasztott TimeSpan.  
    ![Usage és díjak lap ](./media/cost-analysis-common-uses/breakdown2.png)

## <a name="view-enrollment-monthly-cost-by-term"></a>A regisztráció havi költségének megtekintése feltételek szerint

A beléptetés havi költségeinek grafikus nézetét használva megtudhatja, hogy egy adott időszakra vonatkozóan milyen költségek alakulnak ki és számlázottak.

1. A Azure Portal navigáljon a hatókör Cost Analysis eleméhez. Például: **Cost Management + számlázási**  > **Cost Management**  > **Cost Analysis**.
1. Válassza ki a regisztrációt, és állítsa be a beléptetési időszakot.
1. Állítsa a részletességet havi értékre, majd állítsa a nézetet **oszlopra (halmozott)** .

Az adatait csoportosíthatja és szűrheti részletesebb elemzés céljából.

![Havi beléptetési költség – időszak szerint](./media/cost-analysis-common-uses/enrollment-term1.png)

## <a name="view-ea-enrollment-accumulated-costs"></a>Az EA-regisztrációban felhalmozott költségek megtekintése

Tekintse meg a nettó halmozott díjakat az idő múlásával, hogy megértse az adott időszakra vonatkozó teljes kiadásokat.

1. A Azure Portal navigáljon a hatókör Cost Analysis eleméhez. Például: **Cost Management + számlázási**  > **Cost Management**  > **Cost Analysis**.
1. Válassza ki a regisztrációt, és tekintse meg az aktuálisan felhalmozott költségeket.

![Beléptetések halmozott költségei](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)

## <a name="next-steps"></a>Következő lépések
- Ha még nem végezte el a Cost Management első gyors útmutatóját, olvassa el a [költségeket](quick-acm-cost-analysis.md).
- Olvassa el a [Cost Management dokumentációját](index.yml).

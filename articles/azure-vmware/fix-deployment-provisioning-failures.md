---
title: Segítség kérése az Azure VMware-megoldás üzembe helyezésével vagy a kiépítési hibákkal kapcsolatban
description: Hogyan kérheti le a szükséges információkat az Azure VMware-megoldás privát felhőből az Azure VMware-megoldás üzembe helyezésére vagy kiépítésére vonatkozó szolgáltatási kérelem beszerzéséhez.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 63d5440a9e2b15463e465e1d32762889508feca1
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/22/2020
ms.locfileid: "88752232"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Segítség kérése az Azure VMware-megoldás üzembe helyezésével vagy a kiépítési hibákkal kapcsolatban

Ebből a cikkből megtudhatja, hogyan kérhet segítséget az Azure VMware-megoldás üzembe helyezéséhez vagy a hibák kiépítéséhez a privát felhőben, ha megnyit egy szolgáltatásfiókot (SR) a Azure Portal. Először is össze kell gyűjtenie néhány kulcsfontosságú információt a Azure Portal. A legtöbb esetben a következőkre lesz szüksége:

- Korrelációs azonosító (sikertelen központi telepítés)
- ExpressRoute Circuit ID (ha egy meglévő privát felhőt szeretne méretezni a saját Felhőbeli ExpressRoute áramkörrel, és nem sikerül)

## <a name="collect-the-correlation-id"></a>A korrelációs azonosító gyűjtése
 
Nézzük meg először a korrelációs azonosítót. Amikor létrehoz egy saját felhőt (vagy bármilyen erőforrást az Azure-ban), létrejön egy társított korrelációs azonosító. Minden Azure Resource Manager üzemelő példány egyedi korrelációs azonosítót is létrehoz. Ez az azonosító gyorsabb SR-létrehozást és-feloldást tesz lehetővé. 
 
Itt látható egy példa arra, hogy egy sikertelen belső Felhőbeli üzemelő példány kimenete ki van emelve a korrelációs AZONOSÍTÓval.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Nem sikerült a saját Felhőbeli üzembe helyezés korrelációs AZONOSÍTÓval.":::

Másolja és mentse ezt a korrelációs azonosítót a szolgáltatási kérelembe való felvételhez. További részletekért lásd a [támogatási kérelem létrehozásáról](#create-your-support-request) szóló cikket a cikk végén.

Ha a hiba az előzetes ellenőrzési fázisokban történik, a privát felhő üzembe helyezése előtt nem jön létre korrelációs azonosító. Ebben az esetben egyszerűen megadhatja az Azure VMware-megoldás saját felhő létrehozásakor használt adatokat, beleértve a következőket:

- Hely
- Erőforráscsoport
- Erőforrás neve
 
### <a name="collect-a-summary-of-errors"></a>A hibák összefoglalásának összegyűjtése

A hibák részletei a probléma megoldásához is hasznosak lehetnek. Az előző képernyőn válassza a **részletek** (Kiemelt), majd a hibák összefoglalása lehetőséget, ahogy az alábbi képernyőképen is látható.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Hibák összefoglalása.":::

Újból másolja ki és mentse el az összegzést, hogy szerepeljen az SR-ben.
 
### <a name="retrieve-past-deployments"></a>Korábbi üzemelő példányok beolvasása

A korábbi üzemelő példányokat, például a sikerteleneket is lekérheti, ha a központi telepítési tevékenység naplójában megkeresi az értesítések ikont.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Nyisson meg értesítéseket.":::

Az értesítések területen válassza **a tevékenység naplójának további események**elemét.

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Hivatkozás: további események a tevékenység naplójában.":::

Ezután keressen rá az erőforrás nevére, vagy egy másik, az erőforrás létrehozásakor használt információra, hogy megkeresse a sikertelen üzembe helyezést és a korrelációs AZONOSÍTÓját. Az alábbi példa egy privát felhőalapú erőforrás (pc03) keresési eredményeit jeleníti meg.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="A korábbi sikertelen Azure VMware megoldás-telepítések keresése.":::
 
Ha kiválasztja a sikertelen telepítés műveletének nevét, megnyílik egy ablak, amely részleteket tartalmaz. Válassza a JSON fület, és keresse meg a correlationId. Másolás és Belefoglalás az SR-ben. 
 
## <a name="collect-the-expressroute-id-uri"></a>A ExpressRoute-azonosító (URI) összegyűjtése
 
Lehet, hogy már rendelkezik saját felhővel, és hiba lép fel, amikor a saját Felhőbeli ExpressRoute áramkörrel szeretné méretezni a méretezést vagy a társat. Ebben az esetben a privát felhő ExpressRoute-AZONOSÍTÓját használhatja az SR létrehozásakor.

Ha privát felhőt tekint meg a portálon, válassza a **kapcsolat > ExpressRoute** elemet, és másolja a **ExpressRoute-azonosítót** a vágólapra.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Másolja a ExpressRoute-azonosítót a vágólapra."::: 
 
Illessze be a ExpressRoute-azonosítót az új támogatási kérelem megfelelő mezőjébe. További információkért tekintse [meg a támogatási kérést](#create-your-support-request)a következő szakaszban:.
 
> [!NOTE]
> Alkalmanként előfordulhat, hogy az előzetes ellenőrzés az üzembe helyezés előtt meghiúsul, és az egyetlen elérhető információ a hiba és/vagy hibaüzenet. Ezek számos hiba esetén hasznosak lehetnek, például a kvótával kapcsolatos problémák esetén, és fontos, hogy ezeket az üzeneteket a támogatási kérelem tartalmazza. Ezek összegyűjtéséhez tekintse meg a korábbi szakaszt, és [Gyűjtse össze a hibák összefoglalását](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Támogatási kérelem létrehozása

A támogatási kérelem létrehozásával kapcsolatos általános útmutatásért lásd: [Azure-támogatási kérelem létrehozása](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Az alábbiakban további útmutatást talál az SR Azure-beli VMware-megoldás üzembe helyezéséhez vagy kiépítési hibákhoz való létrehozásához.

1. Válassza a **Súgó** ikont, majd az **+ új támogatási kérelmet**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="ExpressRoute-azonosító gyűjtése az SR-hez.":::

2. Töltse ki az összes kötelező mezőt, majd az **alapok** lapon:

    - A **probléma típusa**beállításnál válassza a **konfigurációs és beállítási problémák**lehetőséget.

    - A **probléma altípusa**beállításnál válassza **a privát felhő kiépítése**lehetőséget.

3. A **részletek** lapon:

    - Töltse ki az összes kötelező mezőt.

    - Illessze be a korrelációs azonosítót vagy a ExpressRoute AZONOSÍTÓját a megadott mezőkbe. Ha nem lát egy adott mezőt ezekhez a mezőkhöz, a **probléma részleteinek megadásával** illessze be őket a szövegmezőbe.

    - Illessze be a hibák részleteit, beleértve a másolt hibák összegzését a **probléma részleteinek megadása** területen található szövegmezőbe.

4. Tekintse át és válassza a **Létrehozás** lehetőséget az SR létrehozásához.

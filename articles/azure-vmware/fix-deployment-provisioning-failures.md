---
title: Segítség kérése az Azure VMware-megoldás üzembe helyezésével vagy a kiépítési hibákkal kapcsolatban
description: Hogyan kérheti le a szükséges információkat az Azure VMware-megoldás privát felhőből az Azure VMware-megoldás üzembe helyezésére vagy kiépítésére vonatkozó szolgáltatási kérelem beszerzéséhez.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 1f46dde895db417fd2b488a6203d5482e73d3c5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779492"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Segítség kérése az Azure VMware-megoldás üzembe helyezésével vagy a kiépítési hibákkal kapcsolatban

Ez a cikk segítséget nyújt az Azure VMware-megoldás üzembe helyezéséhez és a kiépítési hibákhoz. Ha hibák vannak a privát felhőben, meg kell nyitnia egy [támogatási kérést](https://rc.portal.azure.com/#create/Microsoft.Support) (SR) a Azure Portal. 

Először is össze kell gyűjtenie néhány fontos információt a Azure Portalban:

- Korrelációs azonosító
- ExpressRoute-áramkör azonosítója

## <a name="collect-the-correlation-id"></a>A korrelációs azonosító gyűjtése
 
A rendszer létrehoz egy korrelációs azonosítót, amikor létrehoz egy saját felhőt vagy bármilyen erőforrást az Azure-ban. Minden Azure Resource Manager üzemelő példány egy korrelációs azonosítót is létrehoz. Ez az azonosító gyorsabb SR-létrehozást és-feloldást tesz lehetővé. 
 
Itt látható egy példa arra, hogy egy sikertelen belső Felhőbeli üzemelő példány kimenete ki van emelve a korrelációs AZONOSÍTÓval.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Nem sikerült a saját Felhőbeli üzembe helyezés korrelációs AZONOSÍTÓval.":::

Másolja és mentse ezt a korrelációs azonosítót a szolgáltatási kérelembe való felvételhez. További részletekért lásd a [támogatási kérelem létrehozásáról](#create-your-support-request) szóló cikket a cikk végén.

Ha a hiba az előzetes ellenőrzési fázisokban fordul elő, a rendszer nem hoz létre korrelációs azonosítót. Ebben az esetben megadhatja az Azure VMware-megoldás saját felhő létrehozásakor használt adatokat, beleértve a következőket:

- Hely
- Erőforráscsoport
- Erőforrás neve
 
### <a name="collect-a-summary-of-errors"></a>A hibák összefoglalásának összegyűjtése

A hibák részletei segíthetnek a probléma megoldásában is. Az előző képernyőn válassza ki a figyelmeztető üzenetet a hibák összegzésének megtekintéséhez.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Nem sikerült a saját Felhőbeli üzembe helyezés korrelációs AZONOSÍTÓval.":::

Újból másolja ki és mentse el az összegzést, hogy szerepeljen az SR-ben.
 
### <a name="retrieve-past-deployments"></a>Korábbi üzemelő példányok beolvasása

A korábbi üzemelő példányokat, például a sikerteleneket is lekérheti, ha a központi telepítési tevékenység naplójában megkeresi az értesítések ikont.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Nem sikerült a saját Felhőbeli üzembe helyezés korrelációs AZONOSÍTÓval.":::

Az értesítések területen válassza **a tevékenység naplójának további események** elemét.

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Nem sikerült a saját Felhőbeli üzembe helyezés korrelációs AZONOSÍTÓval.":::

Ezután keresse meg az erőforrás nevét, vagy az erőforrás létrehozásához használt egyéb információkat a sikertelen központi telepítés és a korrelációs azonosító megkereséséhez. Az alábbi példa egy privát felhőalapú erőforrás (pc03) keresési eredményeit jeleníti meg.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Nem sikerült a saját Felhőbeli üzembe helyezés korrelációs AZONOSÍTÓval.":::
 
Ha kiválasztja a sikertelen telepítés műveletének nevét, megnyílik egy ablak, amely részleteket tartalmaz. Válassza a JSON fület, és keresse meg a correlationId. Másolás és Belefoglalás az SR-ben. 
 
## <a name="collect-the-expressroute-id-uri"></a>A ExpressRoute-azonosító (URI) összegyűjtése
 
Lehet, hogy egy meglévő privát felhőt szeretne méretezni a saját felhőalapú ExpressRoute áramkörével, és ez nem sikerül. Ebben az esetben szüksége lesz a ExpressRoute-AZONOSÍTÓra. 

A Azure Portal válassza a **kapcsolat > ExpressRoute** elemet, és másolja a **ExpressRoute-azonosítót** a vágólapra.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Nem sikerült a saját Felhőbeli üzembe helyezés korrelációs AZONOSÍTÓval."::: 
 
> [!NOTE]
> Időnként előfordulhat, hogy az ellenőrzés előtti ellenőrzések sikertelenek lesznek az üzembe helyezés előtt, és az egyetlen rendelkezésre álló információ lesz a hiba-és hibaüzenetek. Ezek számos hiba esetén hasznosak lehetnek, például a kvótával kapcsolatos problémák esetén, és fontos, hogy ezeket az üzeneteket a támogatási kérelem tartalmazza. Ezek összegyűjtéséhez tekintse meg a korábbi szakaszt, és [Gyűjtse össze a hibák összefoglalását](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Támogatási kérelem létrehozása

A támogatási kérelem létrehozásával kapcsolatos általános útmutatásért lásd: [Azure-támogatási kérelem létrehozása](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Az alábbiakban az SR for Azure VMware-megoldás üzembe helyezéséhez és a kiépítési hibákhoz való létrehozásával kapcsolatos útmutatóban talál konkrét útmutatást.

1. Válassza a **Súgó** ikont, majd az **+ új támogatási kérelmet** .

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Nem sikerült a saját Felhőbeli üzembe helyezés korrelációs AZONOSÍTÓval.":::

2. Töltse ki az összes kötelező mezőt, majd az **alapok** lapon:

    - A **probléma típusa** beállításnál válassza a **konfigurációs és beállítási problémák** lehetőséget.

    - A **probléma altípusa** beállításnál válassza **a privát felhő kiépítése** lehetőséget.

3. A **részletek** lapon:

    - Töltse ki az összes kötelező mezőt.

    - Illessze be a korrelációs azonosítót vagy a ExpressRoute AZONOSÍTÓját a megadott mezőkbe. Ha nem lát egy adott mezőt, beillesztheti őket a **probléma részleteinek megadása** területen található szövegmezőbe.

    - Illessze be a hibák részleteit, beleértve a másolt hibák összegzését a **probléma részleteinek megadása** területen található szövegmezőbe.

4. Tekintse át és válassza a **Létrehozás** lehetőséget az SR létrehozásához.

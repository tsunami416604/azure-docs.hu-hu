---
title: 'Azure ExpressRoute: a ExpressRoute Direct konfigurálása: portál'
description: Ez az oldal segítséget nyújt a ExpressRoute Direct konfigurálásához a portál használatával.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: how-to
ms.date: 06/12/2020
ms.author: cherylmc
ms.openlocfilehash: f22f81d69d746764af43d9167874aabedbd56f51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84765441"
---
# <a name="create-expressroute-direct-using-the-azure-portal"></a>Közvetlen ExpressRoute létrehozása a Azure Portal használatával

Ez a cikk bemutatja, hogyan hozhat létre közvetlen ExpressRoute a Azure Portal használatával.
A ExpressRoute Direct szolgáltatással közvetlenül kapcsolódhat a Microsoft globális hálózatához a világ minden pontján elérhető, stratégiai módon elosztott helyen. További információ: [Az ExpressRoute Direct ismertetése](expressroute-erdirect-about.md).

## <a name="1-create-expressroute-direct"></a><a name="create-erdir"></a>1. ExpressRoute közvetlen létrehozása

1. A [Azure Portal](https://portal.azure.com) menüben vagy a **Kezdőlap** lapon válassza az **erőforrás létrehozása**lehetőséget.

1. Az **új** lap ***Keresés a piactéren*** mezőjébe írja be a **ExpressRoute Direct**kifejezést, majd válassza az **ENTER** billentyűt a keresési eredmények eléréséhez.

1. Az eredmények közül válassza a **közvetlen ExpressRoute**lehetőséget.

1. A **ExpressRoute Direct** lapon válassza a **Létrehozás** lehetőséget a **ExpressRoute közvetlen létrehozása** lap megnyitásához.

1. Első lépésként töltse ki a mezőket az **alapok** oldalon.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/basics.png" alt-text="Alapismeretek lap":::

    * **Előfizetés**: az új ExpressRoute közvetlen létrehozásához használni kívánt Azure-előfizetés. A ExpressRoute Direct erőforrás-és ExpressRoute-áramköröknek ugyanahhoz az előfizetéshez kell tartoznia.
    * **Erőforráscsoport**: az az Azure-erőforráscsoport, amelyben a rendszer létrehozza az új ExpressRoute Direct-erőforrást a alkalmazásban. Ha nem rendelkezik meglévő erőforráscsoporthoz, létrehozhat egy újat.
    * **Régió**: az Azure nyilvános régiója, amelyhez az erőforrást létre kívánja hozni.
    * **Name (név**): az új ExpressRoute közvetlen erőforrás neve.

1. Ezután fejezze be a mezőket a **konfiguráció** lapon.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration.png" alt-text="Konfigurációs lap":::

    * **Egyenrangú hely**: az a társítási hely, ahol csatlakozni fog a ExpressRoute Direct-erőforráshoz. További információ a [ExpressRoute helyeiről](expressroute-locations-providers.md).
   * **Sávszélesség**: a lefoglalni kívánt port pár sávszélessége. A ExpressRoute Direct a 10 GB-os és 100 GB-os sávszélesség-beállításokat egyaránt támogatja. Ha a kívánt sávszélesség nem érhető el a megadott egyenrangú helyen, [Nyisson meg egy támogatási kérést a Azure Portal](https://aka.ms/azsupt).
   * **Beágyazás**: a ExpressRoute Direct a QinQ és a Dot1Q beágyazást is támogatja.
     * Ha a QinQ van kiválasztva, az egyes ExpressRoute-körök dinamikusan hozzá lesznek rendelve egy S-címkéhez, és egyediek lesznek a ExpressRoute közvetlen erőforrásaiban.
     *  Az áramkörön minden C-címkének egyedinek kell lennie az áramkörön, a közvetlen ExpressRoute azonban nem.
     * Ha a Dot1Q beágyazás van kiválasztva, akkor a C-tag (VLAN) egyediségét kell kezelnie a teljes ExpressRoute közvetlen erőforrásban.
     >[!IMPORTANT]
     >A ExpressRoute Direct csak egy beágyazási típus lehet. A beágyazás nem módosítható a közvetlen ExpressRoute létrehozása után.
     >

1. Adja meg az összes erőforrás címkéjét, majd válassza a **felülvizsgálat + létrehozás** lehetőséget a ExpressRoute közvetlen erőforrás-beállításainak ellenőrzéséhez.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/validate.png" alt-text="Áttekintés és létrehozás":::

1. Válassza a **Létrehozás** lehetőséget. Megjelenik egy üzenet, amely tájékoztatja, hogy a telepítés folyamatban van. Az állapot ekkor megjelenik ezen a lapon az erőforrások létrehozásakor. 

## <a name="2-change-admin-state-of-links"></a><a name="state"></a>2. a hivatkozások rendszergazdai állapotának módosítása

Ezt a folyamatot kell használni az 1. rétegbeli tesztek elvégzéséhez, hogy az egyes kapcsolatok megfelelően legyenek kijavítani az egyes útválasztók számára az elsődleges és a másodlagos számára.

1. A ExpressRoute közvetlen erőforrásának **áttekintése** lap **hivatkozások** területén válassza a **link1**lehetőséget.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/link.png" alt-text="1. hivatkozás" lightbox="./media/how-to-expressroute-direct-portal/link-expand.png":::

1. Állítsa be a **rendszergazdai állapot** beállítást **engedélyezve**értékre, majd válassza a **Mentés**lehetőséget.

    :::image type="content" source="./media/how-to-expressroute-direct-portal/state.png" alt-text="Rendszergazdai állapot":::

    >[!IMPORTANT]
    >A számlázás akkor kezdődik, amikor a rendszergazdai állapot engedélyezve van bármelyik hivatkozáson.
    >

1. Ismételje meg ugyanezt a folyamatot a **link2**.

## <a name="3-create-a-circuit"></a><a name="circuit"></a>3. áramkör létrehozása

Alapértelmezés szerint 10 áramkört hozhat létre az előfizetésben, ahol a ExpressRoute Direct erőforrás. Ezt a számot támogatással növelheti. A kiosztott és a felhasznált sávszélesség nyomon követése felelős. A kiépített sávszélesség az összes áramkör sávszélességének összege a ExpressRoute közvetlen erőforráson. A felhasznált sávszélesség a mögöttes fizikai felületek fizikai használata.

* A fentiekben ismertetett forgatókönyvek támogatásához további áramköri sávszélességek is használhatók, amelyek csak a ExpressRoute közvetlen használatával használhatók. Ezek a következők: 40 Gbps és 100 Gbps.

* A SkuTier lehet helyi, standard vagy prémium.

* A SkuFamily csak MeteredData kell lennie. A ExpressRoute Direct nem támogatja a korlátlan használatát.

Az alábbi lépések segítségével ExpressRoute-áramkört hozhat létre a ExpressRoute Direct munkafolyamatból. Ha inkább egy áramkört is létrehozhat a normál áramköri munkafolyamattal, de nem használja ki az ebben a konfigurációban a normál áramköri munkafolyamat lépéseit. Lásd: [ExpressRoute-kör létrehozása és módosítása](expressroute-howto-circuit-portal-resource-manager.md).

1. A ExpressRoute közvetlen **beállításai** szakaszban válassza az **áramkörök**, majd a **+ Hozzáadás**lehetőséget. 

    :::image type="content" source="./media/how-to-expressroute-direct-portal/add.png" alt-text="Hozzáadás" lightbox="./media/how-to-expressroute-direct-portal/add-expand.png":::

1. Konfigurálja a beállításokat a **konfiguráció** lapon.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/configuration2.png" alt-text="Konfigurációs lap":::

1. Adjon meg bármilyen erőforrás-címkét, majd az erőforrás létrehozása előtt válassza a **felülvizsgálat + létrehozás** lehetőséget az értékek érvényesítéséhez.

   :::image type="content" source="./media/how-to-expressroute-direct-portal/review.png" alt-text="Áttekintés és létrehozás":::

1. Válassza a **Létrehozás** lehetőséget. Megjelenik egy üzenet, amely tájékoztatja, hogy a telepítés folyamatban van. Az állapot ekkor megjelenik ezen a lapon az erőforrások létrehozásakor. 

## <a name="next-steps"></a>További lépések

A közvetlen ExpressRoute kapcsolatos további információkért tekintse meg az [áttekintést](expressroute-erdirect-about.md).

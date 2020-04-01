---
title: Oktatóanyag – Földrajzi forgalomútválasztás konfigurálása az Azure Traffic Managerrel
description: Ez az oktatóanyag bemutatja, hogyan konfigurálható a földrajzi forgalomútválasztási módszer az Azure Traffic Manager használatával
services: traffic-manager
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: rohink
ms.openlocfilehash: 3eb3f354d51833e55f405ed35679f1a5882c057a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76938791"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Oktatóanyag: Konfigurálja a földrajzi forgalomútválasztási módszert a Traffic Manager használatával

A földrajzi forgalom útválasztási módszer lehetővé teszi, hogy a forgalmat a földrajzi hely alapján, ahol a kérelmek származnak, a forgalmat adott végpontokra irányítsa. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy Traffic Manager-profilt ezzel az útválasztási módszerrel, és konfigurálhatja a végpontokat adott földrajzi területekről érkező forgalom fogadására.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/).
2. Kattintson **az Erőforrás** > **hálózatkezelés** > **forgalomkezelőjének** > létrehozása**profil létrehozása elemre.**
4. A **Traffic Manager létrehozása profilban:**
    1. Adja meg a profil nevét. Ennek a névnek egyedinek kell lennie a trafficmanager.net zónán belül. A Traffic Manager-profil eléréséhez használja `<profilename>.trafficmanager.net`a DNS-nevet.
    2. Válassza ki a **Földrajzi** útválasztási módszert.
    3. Válassza ki azt az előfizetést, amely alatt létre szeretné hozni ezt a profilt.
    4. Használjon meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot a profil alá. Ha új erőforráscsoportot hoz létre, az **Erőforráscsoport hely** legördülő menüjében adja meg az erőforráscsoport helyét. Ez a beállítás az erőforráscsoport helyére utal, és nincs hatással a Traffic Manager globálisan telepített profilra.
    5. Miután a **Létrehozás gombra**kattintott, a Traffic Manager-profil globálisan jön létre és kerül telepítésre.

![Traffic Manager-profil létrehozása](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Végpontok hozzáadása

1. Keresse meg a portál keresősávjában létrehozott Traffic Manager-profilnevet, és kattintson az eredményre, amikor megjelenik.
2. Nyissa meg a **Beállítások** -> **végpontok lapot** a Traffic Manager programban.
3. Kattintson a **Hozzáadás** gombra a **Végpont hozzáadása megjelenítéséhez.**
3. Kattintson a **Hozzáadás** gombra, és a megjelenő **Végpont hozzáadása** területen a következőképpen hajtsa végre:
4. Válassza a **Típus** lehetőséget a hozzáadni kívánt végpont típusától függően. Az éles környezetben használt földrajzi útválasztási profilok esetében javasoljuk, hogy egynél több végpontot tartalmazó beágyazott végponttípusokat használjon. További információt a [földrajzi forgalomútválasztási módszerekkel kapcsolatos gyakori kérdések](traffic-manager-FAQs.md)ben talál.
5. Adjon meg egy **Nevet**, amelyről felismeri majd a végpontot.
6. Az oldal bizonyos mezői a hozzáadni kívánt végpont típusától függenek:
    1. Ha Azure-végpontot ad hozzá, válassza ki a **Cél erőforrástípust** és a **Cél elemet** a forgalom irányításához kívánt erőforrás alapján.
    2. Ha **külső** végpontot ad hozzá, adja meg a **végpont teljesen minősített tartománynevét (FQDN).**
    3. **Beágyazott végpont**hozzáadásaesetén jelölje ki a használni kívánt gyermekprofilnak megfelelő **Cél erőforrást,** és adja meg a Minimális **gyermekvégpontok számát.**
7. A Geo-mapping szakaszban a legördülő menüben adja hozzá azokat a régiókat, ahonnan a kívánt forgalmat erre a végpontra szeretné küldeni. Hozzá kell adnia legalább egy régiót, és több régiót is leképezhet.
8. Ismételje meg ezt a profil alatt hozzáadni kívánt összes végpontesetében.

![Traffic Manager-végpont hozzáadása](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>A Traffic Manager-profil használata
1.  A portál keresősávján keresse meg az előző szakaszban létrehozott **Traffic Manager-profilnevét,** és kattintson a forgalomkezelő profiljára a megjelenített eredmények között.
2. Kattintson az **Áttekintés** elemre.
3. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve. Ezt bármely ügyfél használhatja (például webböngészővel odanavigálva) az útválasztási típus által meghatározott megfelelő végpontra irányítva.  Földrajzi útválasztás esetén a Traffic Manager megvizsgálja a bejövő kérelem forrás IP-címét, és meghatározza azt a régiót, ahonnan származik. Ha ez a régió egy végponthoz van rendelve, a forgalom oda lesz irányítva. Ha ez a terület nincs végponthoz rendelve, majd a Traffic Manager egy NODATA lekérdezési választ ad vissza.

## <a name="next-steps"></a>További lépések

- További információ a [földrajzi forgalom útválasztási módjáról.](traffic-manager-routing-methods.md#geographic)
- További információ a [Traffic Manager beállításainak teszteléséről.](traffic-manager-testing-settings.md)

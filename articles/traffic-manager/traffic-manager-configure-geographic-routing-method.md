---
title: Oktatóanyag – földrajzi forgalom útválasztásának konfigurálása az Azure Traffic Manager
description: Ez az oktatóanyag azt ismerteti, hogyan konfigurálható a földrajzi forgalom útválasztási módszere az Azure Traffic Manager használatával
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
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938791"
---
# <a name="tutorial-configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>Oktatóanyag: a földrajzi forgalom útválasztási módszerének konfigurálása Traffic Manager használatával

A földrajzi forgalom útválasztási módszere lehetővé teszi, hogy az adott végpontokra irányítsa a forgalmat azon a földrajzi hely alapján, ahol a kérelmek származnak. Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Traffic Manager profilt ezzel az útválasztási módszerrel, és hogyan konfigurálhatja a végpontokat az adott földrajzi helyekről érkező forgalom fogadására.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](https://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/).
2. Kattintson az **Erőforrás létrehozása** > **Hálózat** > **Traffic Manager-profil** > **Létrehozás** lehetőségre.
4. A **Traffic Manager létrehozása profilban**:
    1. Adja meg a profil nevét. Ennek a névnek egyedinek kell lennie az trafficmanager.net zónán belül. A Traffic Manager-profil eléréséhez használja a DNS-nevet `<profilename>.trafficmanager.net`.
    2. Válassza ki a **földrajzi** útválasztási módszert.
    3. Válassza ki azt az előfizetést, amelyhez a profilt létre kívánja hozni.
    4. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot, amelybe a profilt be kívánja helyezni. Ha úgy dönt, hogy létrehoz egy új erőforráscsoportot, az **erőforráscsoport helye** legördülő menüben adja meg az erőforráscsoport helyét. Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a globálisan telepített Traffic Manager-profilra.
    5. Miután rákattintott a **Létrehozás**gombra, a rendszer a Traffic Manager-profilt globálisan hozza létre és telepíti.

![Traffic Manager-profil létrehozása](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Végpontok hozzáadása

1. Keresse meg a portál keresési sávjában létrehozott Traffic Manager profil nevét, és kattintson az eredményre, amikor megjelenik.
2. Navigáljon a **beállítások** -> a Traffic Manager **végpontok** elemre.
3. A Hozzáadás **végpont**megjelenítéséhez kattintson a **Hozzáadás** gombra.
3. Kattintson a **Hozzáadás** gombra, és a megjelenő **végpont hozzáadása** lapon fejezze be a következőt:
4. Válassza a **típus** lehetőséget a hozzáadni kívánt végpont típusától függően. Az éles környezetben használt földrajzi útválasztási profilok esetében javasoljuk, hogy használjon olyan beágyazott végpontokat, amelyek egynél több végponttal rendelkező alárendelt profilt tartalmaznak. További részletekért tekintse meg a [földrajzi forgalom útválasztási módszereivel kapcsolatos gyakori kérdések](traffic-manager-FAQs.md)című témakört.
5. Adjon meg egy **Nevet**, amelyről felismeri majd a végpontot.
6. Ezen a lapon bizonyos mezők a hozzáadott végpont típusától függenek:
    1. Ha Azure-végpontot ad hozzá, válassza ki a **cél erőforrás típusát** és **célját** azon erőforrás alapján, amelynek a forgalmát át szeretné irányítani
    2. Ha **külső** végpontot ad hozzá, adja meg a végpont teljes tartománynevét **(FQDN)** .
    3. Ha **beágyazott végpontot**ad hozzá, válassza ki a használni kívánt gyermek profilnak megfelelő **cél erőforrást** , és adja meg a **gyermek végpontok minimális darabszámát**.
7. A földrajzi leképezés szakaszban a legördülő lista használatával adja hozzá azokat a régiókat, amelyekről szeretné elküldeni a forgalmat a végpontnak. Legalább egy régiót fel kell vennie, és több régió is leképezhető.
8. Ezt ismételje meg a profilban hozzáadni kívánt összes végpont esetében

![Traffic Manager-végpont hozzáadása](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>A Traffic Manager profil használata
1.  A portálon keresse meg az előző szakaszban létrehozott **Traffic Manager profil** nevét, majd kattintson a Traffic Manager-profilra a megjelenített eredmények között.
2. Kattintson az **Áttekintés** elemre.
3. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve. Ezt bármely ügyfél használhatja (például a böngésző használatával történő navigálással), hogy a jobb oldali végpontra irányítsa át az útválasztási típus alapján.  Földrajzi útválasztás esetén Traffic Manager a bejövő kérelem forrás IP-címét vizsgálja, és meghatározza azt a régiót, ahonnan származik. Ha az adott régió egy végpontra van leképezve, a rendszer átirányítja a forgalmat oda. Ha ez a régió nincs leképezve végpontra, akkor Traffic Manager egy Adatlekérdezési választ ad vissza.

## <a name="next-steps"></a>Következő lépések

- További információ a [földrajzi forgalom útválasztási módszeréről](traffic-manager-routing-methods.md#geographic).
- Ismerje meg, hogyan [tesztelheti Traffic Manager beállításait](traffic-manager-testing-settings.md).

---
title: Konfigurálja a földrajzi forgalom-útválasztási módszer az Azure Traffic Managerrel |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálhatja a földrajzi forgalom-útválasztási módszert az Azure Traffic Managerrel
services: traffic-manager
documentationcenter: ''
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 5346617c858ab3bc168781c76985729c2da6a768
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49984349"
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>A földrajzi forgalom-útválasztási módszer használatával a Traffic Manager konfigurálása

A földrajzi forgalom-útválasztási módszer lehetővé teszi, hogy a forgalmat a földrajzi hely, ahol az ügyfélkérések alapján meghatározott végpontokhoz. Ez az oktatóanyag bemutatja, egy Traffic Manager-profil létrehozása az útválasztási módszer, és konkrét földrajzi területek számára a forgalom fogadására a végpontok konfigurálása.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/).
2. Kattintson az **Erőforrás létrehozása** > **Hálózat** > **Traffic Manager-profil** > **Létrehozás** lehetőségre.
4. Az a **Traffic Manager-profil létrehozása**:
    1. Adja meg a profil nevét. Ez a név egyedinek kell lennie a trafficmanager.net zónában. A Traffic Manager-profil eléréséhez használja a DNS-név <profilename>. trafficmanager.net.
    2. Válassza ki a **Geographic** esetén használt útválasztási módszer.
    3. Ez a profil a létrehozni kívánt előfizetés kiválasztásához.
    4. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot, ahová a profil alapján. Ha egy új erőforráscsoportot létrehozni, használja a **erőforráscsoport helye** legördülő listában adja meg az erőforráscsoport helyét. Ez a beállítás az erőforráscsoport helyére vonatkozik, és nem befolyásolja a Traffic Manager-profilt, amely globálisan üzembe van helyezve.
    5. Miután rákattintott **létrehozás**, a Traffic Manager-profil létrehozása és üzembe helyezett globálisan.

![Traffic Manager-profil létrehozása](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Végpontok hozzáadása

1. Keresse meg a Traffic Manager-profil neve, a portál keresősávjában létrehozott, majd kattintson az eredményekre látható.
2. Navigáljon a **beállítások** -> **végpontok** Traffic Managerben.
3. Kattintson a **Hozzáadás** megjelenítéséhez a **végpont hozzáadása**.
3. Kattintson a **Hozzáadás** és a **végpont hozzáadása** megjelenő, az alábbiak szerint:
4. Válassza ki **típus** attól függően, hogy a hozzáadni kívánt végpont típusa. Földrajzi útválasztási profilok éles környezetben használt javasoljuk, hogy egy gyermek profil egynél több végponttal tartalmazó beágyazott végponttípusok használatával. További részletekért lásd: [földrajzi forgalom-útválasztási módszer kapcsolatos gyakori kérdések](traffic-manager-FAQs.md).
5. Adjon meg egy **Nevet**, amelyről felismeri majd a végpontot.
6. Bizonyos mezők ezen az oldalon a hozzáadni kívánt végpont-típustól függnek:
    1. Egy Azure-végpont hozzáadásakor jelölje ki a **célerőforrás típusánál** és a **cél** adatforgalmat kívánt erőforrás alapján
    2. Hozzáadásakor egy **külső** végpont, adja meg a **teljes tartománynév (FQDN)** a végponthoz.
    3. Hozzáadásakor egy **beágyazott végpont**, jelölje be a **Célerőforrásnál** a gyermek profilt szeretne használni, és adja meg, amely megfelel a **minimális gyermek végpontok száma**.
7. A földrajzi hozzárendelés szakaszban le a legördülő menüből használatával adja hozzá a régiók, ahonnan csak szeretné, hogy ennek a végpontnak küldött forgalmat. Hozzá kell adnia legalább egy régiót, és a leképezett több régióban is használhat.
8. Ismételje meg ezt a ezt a profilt a hozzáadni kívánt összes végponthoz

![Traffic Manager-végpont hozzáadása](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>A Traffic Manager-profil használata
1.  A portál keresősávjában keressen a **Traffic Manager-profil** , amelyet az előző szakaszban, majd kattintson a traffic manager-profilt az eredmények között a nevét, amely jelenik meg.
2. Kattintson az **Áttekintés** elemre.
3. A **Traffic Manager-profil** mezőben megjelenik az újonnan létrehozott Traffic Manager-profil DNS-neve. Ezzel használhatja az ügyfelek (például megnyitásával, egy webböngésző használatával), mint a megfelelő végpontra van irányítva határozza meg az útválasztási típus.  Földrajzi útválasztásának esetén a Traffic Manager megvizsgálja a bejövő kérelem a forrás IP-címe, és határozza meg a régiót, amelyből származó. Ha az adott régióban van rendelve egy végpontot, forgalmat irányítja a rendszer itt. Ha ebben a régióban a rendszer nem rendeli hozzá egy végpontot, a Traffic Manager NODATA lekérdezés választ adja vissza.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Geographic forgalom-útválasztási módszer](traffic-manager-routing-methods.md#geographic).
- Ismerje meg, hogyan [Traffic Manager beállításainak tesztelése](traffic-manager-testing-settings.md).

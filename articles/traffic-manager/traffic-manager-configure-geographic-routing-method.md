---
title: "Földrajzi forgalom-útválasztási módszert használja az Azure Traffic Manager konfigurálása |} Microsoft Docs"
description: "Ez a cikk azt ismerteti, hogyan konfigurálhatja a földrajzi forgalom-útválasztási módszert használja az Azure Traffic Manager"
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 13190189074b24b2d28cd3ce46cf8571f3e1e1d1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>A földrajzi forgalom-útválasztási módszert használja a Traffic Manager konfigurálása

A földrajzi forgalom-útválasztási módszert lehetővé teszi a földrajzi hely, ahol a kérelmek származnak alapján meghatározott végpontokhoz forgalmat irányítani. Ez az oktatóanyag bemutatja, hogyan Traffic Manager-profil létrehozása az útválasztási metódushoz, és konfigurálja a végpontokat az adott földrajzi forgalom fogadására.

## <a name="create-a-traffic-manager-profile"></a>A Traffic Manager-profil létrehozása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/).
2. A központ menüben kattintson a **új** > **hálózati** > **láthatja az összes**, és kattintson a **Traffic Manager-profil** megnyitásához a **hozzon létre Traffic Manager-profil** panelen.
3. Az a **hozzon létre Traffic Manager-profil** panel:
    1. Adja meg a profil nevét. Ez a név egyedinek kell lennie a trafficmanager.net belül zónát, és hatására a DNS-nevét <profilename>, trafficmanager.net, amely a Traffic Manager-profil elérésére szolgál majd.
    2. Válassza ki a **Geographic** útválasztási módszer.
    3. Válassza ki az előfizetést, a profil létrehozásához.
    4. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot, ahová a profil alapján. Ha egy új erőforráscsoport létrehozása mellett dönt, használja a **az erőforráscsoport helye** legördülő lista segítségével megadhatja a helyet, az erőforráscsoport. Ez a beállítás az erőforráscsoport helye hivatkozik, és nincs hatással van a Traffic Manager-profilt, amely globálisan telepíti.
    5. Miután rákattintott **létrehozása**, a Traffic Manager-profil létrehozása és telepítése globálisan.

![Traffic Manager-profil létrehozása](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Végpont hozzáadása

1. Keresse meg a keresősávban a portál most létrehozott Traffic Manager-profil neve, majd kattintson a az eredmény, ha látható.
2. Navigáljon a **beállítások** -> **végpontok** a Traffic Manager panelén.
3. Kattintson a **Hozzáadás** megjelenítése a **végpont hozzáadása** panelen.
3. A a **végpontok** panelen kattintson a **Hozzáadás** és a a **végpont hozzáadása** panel, amelyen jelenik meg, végezze el az alábbiak szerint:
4. Válassza ki **típus** hozzáadni kívánt végpont függően. Éles környezetben használt földrajzi útválasztási profilokhoz erősen ajánlott több végpont gyermek profil tartalmazó beágyazott végpont típus használatával. További részletekért lásd: [földrajzi forgalom-útválasztási módszerekkel kapcsolatos gyakori kérdések](traffic-manager-FAQs.md).
5. Adjon meg egy **Nevet**, amelyről felismeri majd a végpontot.
6. Bizonyos mezők ezen a panelen hozzáadni kívánt végpont-típustól függnek:
    1. Ha egy Azure végpontot hozzáadni, jelölje be a **erőforrás Céltípust** és a **cél** forgalmat irányítani kívánt erőforrás alapján
    2. Ha ad hozzá egy **külső** végpont, adja meg a **teljesen minősített tartománynevét (FQDN)** használ a végponthoz.
    3. Hozzáadásakor egy **beágyazott végpont**, jelölje be a **célerőforrás** a gyermek profil használatára, és adja meg, amely megfelel a **minimális gyermek végpontok száma**.
7. A földrajzi-leképezés területen le az vetett használatával adhatja hozzá a régiók végponti küldendő forgalom, ahová a. Hozzá kell adni legalább egy régió tartozik, és akkor leképezése több régióba is.
8. Ismételje meg a hozzá szeretne adni ehhez a profilhoz az összes végpontok

![A Traffic Manager-végpont hozzáadása](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>A Traffic Manager-profil használata
1.  A portál keresősávban, keresse meg a **Traffic Manager-profil** az előző szakaszban, majd kattintson az eredményeket a traffic manager-profilt a létrehozott név, amely a jelennek meg.
2. Az a **Traffic Manager-profil** panelen kattintson a **áttekintése**.
3. A **Traffic Manager-profil** csempe megjeleníti az újonnan létrehozott Traffic Manager-profil DNS-nevét. Ez használhatja olyan ügyfelek (például úgy, hogy keresse meg webböngészővel) az beszerzése irányítja át a megfelelő végpont, határozza meg az Útválasztás típusa.  Földrajzi útválasztási Traffic Manager ellenőrzi, hogy a forrás IP-cím a bejövő kérelem, és meghatározza, hogy a régió, ahol származó. Ha adott régióban le van képezve egy végpontot, továbbítódik oda. A végpont sincs hozzárendelve az ebben a régióban, ha a Traffic Manager NODATA lekérdezés választ ad.

## <a name="next-steps"></a>Következő lépések

- További információ [Geographic forgalom-útválasztási módszer](traffic-manager-routing-methods.md#geographic).
- Megtudhatja, hogyan [Traffic Manager-beállítások tesztelésére](traffic-manager-testing-settings.md).

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
ms.openlocfilehash: 7b49e2a4eef5a966f1ef2aa283a3089bb5b73734
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="configure-the-geographic-traffic-routing-method-using-traffic-manager"></a>A földrajzi forgalom-útválasztási módszert használja a Traffic Manager konfigurálása

A földrajzi forgalom-útválasztási módszert lehetővé teszi a földrajzi hely, ahol a kérelmek származnak alapján meghatározott végpontokhoz forgalmat irányítani. Ez az oktatóanyag bemutatja, hogyan Traffic Manager-profil létrehozása az útválasztási metódushoz, és konfigurálja a végpontokat az adott földrajzi forgalom fogadására.

## <a name="create-a-traffic-manager-profile"></a>A Traffic Manager-profil létrehozása

1. Egy böngészőben jelentkezzen be az [Azure Portalra](http://portal.azure.com). Ha még nincs fiókja, regisztrálhat egy [egy hónapos ingyenes próbaverzióra](https://azure.microsoft.com/free/).
2. Kattintson a **hozzon létre egy erőforrást** > **hálózati** > **Traffic Manager-profil** > **létrehozása**.
4. Az a **hozzon létre Traffic Manager-profil**:
    1. Adja meg a profil nevét. Ez a név egyedinek kell lennie a trafficmanager.net zónán belül. A Traffic Manager-profil eléréséhez használja a DNS-nevet <profilename>. trafficmanager.net.
    2. Válassza ki a **Geographic** útválasztási módszer.
    3. Válassza ki az előfizetést, a profil létrehozásához.
    4. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy új erőforráscsoportot, ahová a profil alapján. Ha egy új erőforráscsoport létrehozása mellett dönt, használja a **az erőforráscsoport helye** legördülő lista segítségével megadhatja a helyet, az erőforráscsoport. Ez a beállítás az erőforráscsoport helye hivatkozik, és nincs hatással van a globális telepített Traffic Manager-profilt.
    5. Miután rákattintott **létrehozása**, a Traffic Manager-profil létrehozása és telepítése globálisan.

![Traffic Manager-profil létrehozása](./media/traffic-manager-geographic-routing-method/create-traffic-manager-profile.png)

## <a name="add-endpoints"></a>Végpont hozzáadása

1. Keresse meg a keresősávban a portálon létrehozott Traffic Manager-profil neve, majd kattintson a az eredmény, ha látható.
2. Navigáljon a **beállítások** -> **végpontok** őket a Traffic Manager.
3. Kattintson a **Hozzáadás** megjelenítése a **végpont hozzáadása**.
3. Kattintson a **Hozzáadás** és a **végpont hozzáadása** , amely akkor jelenik meg, az alábbiak szerint:
4. Válassza ki **típus** hozzáadni kívánt végpont függően. Éles környezetben használt földrajzi útválasztási profilokhoz erősen ajánlott több végpont gyermek profil tartalmazó beágyazott végpont típus használatával. További részletekért lásd: [földrajzi forgalom-útválasztási módszerekkel kapcsolatos gyakori kérdések](traffic-manager-FAQs.md).
5. Adjon meg egy **Nevet**, amelyről felismeri majd a végpontot.
6. Ezen az oldalon bizonyos mezők hozzáadni kívánt végpont-típustól függnek:
    1. Ha egy Azure végpontot hozzáadni, jelölje be a **erőforrás Céltípust** és a **cél** forgalmat irányítani kívánt erőforrás alapján
    2. Ha ad hozzá egy **külső** végpont, adja meg a **teljesen minősített tartománynevét (FQDN)** használ a végponthoz.
    3. Hozzáadásakor egy **beágyazott végpont**, jelölje be a **célerőforrás** a gyermek profil használatára, és adja meg, amely megfelel a **minimális gyermek végpontok száma**.
7. A földrajzi-leképezés területen le az vetett használatával adhatja hozzá a régiók végponti küldendő forgalom, ahová a. Hozzá kell adni legalább egy régió tartozik, és akkor leképezése több régióba is.
8. Ismételje meg a hozzá szeretne adni ehhez a profilhoz az összes végpontok

![A Traffic Manager-végpont hozzáadása](./media/traffic-manager-geographic-routing-method/add-traffic-manager-endpoint.png)

## <a name="use-the-traffic-manager-profile"></a>A Traffic Manager-profil használata
1.  A portál keresősávban, keresse meg a **Traffic Manager-profil** az előző szakaszban, majd kattintson az eredményeket a traffic manager-profilt a létrehozott név, amely a jelennek meg.
2. Kattintson a **áttekintése**.
3. A **Traffic Manager-profil** az újonnan létrehozott Traffic Manager-profil DNS-nevét jeleníti meg. Ez használhatja olyan ügyfelek (például úgy, hogy keresse meg webböngészővel) az beszerzése irányítja át a megfelelő végpont, határozza meg az Útválasztás típusa.  Földrajzi útválasztási Traffic Manager ellenőrzi, hogy a forrás IP-cím a bejövő kérelem, és meghatározza, hogy a régió, ahol származó. Ha adott régióban le van képezve egy végpontot, továbbítódik oda. A végpont sincs hozzárendelve az ebben a régióban, ha a Traffic Manager NODATA lekérdezés választ ad.

## <a name="next-steps"></a>További lépések

- További információ [Geographic forgalom-útválasztási módszer](traffic-manager-routing-methods.md#geographic).
- Megtudhatja, hogyan [Traffic Manager-beállítások tesztelésére](traffic-manager-testing-settings.md).

---
title: Többértékű forgalomútválasztás konfigurálása - Azure Traffic Manager
description: Ez a cikk bemutatja, hogyan konfigurálhatja a Traffic Manager forgalmat az A/AAAA végpontok.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: rohink
ms.openlocfilehash: daf7d09916d276130e337f7acea738228ee23707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938784"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>Többértékű útválasztási módszer konfigurálása a Traffic Manager ben

Ez a cikk a MultiValue traffic-routing metódus konfigurálását ismerteti. A **Többértékű** forgalom útválasztási módszer lehetővé teszi, hogy több kifogástalan állapotú végpontot ad vissza, és segít növelni az alkalmazás megbízhatóságát, mivel az ügyfelek több lehetőséget, hogy újra anélkül, hogy egy másik DNS-keresési. A többértékű útválasztás csak olyan profilokesetében engedélyezett, amelyek összes végpontja IPv4- vagy IPv6-címekkel van megadva. Ha egy lekérdezés érkezik ehhez a profilhoz, az összes kifogástalan végpontot a d. 

>[!NOTE]
> Az IPv4- vagy IPv6-címeket használó végpontok hozzáadása jelenleg csak **a Külső** típusú végpontok esetében támogatott, így a többértékű útválasztás is csak az ilyen végpontok esetében támogatott.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot a Traffic Manager-profilhoz.
1. Az Azure Portal bal oldali ablaktábláján válassza az **Erőforráscsoportok**lehetőséget.
2. Az **Erőforráscsoportok**csoportban a lap tetején válassza a **Hozzáadás**lehetőséget.
3. Az **Erőforráscsoport neve**mezőbe írja be a *myResourceGroupTM1*nevet. Az **Erőforráscsoport helye**területen válassza **az USA keleti része**lehetőséget, majd kattintson az **OK gombra.**

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása
Hozzon létre egy Traffic Manager-profilt, amely úgy irányítja a felhasználói forgalmat, hogy a legalacsonyabb késéssel lévő végpontra küldi őket.

1. A képernyő bal felső részén válassza az **Erőforrás** > létrehozása Hálózati**forgalomkezelő létrehozása** > csoport létrehozása : Create (Erőforrás**létrehozása: Forgalomkezelő** > **létrehozása) lehetőséget.**
2. A **Traffic Manager-profil létrehozása párbeszédpanelen**írja be vagy jelölje ki a következő adatokat, fogadja el a fennmaradó beállítások alapértelmezett beállításait, majd válassza a **Létrehozás lehetőséget:**
    
    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Név                   | Ennek a névnek egyedinek kell lennie a trafficmanager.net zónában, és a trafficmanager.net DNS-nevet eredményezi, amellyel elérhető a Traffic Manager-profil.                                   |
    | Útválasztási metódus          | Válassza a **Többértékű** útválasztási módszert.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza a *myResourceGroupTM1 lehetőséget.* |
    | Hely                | Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a globálisan üzembe helyezendő Traffic Manager-profilra.                              |
   |        |           | 
  
   ![Traffic Manager-profil létrehozása](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Adjon hozzá két IP-címet külső végpontként az előző lépésben létrehozott MultiValue Traffic Manager-profilhoz.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére, majd válassza ki a profilt a megjelenített eredmények között.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában kattintson a **Végpontok**, majd a **Hozzáadás** elemre.
3. Írja be vagy jelölje ki a következő adatokat, fogadja el a fennmaradó beállítások alapértelmezett beállításait, majd kattintson az **OK gombra:**

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Külső végpont                                   |
    | Név           | myEndpoint1                                        |
    | Teljesen minősített tartománynév (FQDN) vagy IP           | Írja be annak a végpontnak a nyilvános IP-címét, amelyet hozzá szeretne adni ehhez a Traffic Manager-profilhoz.                         |
    |        |           |

4. Ismételje meg a 2. *myEndpoint2* **Fully qualified domain name (FQDN) or IP**
5. Miután mindkét végpontot hozzáadta, azok megjelennek a **Traffic Manager-profil** panelen, **Online** figyelési állapottal.

   ![Traffic Manager-végpont hozzáadása](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>További lépések

- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ az [elsődleges útválasztási metódusról](traffic-manager-configure-priority-routing-method.md).
- További információ a [teljesítmény-útválasztási módszerről](traffic-manager-configure-performance-routing-method.md)
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).



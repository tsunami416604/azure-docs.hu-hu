---
title: Többértékű forgalom-útválasztási módszer konfigurálása az Azure Traffic Managerben |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogy A/AAAA-végpontokra irányuló forgalom irányítása a Traffic Manager konfigurálása.
services: traffic-manager
documentationcenter: ''
author: KumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: kumud
ms.openlocfilehash: d4a51551fb0f9067929df1fdf074e7286938004b
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54187705"
---
# <a name="configure-multivalue-routing-method-in-traffic-manager"></a>A Traffic Manager többértékű útválasztási mód konfigurálása

Ez a cikk ismerteti a többértékű forgalom-útválasztási módszer konfigurálása. A **típushoz** adatforgalom esetén használt útválasztási módszer lehetővé teszi, hogy több kifogástalan állapotú végpontok adja vissza, és segít az alkalmazás megbízhatóságának növelése, mivel az ügyfelek számára anélkül, hogy egy másik DNS-címkeresés tennie kellene újra több lehetősége van. Többértékű útválasztás csak az összes azok végpontjait, a megadott IPv4 vagy IPv6-címekkel rendelkező profilok engedélyezve van. Ehhez a profilhoz fogadásakor egy lekérdezést, kifogástalan állapotú végpontok összes vannak alapján visszaadott megadott konfigurálható maximális visszatérési száma. 

>[!NOTE]
> Végpontok hozzáadása jelenleg IPv4 vagy IPv6-címeket használ, csak a támogatott típusú végpontok **külső** ezért többértékű útválasztást is támogatott csak az ilyen végpontok.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba 

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy erőforráscsoportot a Traffic Manager-profil számára.
1. Az Azure portal bal oldali panelén válassza **erőforráscsoportok**.
2. A **erőforráscsoportok**, az oldal felső részén válassza ki a **Hozzáadás**.
3. A **erőforráscsoport-név**, adjon meg egy nevet *myResourceGroupTM1*. A **erőforráscsoport helye**válassza **USA keleti Régiójában**, majd válassza ki **OK**.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása
Hozzon létre egy Traffic Manager-profilt, amely irányítja a felhasználói adatforgalmat, elküldheti a legkisebb késés a végponthoz.

1. A képernyő bal felső részén válassza az **Erőforrás létrehozása** > **Hálózat** > **Traffic Manager-profil** > **Létrehozás** elemet.
2. A **Traffic Manager-profil létrehozása**, adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, és válassza **létrehozás**:
    
    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Name (Név)                   | Ennek a névnek egyedinek kell lennie a trafficmanager.net zónában, és a trafficmanager.net DNS-nevet eredményezi, amellyel elérhető a Traffic Manager-profil.                                   |
    | Útválasztási metódus          | Válassza ki a **típushoz** esetén használt útválasztási módszer.                                       |
    | Előfizetés            | Válassza ki előfizetését.                          |
    | Erőforráscsoport          | Válassza ki *myResourceGroupTM1*. |
    | Hely                | Ez a beállítás az erőforráscsoport helyére vonatkozik, és nincs hatással a globálisan üzembe helyezendő Traffic Manager-profilra.                              |
   |        |           | 
  
  ![Traffic Manager-profil létrehozása](./media/traffic-manager-multivalue-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása

Két IP-címet, külső végpontok hozzáadása típushoz Traffic Manager-profilt, amely az előző lépésben létrehozott.

1. A portál keresősávjában keressen rá az előző szakaszban létrehozott Traffic Manager-profil nevére, majd válassza ki a profilt a megjelenített eredmények között.
2. A **Traffic Manager-profil** panel **Beállítások** szakaszában kattintson a **Végpontok**, majd a **Hozzáadás** elemre.
3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Típus                    | Külső végpont                                   |
    | Name (Név)           | myEndpoint1                                        |
    | Teljesen minősített tartománynevét (FQDN) vagy IP-           | Írja be a végpont a Traffic Manager-profil hozzá kívánt nyilvános IP-címe                         |
    |        |           |

4. Ismételje meg a 2. és 3-nevű másik végpont hozzáadása *myEndpoint2*, a **teljesen minősített tartománynevét (FQDN) vagy IP-**, adja meg a nyilvános IP-címét a második végpontnak.
5.  Miután mindkét végpontot hozzáadta, azok megjelennek a **Traffic Manager-profil** panelen, **Online** figyelési állapottal.

   ![Traffic Manager-végpont hozzáadása](./media/traffic-manager-multivalue-routing-method/add-endpoint.png)
 
## <a name="next-steps"></a>További lépések

- További információ a [súlyozott forgalom útválasztási metódusról](traffic-manager-configure-weighted-routing-method.md).
- További információ az [elsődleges útválasztási metódusról](traffic-manager-configure-priority-routing-method.md).
- Tudjon meg többet [Teljesítménycentrikus útválasztási mód](traffic-manager-configure-performance-routing-method.md)
- További információ a [földrajzi útválasztási metódusról](traffic-manager-configure-geographic-routing-method.md).



---
title: Terheléselosztási és kimenő szabályok konfigurálása az Azure Portal használatával
titleSuffix: Azure Load Balancer
description: Ez a cikk bemutatja, hogyan konfigurálhatja a terheléselosztásés a kimenő szabályok standard terheléselosztó az Azure Portal használatával.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77590012"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Terheléselosztási és kimenő szabályok konfigurálása a Standard Load Balancer szolgáltatásban az Azure Portal használatával

Ez a cikk bemutatja, hogyan konfigurálhatja a kimenő szabályok standard terheléselosztó az Azure Portal használatával.  

A terheléselosztó erőforrás két előtér-végződést és a hozzájuk tartozó szabályokat tartalmaz. Van egy előtér a bejövő forgalom és egy másik előtér a kimenő forgalom.  

Minden előtér egy nyilvános IP-címre hivatkozik. Ebben a forgatókönyvben a bejövő forgalom nyilvános IP-címe eltér a kimenő forgalom címétől.   A terheléselosztási szabály csak bejövő terheléselosztást biztosít. A kimenő szabály szabályozza a kimenő hálózati cím fordítása (NAT) a virtuális gép.  

A forgatókönyv két háttér-készletet használ: egyet a bejövő és egyet a kimenő forgalomhoz. Ezek a készletek jól szemléltetik a képességeket, és rugalmasságot biztosítanak a forgatókönyvhöz.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a .

## <a name="create-a-load-balancer"></a>Terheléselosztó létrehozása

Ebben a szakaszban hozzon létre egy terheléselosztót, amely terheléselosztási virtuális gépek. Létrehozhat egy nyilvános terheléselosztót vagy egy belső terheléselosztót. Nyilvános terheléselosztó létrehozásakor hozzon létre egy új nyilvános IP-címet, amely a terheléselosztó előtétjeként van konfigurálva. Az előtér neve alapértelmezés szerint **LoadBalancerFrontEnd** lesz.

1. A képernyő bal felső részén válassza az **Erőforrás** > **létrehozása hálózati** > **terheléselosztó**lehetőséget.
2. A **Terheléselosztó létrehozása** lap **Alapjai** lapján adja meg vagy válassza a következő adatokat:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza **az Új létrehozása lehetőséget,** és írja be a **myResourceGroupSLB** parancsot a szövegmezőbe.|
    | Név                   | **myLoadBalancer**                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | SKU           | Válassza **a Normál** vagy az **Alapszintű**lehetőséget. A Microsoft az éles számítási feladatokhoz ajánlja a Standard ot. |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. Ha meglévő nyilvános IP-címvel szeretne rendelkezni, válassza **a Meglévő használata**lehetőséget.  A meglévő nyilvános IP-címnek **szabványos** termékváltozatnak kell lennie.  Az alapvető nyilvános IP-k nem kompatibilisek a **szabványos** termékváltozat terheléselosztójával.  |
    | Nyilvános IP-cím              | Írja be a **myPublicIP szöveget** a szövegmezőbe.|
    | Rendelkezésreállási zóna | Válassza **a Zónaredundáns** lehetőséget rugalmas terheléselosztó létrehozásához. Zónaszintű terheléselosztó létrehozásához válasszon ki egy adott zónát az 1, 2 vagy 3 |

3. Fogadja el az alapértelmezett beállításokat a konfiguráció többi részéhez.
4. Válassza **a Véleményezés + létrehozás lehetőséget**

    > [!IMPORTANT]
    > A rövid útmutató többi része feltételezi, hogy a **standard** termékváltozat a fenti termékváltozat-kiválasztási folyamat során kerül kiválasztásra.

5. A **Véleményezés + létrehozás** lapon válassza a **Létrehozás gombot.**   

    ![Standard Load Balancer létrehozása](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a terheléselosztó beállításait fogja konfigurálni egy háttércímkészlet és egy állapotminta számára, illetve egy elosztási szabályt fog megadni.

### <a name="create-a-backend-pool"></a>Háttérkészlet létrehozása

A háttércímkészlet a háttérkészletben lévő virtuális hálózati adapterek IP-címét tartalmazza. Hozza létre a **myBackendPool** háttércímkészletet, hogy virtuális gépeket tartalmazzon a terheléselosztáshoz az internetes forgalomhoz.

1. Válassza a Bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd válassza a **myLoadBalancer** elemet az erőforrások listájából.
2. A **Beállítások csoportban**válassza **a Háttérkészletek**lehetőséget, majd a **Hozzáadás**lehetőséget.
3. A **Háttérkészlet hozzáadása** lap névmezőbe írja be a **myBackendPool**nevet a háttérkészlet neveként, majd válassza **a Hozzáadás lehetőséget.**

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Az állapotminta az alkalmazás állapotának figyelésére szolgál. Az állapotminta hozzáadja vagy eltávolítja a virtuális gépeket a terheléselosztóból az állapot-ellenőrzésekre adott válaszuk alapján. Hozzon létre egy **myHealthProbe** nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza a Bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd válassza a **myLoadBalancer** elemet az erőforrások listájából.
2. A **Beállítások csoportban**válassza **az Állapotmintavételek**lehetőséget, majd a **Hozzáadás**lehetőséget.
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg **a myHealthProbe .** |
    | Protocol (Protokoll) | Válassza a **HTTP**lehetőséget. |
    | Port | Adja meg **a 80**.|
    | Intervallum | Adja meg a **15** értéket a mintavételi kísérletek közötti **időköz** másodpercben. |
    | Nem kifogástalan állapot küszöbértéke | Válassza ki a **2-t** a **nem megfelelő küszöbértékek** vagy egymást követő mintavételi hibák, amelyek a virtuális gép nem megfelelő állapotúnak kell lennie.|
    | | |
4. Válassza **az OK gombot.**

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. 

Ön határozza meg a következőket:
 - Előtér IP-konfigurációja a bejövő forgalomhoz.
 - A háttér-IP-készlet a forgalom fogadásához.
 - A szükséges forrás- és célport. 

A következő szakaszban létrehoz egy:
 - A terheléselosztó szabály **myHTTPRule** a 80-as port meghallgatásához.
 - Frontend **LoadBalancerFrontEnd**.
 - Háttércímkészlet: **a myBackEndPool** a 80-as portot is használja. 

1. Válassza a Bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd válassza a **myLoadBalancer** elemet az erőforrások listájából.
2. A **Beállítások csoportban**válassza a **Terheléselosztási szabályok**lehetőséget, majd a **Hozzáadás**lehetőséget.
3. Ezekkel az értékekkel konfigurálhatja a terheléselosztási szabályt:
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Írja be **a myHTTPRule értéket**. |
    | Protocol (Protokoll) | Válassza a **TCP**lehetőséget. |
    | Port | Adja meg **a 80**.|
    | Háttérablak | Adja meg **a 80**. |
    | A háttérkészlet | Válassza a **myBackendPool lehetőséget.**|
    | Állapotadat-mintavétel | Válassza a **myHealthProbe lehetőséget.** |
    | Implicit kimenő szabályok létrehozása | Válassza a **Nem** lehetőséget. Kimenő szabályokat hozunk létre egy későbbi szakaszban egy dedikált nyilvános IP használatával. |
4. Hagyja meg a többi alapértelmezést, majd kattintson az **OK gombra.**

## <a name="create-outbound-rule-configuration"></a>Kimenő szabály konfigurációjának létrehozása
A terheléselosztó kimenő szabályai konfigurálják a kimenő SNAT-t a háttérkészletben lévő virtuális gépekhez. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Kimenő nyilvános IP-cím és előtér létrehozása

1. Válassza a Bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd válassza a **myLoadBalancer** elemet az erőforrások listájából.

2. A **Beállítások csoportban**válassza **az Előtér IP-konfigurációja**lehetőséget, majd a **Hozzáadás**lehetőséget.

3. Ezekkel az értékekkel konfigurálhatja az előtér IP-konfigurációját a kimenő:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Írja be **a LoadBalancerFrontEndOutbound értéket.** |
    | IP-cím verziója | Válassza az **IPv4** lehetőséget. |
    | IP-típus | Válassza az **IP-címet.**|
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. A **Nyilvános IP-cím hozzáadása**mezőbe írja be a **myPublicIPOutbound értéket.**  Válassza **az OK gombot.** |

4. Válassza a **Hozzáadás** lehetőséget.

### <a name="create-an-outbound-backend-pool"></a>Kimenő háttérkészlet létrehozása

1. Válassza a Bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd válassza a **myLoadBalancer** elemet az erőforrások listájából.

2. A **Beállítások csoportban**válassza **a Háttérkészletek**lehetőséget, majd a **Hozzáadás**lehetőséget.

3. A **Háttérkészlet hozzáadása** lap névmezőbe írja be a **myBackendPoolOutbound**nevet a háttérkészlet neveként, majd válassza a **Hozzáadás lehetőséget.**

### <a name="create-outbound-rule"></a>Kimenő szabály létrehozása

1. Válassza a Bal oldali menü **Minden szolgáltatás lehetőséget,** válassza az **Összes erőforrás**lehetőséget, majd válassza a **myLoadBalancer** elemet az erőforrások listájából.

2. A **Beállítások csoportban**válassza **a Kimenő szabályok**lehetőséget, majd a **Hozzáadás**lehetőséget.

3. A kimenő szabályok konfigurálásához használja ezeket az értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg **a myOutboundRule (OutboundRule**) értéket. |
    | Előtérbeli IP-cím | Válassza **a LoadBalancerFrontEndOutbound lehetőséget.** |
    | Tétlen időelés (perc) | Mozgassa a csúszkát **15 percre.|
    | TCP alaphelyzetbe állítása | Válassza az **Engedélyezve** lehetőséget.|
    | A háttérkészlet | **A myBackendPoolOutbound** kiválasztása |
    | Portallokáció -> portfelosztása | **Válassza a Kimenő portok számának kézi kiválasztása lehetőséget.** |
    | Kimenő portok -> Választás | **Példányonkénti portok** kiválasztása |
    | Kimenő portok -> port példányonként | Adja meg **a 10.000**. |

4. Válassza a **Hozzáadás** lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Jelölje ki a terheléselosztót tartalmazó **myResourceGroupSLB** erőforráscsoportot, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

A cikk tartalma:
 - Létrehozott egy szabványos terheléselosztót.
 - Konfigurált a bejövő és a kimenő terheléselosztó forgalmi szabályai.
 - Konfigurált egy állapotminta a virtuális gépek a háttér-készletben. 

További információkért folytassa az [Azure Load Balancer oktatóanyagaival.](tutorial-load-balancer-standard-public-zone-redundant-portal.md)

---
title: Terheléselosztás és kimenő szabályok konfigurálása a Azure Portal használatával
titleSuffix: Azure Load Balancer
description: Ez a cikk bemutatja, hogyan konfigurálhatja a terheléselosztást és a kimenő szabályokat a standard Load Balancer a Azure Portal használatával.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/24/2019
ms.author: allensu
ms.openlocfilehash: b75f49155991bfc71f788ad88f166c0bec281841
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77590012"
---
# <a name="configure-load-balancing-and-outbound-rules-in-standard-load-balancer-by-using-the-azure-portal"></a>Terheléselosztás és kimenő szabályok konfigurálása standard Load Balancerban a Azure Portal használatával

Ez a cikk bemutatja, hogyan konfigurálhatja a standard Load Balancer kimenő szabályait a Azure Portal használatával.  

A terheléselosztó erőforrás két előtér-végpontot és a hozzájuk tartozó szabályokat tartalmazza. A bejövő forgalomhoz és a kimenő forgalomhoz egy másik előtér tartozik.  

Mindegyik előtér egy nyilvános IP-címet hivatkozik. Ebben az esetben a bejövő forgalom nyilvános IP-címe eltér a kimenő forgalom címétől.   A terheléselosztási szabály csak bejövő terheléselosztást biztosít. A Kimenő szabály a virtuális gép kimenő hálózati címfordítását (NAT) szabályozza.  

A forgatókönyv két háttér-készletet használ: egyet a bejövő forgalomhoz, egy pedig a kimenő forgalomhoz. Ezek a készletek szemléltetik a képességet, és rugalmasságot biztosítanak a forgatókönyvhöz.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-a-load-balancer"></a>Load Balancer létrehozása

Ebben a szakaszban létrehoz egy terheléselosztó, amely terheléselosztást végez a virtuális gépeken. Létrehozhat egy nyilvános terheléselosztó vagy egy belső terheléselosztó is. Nyilvános terheléselosztó létrehozásakor létre kell hoznia egy új nyilvános IP-címet, amely a terheléselosztó előtér-felületének megfelelően van konfigurálva. A frontend alapértelmezett neve **LoadBalancerFrontEnd** lesz.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **Load Balancer**lehetőséget.
2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg a következő információkat, vagy válassza ki az alábbi adatokat:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetést               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** lehetőséget, és írja be a **myresourcegroupslb erőforráscsoportban** szöveget a szövegmezőbe.|
    | Name (Név)                   | **myLoadBalancer**                                   |
    | Régió         | Válassza a **Nyugat-Európa** régiót.                                        |
    | Típus          | Válassza a **nyilvános**lehetőséget.                                        |
    | SKU           | Válassza a **standard** vagy az **alapszintű**lehetőséget. A Microsoft a standard szintű üzemi számítási feladatokhoz ajánlja. |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. Ha meglévő nyilvános IP-címmel szeretne használni, válassza a **meglévő használata**lehetőséget.  A meglévő nyilvános IP-nek **standard** SKU-nak kell lennie.  Az alapszintű nyilvános IP-címek nem kompatibilisek a **standard** SKU Load balancerrel.  |
    | Nyilvános IP-cím neve              | Írja be a **myPublicIP** szöveget a szövegmezőbe.|
    | Rendelkezésreállási zóna | Rugalmas Load Balancer létrehozásához válassza a **felesleges zóna** lehetőséget. A zóna Load Balancer létrehozásához válasszon ki egy 1, 2 vagy 3 zónát egy adott zónából |

3. Fogadja el az alapértelmezett beállításokat a többi konfigurációnál.
4. Válassza a **felülvizsgálat + létrehozás** lehetőséget

    > [!IMPORTANT]
    > A rövid útmutató további része azt feltételezi, hogy a **standard** SKU a fenti SKU kiválasztási folyamat során lett kiválasztva.

5. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget.   

    ![Standard Load Balancer létrehozása](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a terheléselosztó beállításait fogja konfigurálni egy háttércímkészlet és egy állapotminta számára, illetve egy elosztási szabályt fog megadni.

### <a name="create-a-backend-pool"></a>Háttérkészlet létrehozása

A háttér-címkészlet a háttér-készletben található virtuális hálózati adapterek IP-címeit tartalmazza. Hozza létre a háttér-címkészlet **myBackendPool** , hogy a virtuális gépeket a terheléselosztási internetes forgalom számára is tartalmazza.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások**területen válassza a **háttér-készletek**, majd a **Hozzáadás**lehetőséget.
3. A **háttérbeli készlet hozzáadása** lapon a név mezőbe írja be a **myBackendPool**nevet a háttérbeli készlet neveként, majd válassza a **Hozzáadás**lehetőséget.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

A rendszer az alkalmazás állapotának figyelésére használja az állapotot. Az állapot mintavétele a terheléselosztó által az állapot-ellenőrzésekre adott válasz alapján hozzáadja vagy eltávolítja a virtuális gépeket. Hozzon létre egy **myHealthProbe** nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások**területen válassza az **állapot**-mintavételek, majd a **Hozzáadás**lehetőséget.
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a **myHealthProbe**. |
    | Protokoll | Válassza a **http**lehetőséget. |
    | Port | Adja meg a **80**értéket.|
    | Intervallum | A mintavételi kísérletek közötti időtartam másodpercben megadott számú **intervallumában** adja meg a **15** értéket. |
    | Nem Kifogástalan állapot küszöbértéke | Válassza a **2** értéket a nem megfelelő **állapotú küszöbértékek** vagy egymást követő mintavételi hibák számának megadásához, amelyeknek a virtuális gép nem megfelelő állapotba kell kerülnie.|
    | | |
4. Kattintson az **OK** gombra.

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása
A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. 

A következőket adhatja meg:
 - A bejövő forgalom előtérbeli IP-konfigurációja.
 - A háttérbeli IP-készlet a forgalom fogadásához.
 - A szükséges forrás-és célport. 

A következő szakaszban létrehoz egy:
 - Terheléselosztó-szabály **: myhttprule** az 80-es port figyeléséhez.
 - Előtér- **LoadBalancerFrontEnd**.
 - A háttér-címkészlet a 80-as porton is **myBackEndPool** . 

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások**területen válassza a terheléselosztási **szabályok**lehetőséget, majd kattintson a **Hozzáadás**gombra.
3. Használja ezeket az értékeket a terheléselosztási szabály konfigurálásához:
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a **: myhttprule**. |
    | Protokoll | Válassza a **TCP**lehetőséget. |
    | Port | Adja meg a **80**értéket.|
    | Háttér-port | Adja meg a **80**értéket. |
    | Háttér-készlet | Válassza a **myBackendPool**lehetőséget.|
    | Állapotadat-mintavétel | Válassza a **myHealthProbe**lehetőséget. |
    | Implicit kimenő szabályok létrehozása | Kattintson a **Nem** gombra. Egy későbbi szakaszban hozunk létre kimenő szabályokat egy dedikált nyilvános IP-cím használatával. |
4. Hagyja meg a többi alapértelmezett beállítást, majd kattintson az **OK gombra**.

## <a name="create-outbound-rule-configuration"></a>Kimenő szabály konfigurációjának létrehozása
A terheléselosztó kimenő szabályai a háttér-készletben lévő virtuális gépek kimenő SNAT konfigurálása. 

### <a name="create-an-outbound-public-ip-address-and-frontend"></a>Kimenő nyilvános IP-cím és előtér létrehozása

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások**területen válassza ki a előtér **-IP-konfiguráció**elemet, majd kattintson a **Hozzáadás**gombra.

3. Az alábbi értékek használatával konfigurálhatja a kimenő előtér-IP-konfigurációt:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a **LoadBalancerFrontEndOutbound**. |
    | IP-cím verziója | Válassza az **IPv4**elemet. |
    | IP-cím típusa | Válassza ki az **IP-címet**.|
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. A **nyilvános IP-cím hozzáadása**mezőben adja meg a **myPublicIPOutbound**.  Kattintson az **OK** gombra. |

4. Válassza a **Hozzáadás** lehetőséget.

### <a name="create-an-outbound-backend-pool"></a>Kimenő háttérrendszer-készlet létrehozása

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások**területen válassza a **háttér-készletek**, majd a **Hozzáadás**lehetőséget.

3. A **háttérbeli készlet hozzáadása** lapon a név mezőbe írja be a **myBackendPoolOutbound**nevet a háttérbeli készlet neveként, majd válassza a **Hozzáadás**lehetőséget.

### <a name="create-outbound-rule"></a>Kimenő szabály létrehozása

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások**területen válassza a **Kimenő szabályok**elemet, majd kattintson a **Hozzáadás**gombra.

3. A kimenő szabályok konfigurálásához használja ezeket az értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Name (Név) | Adja meg a **myOutboundRule**. |
    | Előtérbeli IP-cím | Válassza a **LoadBalancerFrontEndOutbound**lehetőséget. |
    | Üresjárati időkorlát (perc) | Mozgassa a csúszkát * * 15 percre.|
    | TCP alaphelyzetbe állítása | Válassza az **Engedélyezve** lehetőséget.|
    | Háttér-készlet | **MyBackendPoolOutbound** kiválasztása |
    | Port kiosztása – > a portok kiosztása | Válassza ki **manuálisan a kimenő portok számának** kiválasztása lehetőséget. |
    | Kimenő portok – > választhat | Válassza ki a **portok száma példányt** |
    | Kimenő portok – > portok/példányok száma | Adja meg a **10 000**értéket. |

4. Válassza a **Hozzáadás** lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztót és az összes kapcsolódó erőforrást. Válassza ki azt az erőforráscsoportot, amely tartalmazza a terheléselosztó **myresourcegroupslb erőforráscsoportban** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

A cikk tartalma:
 - Létrehozta a standard Load balancert.
 - A bejövő és a kimenő terheléselosztó forgalmi szabályait egyaránt konfigurálta.
 - A háttér-készletben lévő virtuális gépekhez beállított állapot-mintavétel. 

További információért folytassa a [Azure Load Balancer oktatóanyagokkal](tutorial-load-balancer-standard-public-zone-redundant-portal.md).

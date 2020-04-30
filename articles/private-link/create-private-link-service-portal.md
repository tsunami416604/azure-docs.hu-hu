---
title: Rövid útmutató – privát hivatkozási szolgáltatás létrehozása a Azure Portal használatával
titlesuffix: Azure Private Link
description: Megtudhatja, hogyan hozhat létre privát hivatkozási szolgáltatást a rövid útmutató Azure Portal használatával
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: f21b440ee0e2c53d9824300e85b683629c1575da
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "78252552"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Rövid útmutató: privát link szolgáltatás létrehozása a Azure Portal használatával

Az Azure Private link Service a saját szolgáltatására hivatkozik, amelyet a privát kapcsolat kezel. Az Azure-standard Load Balancer mögött működő szolgáltatáshoz vagy erőforráshoz privát hivatkozásokat adhat hozzá. A szolgáltatás felhasználói saját virtuális hálózatokról is hozzáférhetnek. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy privát hivatkozási szolgáltatást a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-an-internal-load-balancer"></a>Hozzon létre egy belső terheléselosztót

Először hozzon létre egy virtuális hálózatot. Következő lépésként hozzon létre egy belső terheléselosztó szolgáltatást a privát kapcsolat szolgáltatással való használatra.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban egy új virtuális hálózatot hozhat létre. Emellett létrehozza azt az alhálózatot is, amely a privát kapcsolati szolgáltatáshoz hozzáférő terheléselosztó futtatására szolgál.

Ebben a szakaszban le kell cserélnie a következő paramétereket a lépésekben az alábbi információkkal:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforrás-csoport neve>**  | Myresourcegrouplb erőforráscsoportban |
| **\<virtuális hálózat neve>** | myVNet          |
| **\<régió neve>**          | USA 2. keleti régiója      |
| **\<IPv4 – címtartomány>**   | 10.3.0.0 \ 16          |
| **\<alhálózat – név>**          | myBackendSubnet        |
| **\<alhálózat – címtartomány>** | 10.3.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Standard terheléselosztó létrehozása

A portál használatával hozzon létre egy standard belső terheléselosztó. A megadott név és IP-cím automatikusan be van állítva a terheléselosztó előtér-címére.

1. A portál bal felső részén válassza az **erőforrás** > létrehozása**hálózatkezelés** > **Load Balancer**elemet.

1. A **Load Balancer létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | **Előfizetés**               | Válassza ki előfizetését.    |
    | **Erőforráscsoport**         | Válassza a **myresourcegrouplb erőforráscsoportban** lehetőséget a mezőben.|
    | **Név**                   | Adja meg a **myLoadBalancer**.                                   |
    | **Régió**         | Válassza az **USA 2. keleti régiója** lehetőséget.                                        |
    | **Típus**          | Válassza a **belső**lehetőséget.                                        |
    | **SKU**           | Válassza a **standard**lehetőséget.                          |
    | **Virtuális hálózat**           | Válassza a **myVNet**lehetőséget.                          |
    | **IP-cím hozzárendelése**              | Válassza a **statikus**lehetőséget.   |
    | **Magánhálózati IP-cím**|Adjon meg egy, a virtuális hálózat és az alhálózat címterület területén található címeket. Ilyen például a 10.3.0.7.  |

1. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás** elemet.

1. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás**lehetőséget.

### <a name="create-standard-load-balancer-resources"></a>Standard Load Balancer-erőforrások létrehozása

Ebben a szakaszban a terheléselosztó beállításait fogja konfigurálni egy háttércímkészlet és egy állapotminta számára, A terheléselosztó szabályait is megadhatja.

#### <a name="create-a-back-end-pool"></a>Háttérbeli készlet létrehozása

A háttér-címkészlet a terheléselosztó számára csatlakozó virtuális hálózati adapterek IP-címeit tartalmazza. Ez a készlet lehetővé teszi az erőforrások forgalmának elosztását. Hozza létre a **myBackendPool** nevű háttér-címkészletet a forgalmat terheléselosztási erőforrások belefoglalásához.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben.
1. Válassza az **összes erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
1. A **Beállítások** alatt válassza a **Háttérkészletek**, majd a **Hozzáadás** lehetőséget.
1. A **háttérbeli készlet hozzáadása** lapon adja meg a **myBackendPool** nevet a háttér-készlet neveként, majd válassza a **Hozzáadás**lehetőséget.

#### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Használjon állapot-mintavételt, hogy a terheléselosztó-figyelő erőforrás állapota meglegyen. Az állapot-ellenőrzésre adott erőforrás-válasz alapján az állapotfigyelő szolgáltatás dinamikusan hozzáadja vagy eltávolítja az erőforrásokat a terheléselosztó elforgatásával.

Állapot-mintavétel létrehozása az erőforrások állapotának figyeléséhez:

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **myLoadBalancer** lehetőséget az erőforrások listájából.

1. A **Beállítások** területn válassza az **Állapotminták**, majd a **Hozzáadás** lehetőséget.

1. Az **állapotfelmérés hozzáadása** lapon adja meg vagy válassza ki a következő értékeket:

   - **Név**: adja meg a **myHealthProbe**.
   - **Protokoll**: válassza a **TCP**lehetőséget.
   - **Port**: írja be a **80**értéket.
   - **Intervallum**: adja meg a **15**értéket. Ez az érték a mintavételi kísérletek között eltelt másodpercek száma.
   - Nem megfelelő **állapot küszöbértéke**: írja be a **2**értéket. Ez az érték azon egymást követő mintavételi hibák száma, amelyek a virtuális gép nem megfelelő állapotának számítása előtt történnek.

1. Kattintson az **OK** gombra.

#### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztó szabályai határozzák meg, hogy a rendszer hogyan ossza el a forgalmat az erőforrásokra. A szabály az alábbiakat határozza meg:

- A bejövő forgalom előtér-IP-konfigurációja.
- A háttérbeli IP-készlet a forgalom fogadására.
- A szükséges forrás és cél portok.

A **myLoadBalancerRule** nevű terheléselosztó-szabály a **LoadBalancerFrontEnd** előtérben figyeli a 80-es portot. A szabály a 80-as porton keresztül továbbítja a hálózati forgalmat az **myBackendPool** -háttérbeli címkészlet számára.

Terheléselosztó-szabály létrehozása:

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **myLoadBalancer** lehetőséget az erőforrások listájából.

1. A **Beállítások**területen válassza a **terheléselosztási szabályok**elemet, majd kattintson a **Hozzáadás**gombra.

1. A **terheléselosztási szabály hozzáadása** lapon adja meg vagy válassza ki a következő értékeket, ha még nem jelennek meg:

   - **Név**: adja meg a **myLoadBalancerRule**.
   - Előtérbeli **IP-cím:** Adja meg a **LoadBalancerFrontEnd**.
   - **Protokoll**: válassza a **TCP**lehetőséget.
   - **Port**: írja be a **80**értéket.
   - **Háttér-port**: írja be a **80**értéket.
   - **Háttér-készlet**: válassza a **myBackendPool**lehetőséget.
   - **Állapot**-mintavétel: válassza a **myHealthProbe**lehetőséget. 

1. Kattintson az **OK** gombra.

## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása

Ebben a szakaszban egy standard Load Balancer mögötti privát kapcsolati szolgáltatást hoz létre.

1. A Azure Portal oldal bal felső részén válassza az **erőforrás** > **Networking** > létrehozása magánhálózat**privát kapcsolati központ (előzetes verzió)** lehetőséget. A portál keresőmező használatával is megkeresheti a privát hivatkozást.

1. A **Private link Centerben –** > **a saját szolgáltatás közzététele, hogy mások is csatlakozhatnak**, válassza az **Indítás**lehetőséget.

1. A **Private link Service létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás           | Érték                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | Projekt részletei:  |                                                                              |
    | **Előfizetés**      | Válassza ki előfizetését.                                                     |
    | **Erőforráscsoport**    | Válassza a **myresourcegrouplb erőforráscsoportban**lehetőséget.                                                    |
    | Példány részletei: |                                                                              |
    | **Név**              | Adja meg a **myPrivateLinkService**. |
    | **Régió**            | Válassza az **USA 2. keleti régiója** lehetőséget.                                                        |

1. Válassza a **Next (tovább): kimenő beállítások**lehetőséget.

1. A **privát kapcsolati szolgáltatás létrehozása – kimenő beállítások**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás                           | Érték                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load Balancer**                     | Válassza a **myLoadBalancer**lehetőséget.                                                           |
    | **Load Balancer előtér IP-címe** | Válassza ki a **myLoadBalancer**ELŐTÉR-IP-címét.                                |
    | **Forrás NAT virtuális hálózata**        | Válassza a **myVNet**lehetőséget.                                                                   |
    | **Forrás NAT-alhálózat**                 | Válassza a **myBackendSubnet**lehetőséget.                                                          |
    | **A TCP-proxy v2 engedélyezése**               | Válassza az **Igen** vagy a **nem** lehetőséget attól függően, hogy az alkalmazás TCP-proxy v2-fejlécet vár-e. |
    | **Magánhálózati IP-cím beállításai**       | Konfigurálja a kiosztási módszert és az IP-címet az egyes NAT-IP-címekhez.                  |

1. Válassza a Next (tovább) lehetőséget **: hozzáférés a biztonsághoz**.

1. **A privát kapcsolati szolgáltatás létrehozása – hozzáférési biztonság**területen válassza a **láthatóság**lehetőséget, majd válassza a **szerepköralapú hozzáférés-vezérlés**lehetőséget.
  
1. Válassza a **Next (tovább) lehetőséget: címkék** > **felülvizsgálat + létrehozás** vagy válassza a lap tetején található **felülvizsgálat + létrehozás** lapot.

1. Tekintse át az adatokat, majd válassza a **Létrehozás**lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a Private link szolgáltatással, törölje az erőforráscsoportot az ebben a rövid útmutatóban használt erőforrások törléséhez.

1. Írja be a **myresourcegrouplb erőforráscsoportban** kifejezést a portál tetején található keresőmezőbe, és válassza a **myresourcegrouplb erőforráscsoportban** elemet a keresési eredmények közül.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Az **írja be az ERŐFORRÁSCSOPORT nevét mezőbe írja be a következőt**: **myResourceGroup**.
1. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy belső Azure Load balancert és egy privát kapcsolati szolgáltatást. Azt is megtudhatja, hogyan [hozhat létre egy privát végpontot a Azure Portal használatával](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).

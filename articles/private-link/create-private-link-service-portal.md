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
ms.openlocfilehash: 4dd27d3fb4321c2eecf222136376bfd4b49790bb
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027775"
---
# <a name="quickstart-create-a-private-link-service-using-the-azure-portal"></a>Rövid útmutató: privát link szolgáltatás létrehozása a Azure Portal használatával

Az Azure Private link Service a saját szolgáltatására vonatkozik, amelyet az Azure privát kapcsolata működtet. A Azure Load Balancer mögött működő szolgáltatás vagy erőforrás engedélyezhető a magánjellegű kapcsolatokhoz való hozzáféréshez. A szolgáltatás felhasználói saját virtuális hálózatok érhetik el a szolgáltatást. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy privát hivatkozási szolgáltatást a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.


## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-an-internal-load-balancer"></a>Belső terheléselosztó létrehozása

Először hozzon létre egy virtuális hálózatot, majd egy belső terheléselosztó használatát az Azure Private link szolgáltatással.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy Virtual Network és egy alhálózatot, amely a privát kapcsolati szolgáltatás eléréséhez használt terheléselosztó üzemeltetésére szolgál.


1. A portál bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **virtuális hálózat**lehetőséget.
   
1. A **virtuális hálózat létrehozása** panelen írja be vagy válassza ki a következő értékeket:
   
   - **Név**: írja be a **MyVNet**nevet.
   - **ResourceGroup**: válassza az **új létrehozása**elemet, majd írja be a **Myresourcegrouplb erőforráscsoportban**, majd kattintson **az OK gombra**. 
   - **Alhálózat** > **neve**: írja be a következőt: **MyBackendSubnet**.
   
1. Kattintson a **Létrehozás** gombra.

   ![Virtuális hálózat létrehozása](../load-balancer/media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-a-standard-load-balancer"></a>Standard Load Balancer létrehozása

Hozzon létre egy standard belső terheléselosztó-t a portál használatával. A létrehozott név és IP-cím automatikusan a terheléselosztó előlapján lesz konfigurálva.

1. A Portal bal felső sarkában kattintson az **Erőforrás létrehozása** > **Hálózat** > **Terheléselosztó** elemre.
   
2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg vagy válassza ki a következő adatokat, fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás**:

    | Beállítás                 | Value (Díj)                                              |
    | ---                     | ---                                                |
    | Előfizetést               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | A legördülő listában válassza a *myresourcegrouplb erőforráscsoportban* lehetőséget.|
    | Name (Név)                   | *myLoadBalancer*                                   |
    | Region (Régió)         | Válassza az **USA 2. keleti régiója** lehetőséget.                                        |
    | Type (Típus)          | Válassza a **belső**lehetőséget.                                        |
    | SKU (Cikkszám)           | Válassza a **standard**lehetőséget.                          |
    | Virtuális hálózat           | Válassza a *MyVNet*lehetőséget.                          |    
    | IP-cím hozzárendelése              | Válassza a **statikus**lehetőséget.   |
    | Magánhálózati IP-cím|Írjon be egy olyan címeket, amely a virtuális hálózat és az alhálózat címterület területén található, például *10.3.0.7*.  |

3. A **felülvizsgálat + létrehozás** lapon kattintson a **Létrehozás**gombra. 
   

### <a name="create-standard-load-balancer-resources"></a>Standard Load Balancer-erőforrások létrehozása

Ebben a szakaszban a terheléselosztó beállításait konfigurálja egy háttérbeli címkészlet és egy állapot-mintavétel számára, és meg kell adnia a terheléselosztó szabályait.

#### <a name="create-a-backend-pool"></a>Háttérbeli készlet létrehozása

Az erőforrásokra irányuló forgalom elosztásához a háttérbeli címkészlet tartalmazza a Load Balancerhoz csatlakozó virtuális hálózati adapterek IP-címeit. Hozza létre a háttér-címkészlet *myBackendPool* , hogy tartalmazza a terheléselosztási forgalomhoz szükséges erőforrásokat.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás**lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.
2. A **Beállítások**területen válassza a **háttér-készletek**, majd a **Hozzáadás**lehetőséget.
3. A **háttérbeli készlet hozzáadása** lapon a név mezőbe írja be a *myBackendPool*nevet a háttérbeli készlet neveként, majd válassza a **Hozzáadás**lehetőséget.



#### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Ha engedélyezni szeretné a terheléselosztó számára az erőforrás állapotának figyelését, az állapot-mintavételt kell használnia. Az állapot-mintavétel dinamikusan hozzáadja vagy eltávolítja az erőforrásokat a terheléselosztó forgása alapján az állapot-ellenőrzésekre adott válasz alapján. 

**Állapot-mintavétel létrehozása az erőforrások állapotának figyeléséhez:**

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások** területn válassza az **Állapotminták**, majd a **Hozzáadás** lehetőséget.
   
1. Az **állapotfelmérés hozzáadása** lapon írja be vagy válassza ki a következő értékeket:
   
   - **Név**: írja be a **MyHealthProbe**nevet.
   - **Protokoll**: legördülő menüből válassza a **TCP**lehetőséget. 
   - **Port**: Type **80**. 
   - **Időköz**: Type **15**. Az intervallum a mintavételi kísérletek között eltelt másodpercek száma.
   - Nem megfelelő **állapot küszöbértéke**: Type **2**. Ez az érték azon egymást követő mintavételi hibák száma, amelyek a virtuális gép nem megfelelő állapotának tekintendők.
   
1. Kattintson az **OK** gombra.

#### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztó szabályai határozzák meg, hogy a rendszer hogyan ossza el a forgalmat az erőforrásokra. A szabály meghatározza a bejövő forgalom előtér-IP-konfigurációját, a háttérbeli IP-készletet a forgalom fogadásához, valamint a szükséges forrás-és célport eléréséhez. 

A **MyLoadBalancerRule** nevű terheléselosztó-szabály a 80-es portot figyeli az előtér- **LoadBalancerFrontEnd**. A szabály a 80-es porton is továbbítja a hálózati forgalmat a háttérbeli címkészlet **MyBackendPool**. 

**A terheléselosztó szabály létrehozása:**

1. A bal oldali menüben válassza az **összes erőforrás** lehetőséget, majd válassza a **MyLoadBalancer** lehetőséget az erőforrások listájából.
   
1. A **Beállítások**területen válassza a **terheléselosztási szabályok**elemet, majd kattintson a **Hozzáadás**gombra.
   
1. A **terheléselosztási szabály hozzáadása** lapon írja be vagy válassza ki a következő értékeket, ha még nem létezik:
   
   - **Név**: írja be a **MyLoadBalancerRule**nevet.
   - Előtérbeli **IP-cím:** Írja be a **LoadBalancerFrontEnd** típust, ha nincs jelen.
   - **Protokoll**: válassza a **TCP**lehetőséget.
   - **Port**: Type **80**.
   - **Háttér-port**: Type **80**.
   - **Háttér-készlet**: válassza a **MyBackendPool**lehetőséget.
   - **Állapot**-mintavétel: válassza a **MyHealthProbe**lehetőséget. 
   
1. Kattintson az **OK** gombra.
   
## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása

Ebben a szakaszban egy, a standard Load Balancer mögötti privát kapcsolati szolgáltatást fog létrehozni.

1. A Azure Portal képernyő bal felső részén válassza az **erőforrás létrehozása** > **hálózatkezelés** > **Private link Center (előzetes verzió)** lehetőséget. A privát hivatkozás a portálon keresztül is kereshető.

2. A **Private link Centerben – áttekintés**a **"saját szolgáltatás közzététele, hogy mások is csatlakozhatnak"** lehetőségnél válassza az indítás lehetőséget.

3. A **Private link Service létrehozása – alapismeretek**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás           | Value (Díj)                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | **Projekt részletei:**  |                                                                              |
    | Előfizetést      | Válassza ki előfizetését.                                                     |
    | Erőforráscsoport    | *Myresourcegrouplb erőforráscsoportban* kiválasztása                                                     |
    | **PÉLDÁNY RÉSZLETEI:** |                                                                              |
    | Name (Név)              | *MyPrivateLinkService* megadása |
    | Region (Régió)            | Válassza az *USA 2. keleti* régióját                                                         |

4. Válassza a **Next (tovább): kimenő beállítások**lehetőséget.

5. A **privát kapcsolati szolgáltatás létrehozása – kimenő beállítások**lapon adja meg vagy válassza ki az alábbi adatokat:


    | Beállítás                           | Value (Díj)                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | Terheléselosztó                     | *MyLoadBalancer* kiválasztása                                                           |
    | Load Balancer előtér IP-címe | Válassza ki a MyLoadBalancer felületének IP-címét                                |
    | Forrás NAT virtuális hálózata        | *MyVNET* kiválasztása                                                                   |
    | Forrás NAT-alhálózat                 | *MyBackendSubnet* kiválasztása                                                          |
    | A TCP-proxy v2 engedélyezése               | Válassza az igen/nem beállítást attól függően, hogy az alkalmazás a TCP-proxy v2 fejlécét várja-e |
    | Magánhálózati IP-cím beállításai       | A kiosztási módszer és az IP-cím konfigurálása az egyes NAT-IP-címekhez                  |

6. Válassza a Next (tovább) lehetőséget **: hozzáférés a biztonsághoz**.

7. A **privát kapcsolat szolgáltatás létrehozása – hozzáférési biztonság**területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás                                     | Value (Díj)                                                             |
    |---------------------------------------------|-------------------------------------------------------------------|
    | Láthatóság                                  | *Csak szerepköralapú hozzáférés-vezérlés* kiválasztása         |
  
8. Válassza a **Next (tovább**) gombot, majd a lap tetején a **felülvizsgálat + létrehozás lapot** .

9. Tekintse át az adatokat, és válassza a **Létrehozás**lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása 
Ha végzett a Private link szolgáltatással, törölje az erőforráscsoportot az ebben a rövid útmutatóban használt erőforrások törléséhez:

1. Adja meg a *myresourcegrouplb erőforráscsoportban* a portál tetején található **keresőmezőbe** , és válassza a *myresourcegrouplb erőforráscsoportban* lehetőséget a keresési eredmények közül. 
2. Válassza az **Erőforráscsoport törlése** elemet. 
3. Írja be **a myResourceGroup nevet az erőforráscsoport neveként** , majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy belső Azure Load balancert és egy privát kapcsolati szolgáltatást. További információ a privát végpontok létrehozásáról: [privát végpontok létrehozása Azure Portal használatával](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal).

---
title: Rövid útmutató – Privát kapcsolatszolgáltatás létrehozása az Azure Portal használatával
titlesuffix: Azure Private Link
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre privát kapcsolati szolgáltatást az Azure Portal használatával
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: f21b440ee0e2c53d9824300e85b683629c1575da
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78252552"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Rövid útmutató: Privát kapcsolatszolgáltatás létrehozása az Azure Portal használatával

Az Azure Private Link szolgáltatás a saját szolgáltatás, amely a Private Link által kezelt. Privát kapcsolat hozzáférést adhat az Azure Standard Load Balancer mögött működő szolgáltatáshoz vagy erőforráshoz. A szolgáltatás fogyasztói saját virtuális hálózatukból férhetnek hozzá a szolgáltatáshoz. Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre egy privát kapcsolat szolgáltatást az Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-an-internal-load-balancer"></a>Hozzon létre egy belső terheléselosztót

Először hozzon létre egy virtuális hálózatot. Ezután hozzon létre egy belső terheléselosztót a Private Link szolgáltatással való használatra.

## <a name="virtual-network-and-parameters"></a>Virtuális hálózat és paraméterek

Ebben a szakaszban egy új virtuális hálózatot hozhat létre. Az alhálózatot is létrehozhatja a privát kapcsolat szolgáltatáshoz hozzáférő terheléselosztó üzemeltetéséhez.

Ebben a szakaszban a következő paramétereket kell lecserélnie az alábbi információkra:

| Paraméter                   | Érték                |
|-----------------------------|----------------------|
| **\<erőforráscsoport-név>**  | myResourceGroupLB |
| **\<virtuális hálózat neve>** | myVNet          |
| **\<régiónév>**          | USA 2. keleti régiója      |
| **\<IPv4-címtér>**   | 10.3.0.0\16          |
| **\<alhálózat-név>**          | myBackendSubnet        |
| **\<alhálózati címtartomány>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Standard terheléselosztó létrehozása

A portál segítségével hozzon létre egy szabványos belső terheléselosztót. A megadott név és IP-cím automatikusan konfigurálva lesz a terheléselosztó előtérként.

1. A portál bal felső részén válassza az **Erőforrás** > **hálózati** > **terheléselosztó**létrehozása lehetőséget.

1. A **Terheléselosztó létrehozása** lap **Alapjai** lapján adja meg vagy válassza ki a következő adatokat:

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | **Előfizetés**               | Válassza ki előfizetését.    |
    | **Erőforráscsoport**         | Válassza ki a **myResourceGroupLB** elemet a mezőből.|
    | **Név**                   | Adja meg **a myLoadBalancer .**                                   |
    | **Régió**         | Válassza az **USA 2. keleti régiója** lehetőséget.                                        |
    | **Típus**          | Válassza a **Belső**lehetőséget.                                        |
    | **Sku**           | Válassza a **Normál**lehetőséget.                          |
    | **Virtuális hálózat**           | Válassza a **myVNet**lehetőséget.                          |
    | **IP-cím hozzárendelése**              | Válassza a **Statikus**lehetőséget.   |
    | **Privát IP-cím**|Adja meg a virtuális hálózat és az alhálózat címterében található címet. Egy példa a 10.3.0.7.  |

1. Fogadja el a fennmaradó beállítások alapértelmezéseit, majd válassza a **Véleményezés + létrehozás lehetőséget**

1. A **Véleményezés + létrehozás** lapon válassza a **Létrehozás gombot.**

### <a name="create-standard-load-balancer-resources"></a>Szabványos terheléselosztó-erőforrások létrehozása

Ebben a szakaszban a terheléselosztó beállításait fogja konfigurálni egy háttércímkészlet és egy állapotminta számára, A terheléselosztó szabályait is megadhatja.

#### <a name="create-a-back-end-pool"></a>Háttérkészlet létrehozása

A háttércímkészlet a terheléselosztóhoz csatlakoztatott virtuális hálózati adapterEK IP-címeit tartalmazza. Ez a készlet lehetővé teszi a forgalom elosztását az erőforrások között. Hozza létre a **myBackendPool** nevű háttércímkészletet, amely tartalmazza a terheléselosztási forgalmat tartalmazó erőforrásokat.

1. Válassza a bal szélső menü **Minden szolgáltatás lehetőséget.**
1. Válassza az **Összes erőforrás**lehetőséget, majd válassza a **myLoadBalancer** elemet az erőforráslistából.
1. A **Beállítások** alatt válassza a **Háttérkészletek**, majd a **Hozzáadás** lehetőséget.
1. A **Háttérkészlet hozzáadása** lapon írja be a **myBackendPool** nevet a háttérkészlet neveként, majd válassza a **Hozzáadás lehetőséget.**

#### <a name="create-a-health-probe"></a>Állapotminta létrehozása

Az állapotminta segítségével a terheléselosztó figyelheti az erőforrás állapotát. Az állapotellenőrzésekre adott erőforrás-válasz alapján az állapotminta dinamikusan hozzáadja vagy eltávolítja az erőforrásokat a terheléselosztó elforgatásából.

Az erőforrások állapotának figyelésére állapotminta létrehozása:

1. Válassza a bal szélső menü **Összes erőforrás elemét,** majd válassza a **myLoadBalancer** elemet az erőforráslistából.

1. A **Beállítások** területn válassza az **Állapotminták**, majd a **Hozzáadás** lehetőséget.

1. Az **Állapotminta hozzáadása** lapon adja meg vagy válassza ki a következő értékeket:

   - **Név**: Írja be **a myHealthProbe értéket**.
   - **Protokoll**: Válassza a **TCP**lehetőséget.
   - **Port**: Írja be a **80**értéket.
   - **Időköz**: Adja meg **a 15**értéket . Ez az érték a mintavételi kísérletek közötti másodpercek száma.
   - **Nem kifogástalan küszöbérték**: Írja be a **2**értéket. Ez az érték a virtuális gép nem megfelelő állapotúnak minősülő, egymást követő mintavételi hibák száma.

1. Válassza **az OK gombot.**

#### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztó szabály határozza meg, hogyan forgalmat kell elosztani az erőforrások között. A szabály a következőket határozza meg:

- A bejövő forgalom előtér-IP-konfigurációja.
- A forgalom fogadásához a háttér-IP-készlet.
- A szükséges forrás- és célportok.

A **myLoadBalancerRule** nevű terheléselosztó szabály a **LoadBalancerFrontEnd** előtér 80-as portját figyeli. A szabály hálózati forgalmat küld a **myBackendPool** back-end címkészletugyanazon a porton 80.

Terheléselosztó szabály létrehozása:

1. Válassza a bal szélső menü **Összes erőforrás elemét,** majd válassza a **myLoadBalancer** elemet az erőforráslistából.

1. A **Beállítások csoportban**válassza a **Terheléselosztási szabályok**lehetőséget, majd a **Hozzáadás**lehetőséget.

1. A **Terheléselosztási szabály hozzáadása** lapon adja meg vagy jelölje ki a következő értékeket, ha azok még nincsenek jelen:

   - **Név**: Írja be **a myLoadBalancerRule értéket.**
   - **Előtér IP-címe:** Adja meg **a LoadBalancerFrontEnd értéket.**
   - **Protokoll**: Válassza a **TCP**lehetőséget.
   - **Port**: Írja be a **80**értéket.
   - **Háttérport:** Írja be a **80-at.**
   - **Háttérkészlet:** Válassza a **myBackendPool elemet.**
   - **Egészségügyi szonda**: Válassza a **myHealthProbe**lehetőséget. 

1. Válassza **az OK gombot.**

## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása

Ebben a szakaszban hozzon létre egy Privát kapcsolat szolgáltatást egy szabványos terheléselosztó mögött.

1. Az Azure Portal lap bal felső részén válassza az > Erőforrás létrehozása**hálózati privát** > **kapcsolati központ (előzetes verzió)** **lehetőséget.** A portál keresőmezőjével privát hivatkozást is kereshet.

1. A **Privát kapcsolati központban - Áttekintés:** > **Saját szolgáltatás ának feltéve, hogy mások is csatlakozhatnak,** válassza a **Start**lehetőséget.

1. A **Privát kapcsolat szolgáltatás létrehozása - Alapjai**csoportban adja meg vagy jelölje ki ezt az információt:

    | Beállítás           | Érték                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | A projekt részletei:  |                                                                              |
    | **Előfizetés**      | Válassza ki előfizetését.                                                     |
    | **Erőforráscsoport**    | Válassza a **myResourceGroupLB**lehetőséget.                                                    |
    | Példány adatai: |                                                                              |
    | **Név**              | Írja be **a myPrivateLinkService .** |
    | **Régió**            | Válassza az **USA 2. keleti régiója** lehetőséget.                                                        |

1. Válassza a **Tovább: Kimenő beállítások lehetőséget.**

1. A **Privát kapcsolatszolgáltatás létrehozása - Kimenő beállítások csoportban**adja meg vagy jelölje ki ezt az információt:

    | Beállítás                           | Érték                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Terheléselosztó**                     | Válassza a **myLoadBalancer lehetőséget.**                                                           |
    | **Terheléselosztó előtér ÉNEK ip-címe** | Válassza ki a **myLoadBalancer**előtér-IP-címét.                                |
    | **Forrás NAT virtuális hálózat**        | Válassza a **myVNet**lehetőséget.                                                                   |
    | **Forrás Hálózati attaszalhálózata**                 | Válassza a **myBackendSubnet**lehetőséget.                                                          |
    | **TCP-proxy engedélyezése v2**               | Válassza az **IGEN** vagy **a NEM** lehetőséget attól függően, hogy az alkalmazás tcp proxy v2 fejlécet vár-e. |
    | **Privát IP-címbeállítások**       | Konfigurálja a foglalási módszert és az IP-címet minden NAT IP-címhez.                  |

1. Válassza a **Tovább: Hozzáférés biztonsága lehetőséget.**

1. A **Privát kapcsolatszolgáltatás létrehozása - Hozzáférés biztonsága**csoportban válassza a **Láthatóság**lehetőséget, majd válassza a **Csak szerepköralapú hozzáférés-vezérlés lehetőséget.**
  
1. Vagy válassza a **Tovább: Címkék** > **áttekintése + létrehozása** vagy az oldal tetején található Véleményezés + **létrehozás** fület.

1. Tekintse át az adatokat, és válassza a **Létrehozás gombot.**

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte a Privát kapcsolat szolgáltatás használatát, törölje az erőforráscsoportot a rövid útmutatóban használt erőforrások karbantartásához.

1. Írja be a **myResourceGroupLB** kifejezést a portál tetején lévő keresőmezőbe, és válassza ki a **myResourceGroupLB** elemet a keresési eredmények közül.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. Az **ERŐFORRÁSCSOPORT NEVÉNEK BEÍRÁSA**mezőbe írja be a **myResourceGroup**értéket.
1. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy belső Azure-terheléselosztót és egy Private Link szolgáltatást. Azt is megtudhatja, hogyan [hozhat létre privát végpontot az Azure Portal használatával.](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)
